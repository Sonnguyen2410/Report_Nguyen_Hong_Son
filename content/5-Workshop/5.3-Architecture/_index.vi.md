---
title: "Mô tả kiến trúc"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

Nội dung dưới đây phân tích chuyên sâu toàn bộ kiến trúc hạ tầng đám mây của hệ thống LearnSphere trên AWS, đi sâu vào từng quy trình xử lý dữ liệu, thông số kỹ thuật chi tiết của từng thành phần, cơ chế mã hóa mạng, thiết kế phân quyền bảo mật và nguyên lý hoạt động của quy trình vận hành tự động.

![LearnSphere AWS Architecture](/images/LEARNSHPHERE.drawio.png)

---

### 1. Phân tích Luồng Dữ liệu và Tương tác Hệ thống (Data Flow Mechanics)

Kiến trúc hạ tầng của LearnSphere được thiết kế để xử lý mượt mà 4 luồng dữ liệu chính trong hệ thống:

#### Luồng 1: Tải Giao diện Frontend và Định tuyến Ứng dụng trang đơn (React SPA Routing Flow)
* **Khởi tạo kết nối:** Người dùng truy cập tên miền HTTPS của CloudFront từ trình duyệt. CloudFront tiếp nhận kết nối qua chứng chỉ bảo mật TLS/SSL chuẩn.
* **Truy xuất tệp tĩnh:** Yêu cầu (Request) khớp với quy tắc mặc định (Default Behavior `/`). CloudFront gửi mã ký bảo mật qua cơ chế Origin Access Control (OAC) tới S3 Frontend Bucket để lấy các tệp giao diện (`index.html`, tệp JavaScript, CSS, hình ảnh giao diện). S3 Bucket kiểm tra mã ký OAC hợp lệ và trả về nội dung cho CloudFront để truyền tới trình duyệt.
* **Xử lý F5 / Client-Side Sub-routes:** Khi học viên thực hiện tải lại trang (F5) hoặc truy cập trực tiếp vào các tuyến đường phụ như `/profile` hay `/courses/123`, CloudFront Function đính kèm tại sự kiện Viewer Request sẽ can thiệp. Kịch bản này kiểm tra nếu đường dẫn không chứa dấu chấm phần mở rộng tệp, nó sẽ tự động sửa đổi URI nội bộ thành `/index.html`. Trình duyệt vẫn nhận về mã nguồn React và React Router sẽ đảm nhận việc hiển thị đúng giao diện tương ứng mà không xuất hiện lỗi 404 Not Found từ S3.

#### Luồng 2: Thực thi Yêu cầu REST API Backend (Backend API Execution Flow)
* **Chuyển tiếp yêu cầu:** Khi ứng dụng React thực hiện gọi các API nghiệp vụ (như đăng nhập, lấy danh sách khóa học, nộp bài thi), request bắt đầu bằng đường dẫn `/api/*`. CloudFront khớp với Behavior ưu tiên `/api/*` và chuyển tiếp thẳng gói tin về địa chỉ máy chủ EC2.
* **Bỏ qua Cache và Bảo toàn Header:** Behavior này cấu hình vô hiệu hóa bộ nhớ đệm (`CachingDisabled`) và áp dụng chính sách `AllViewerExceptHostHeader` để giữ nguyên toàn bộ các HTTP Headers gốc từ trình duyệt (bao gồm chuỗi mã hóa xác thực JWT trong Header Authorization).
* **Tiếp nhận tại EC2:** Gói tin đi qua lớp tường lửa Security Group của EC2 (nơi chỉ chấp nhận lưu lượng đến từ CloudFront Prefix List) và đi vào cổng 5000. Docker Container chứa ứng dụng Express.js tiếp nhận request, giải mã token JWT, xử lý logic nghiệp vụ.
* **Truy vấn Database:** Backend sử dụng Mongoose ODM gửi câu lệnh truy vấn tới MongoDB Atlas Cluster thông qua kết nối SRV an toàn. Dữ liệu kết quả phản hồi được Backend đóng gói dưới dạng định dạng JSON và trả ngược về cho trình duyệt qua kênh CloudFront.

#### Luồng 3: Tải lên và Tải xuống Tệp Truyền thông dung lượng lớn (Media Presigned URL Flow)
* **Luồng Upload (Tải lên Video/PDF/Image):**
  1. Giáo viên chọn tệp video bài giảng trên giao diện React. Frontend gửi một request ngắn tới Backend yêu cầu xin quyền tải lên.
  2. Backend nhận request, kiểm tra quyền của Giáo viên, sau đó dùng AWS SDK (sử dụng Temporary Credentials từ IAM Role của EC2) để sinh ra một đường dẫn Presigned PUT URL có thời hạn hiệu lực đúng 5 phút.
  3. Backend trả Presigned PUT URL về cho Frontend.
  4. Frontend sử dụng Presigned URL này để thực hiện tải tệp trực tiếp từ trình duyệt lên S3 Media Bucket mà không cần thông qua máy chủ EC2. Đối với tệp dung lượng lớn, Frontend thực hiện tải lên theo các phần (S3 Multipart Upload). Trình duyệt đọc thông số ETag được Expose từ cấu hình CORS của S3 để xác nhận hoàn tất quá trình ghép tệp.
* **Luồng Download (Xem Video/Đọc Tài liệu):**
  1. Học viên bấm xem bài học. Frontend gửi request tới Backend xin đường dẫn xem video.
  2. Backend kiểm tra học viên đã đăng ký khóa học hay chưa. Nếu hợp lệ, Backend sinh ra một Presigned GET URL có thời hạn hiệu lực 15 phút và trả về cho Frontend.
  3. Trình duyệt dùng Presigned GET URL để luồng (stream) dữ liệu video trực tiếp từ S3 Media Bucket về phát trên trình duyệt. S3 Bucket tuyệt đối không mở công khai cho toàn Internet.

#### Luồng 4: Tự động hóa Triển khai và Hoàn tác (CI/CD Deployment & Rollback Flow)
* **Xác thực OIDC & Build Image:** Kỹ sư đẩy mã nguồn mới lên nhánh `main` trên GitHub. GitHub Actions kích hoạt workflow, sử dụng OIDC xác thực với AWS STS để nhận quyền ngắn hạn. Workflow biên dịch ứng dụng, đóng gói Docker Image Backend với thẻ (tag) là mã băm Git Commit SHA và đẩy lên Amazon ECR.
* **Thực thi lệnh triển khai qua SSM:** GitHub Actions sử dụng lệnh AWS SSM RunCommand gửi bản kịch bản triển khai mã hóa tới SSM Agent trên EC2.
* **Triển khai Candidate Container:** Kịch bản trên EC2 kéo Image mới từ ECR, khởi chạy một Container chạy thử tên là `candidate` trên cổng tạm thời 5001.
* **Kiểm tra Sức khỏe (Health Check Retry):** Kịch bản lặp lại câu lệnh gọi endpoint `/health/ready` tới cổng 5001 tối đa 24 lần (mỗi lần cách nhau 5 giây).
* **Trường hợp thành công:** Nếu endpoint phản hồi status code 200 OK và báo kết nối Database thành công, kịch bản tiến hành đổi tên container chính cũ thành `rollback`, sau đó mở container mới trên cổng chính 5000 và xóa container thử nghiệm. Quá trình chuyển đổi diễn ra trong vài miligiây.
* **Trường hợp thất bại:** Nếu sau 24 lần kiểm tra mà container 5001 không phản hồi, kịch bản tự động dừng và xóa container `candidate`, giữ nguyên container cũ đang chạy trên cổng 5000, đồng thời báo lỗi về GitHub Actions để hủy pipeline.

---

### 2. Thông số Kỹ thuật và Thiết kế Chi tiết từng Thành phần

#### Amazon CloudFront (Cấu hình Chi tiết Distribution)
* **Tên miền phân phối:** Được cấp tự động dạng `d2onzy56n3iw1w.cloudfront.net`.
* **Cấu hình Nguồn Origin 1 (S3 Frontend):**
  * Origin Domain: Trỏ tới S3 Bucket `learnsphere-fe-575620421319`.
  * Origin Access: Origin Access Control (OAC) với chế độ Ký yêu cầu (Sign requests enabled).
* **Cấu hình Nguồn Origin 2 (EC2 Backend):**
  * Origin Domain: Địa chỉ IPv4 Public/DNS của máy chủ EC2.
  * Protocol Policy: HTTP Only (Do kết nối nội bộ giữa CloudFront và EC2 trong hạ tầng AWS).
  * HTTP Port: `5000`.
* **Chính sách Default Cache Behavior (`/*`):**
  * Target Origin: S3 Frontend Origin.
  * Viewer Protocol Policy: Redirect HTTP to HTTPS.
  * Allowed HTTP Methods: GET, HEAD.
  * Cache Policy: `CachingOptimized` (Tối ưu hóa đệm cho các tệp đã được băm tên như JavaScript, CSS).
  * Function Associations: Gắn CloudFront Function cho sự kiện Viewer Request để điều hướng đường dẫn SPA.
* **Chính sách Cache Behavior API (`/api/*`):**
  * Target Origin: EC2 Backend Origin.
  * Viewer Protocol Policy: Redirect HTTP to HTTPS.
  * Allowed HTTP Methods: GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE.
  * Cache Policy: `CachingDisabled` (Tuyệt đối không lưu đệm dữ liệu API).
  * Origin Request Policy: `AllViewerExceptHostHeader` (Chuyển tiếp tất cả Header ngoại trừ Host Header).

#### Amazon S3 (Chi tiết Cấu hình 2 Buckets)
* **Bucket Frontend (`learnsphere-fe-575620421319`):**
  * Region: `ap-southeast-1`.
  * Cấu hình Block Public Access: Bật toàn bộ (Block all public access = ON).
  * S3 Static Website Hosting: Tắt (Disabled).
  * Bucket Policy: Chỉ cho phép duy nhất Service Principal `cloudfront.amazonaws.com` thực hiện hành động `s3:GetObject` với điều kiện `ArnLike AWS:SourceArn` phải khớp với mã ARN của CloudFront Distribution đại diện.
* **Bucket Media (`learnsphere-media-575620421319`):**
  * Region: `ap-southeast-1`.
  * Cấu hình Block Public Access: Bật toàn bộ (Block all public access = ON).
  * Cấu hình CORS (Cross-Origin Resource Sharing):
    * AllowedOrigins: `http://localhost:5173`, `https://d2onzy56n3iw1w.cloudfront.net`.
    * AllowedMethods: GET, PUT, HEAD.
    * AllowedHeaders: `*` (Cho phép tất cả Header).
    * ExposeHeaders: `ETag` (Bắt buộc phải công khai ETag để trình duyệt hoàn tất quá trình tải tệp lớn Multipart Upload).
    * MaxAgeSeconds: `3600` (Lưu đệm thông số CORS trong 1 giờ).

#### Amazon ECR (Chi tiết Cấu hình Repository)
* **Tên Repository:** `learnsphere-be`.
* **Loại Repository:** Private.
* **Image Scan Settings:** Enable Scan on Push (Tự động chạy bộ quét rà soát lỗ hổng bảo mật dựa trên cơ sở dữ liệu CVE mỗi khi có image mới được push lên).
* **Lifecycle Policy Rules:** Tạo quy tắc sắp xếp theo số lượng image có chứa thẻ (tagged images), thiết lập giới hạn lưu giữ tối đa 10 image gần nhất. Các image cũ hơn sẽ tự động bị xóa bỏ để tối ưu dung lượng lưu trữ.

#### Amazon EC2 & Môi trường Runtime Docker
* **Thông số Máy chủ:**
  * Instance ID: `i-008c48e6c120b2978`.
  * HĐH: Amazon Linux 2023 64-bit (x86).
  * Kích thước: Instance type `t3.small` (2 vCPU, 2.0 GiB Physical Memory).
  * Vị trí: Default VPC, Public Subnet, Auto-assign Public IP = Enabled.
* **Cấu hình Bộ nhớ RAM Swap:**
  * Khởi tạo tệp bộ nhớ ảo Swap dung lượng 2.0 GB tại đường dẫn `/swapfile`.
  * Thiết lập phân quyền siết chặt `600` (chỉ root có quyền đọc/ghi).
  * Đăng ký cấu hình tự động kích hoạt vào tệp `/etc/fstab` hệ thống.
  * **Tác dụng:** Cung cấp tổng cộng ~4.0 GB dung lượng bộ nhớ khả dụng (2GB RAM vật lý + 2GB Swap), giúp quá trình đọc hiểu tài liệu PDF, phân tích dữ liệu hình ảnh OCR và xử lý đa tiến trình của Node.js không bao giờ bị dừng đột ngột do lỗi tràn bộ nhớ.
* **Thiết lập An ninh Mạng (Security Group Rules):**
  * Inbound Rules: Custom TCP, Port: `5000`, Source: AWS Managed Prefix List `com.amazonaws.global.cloudfront.origin-facing` (Danh sách tập hợp tất cả dải IP đầu ra của CloudFront CDN trên toàn cầu).
  * Chặn 100% kết nối cổng 22 (SSH), 80 (HTTP), 443 (HTTPS) từ địa chỉ `0.0.0.0/0`.
* **Bối Cảnh Bảo mật Docker Container:**
  * Đóng gói nền tảng: Linux Alpine bản siêu nhẹ (`node:24-alpine`).
  * Phân quyền chạy: Tạo nhóm người dùng hệ thống `nodejs` (GID 1001) và người dùng `nodejs` (UID 1001). Cấp quyền sở hữu thư mục ứng dụng cho user này.
  * Chỉ thị thực thi: Chạy ứng dụng dưới quyền user non-root `nodejs`, triệt tiêu rủi ro chiếm quyền điều khiển máy chủ nếu container bị khai thác lỗ hổng.
  * Chỉ thị Healthcheck: Cấu hình lệnh `wget` kiểm tra định kỳ 30 giây tới cổng nội bộ 5000 tại endpoint `/health/ready`.

#### Cơ sở dữ liệu MongoDB Atlas
* **Mô hình triển khai:** Replica Set 3 nút (Primary - Secondary - Secondary) đảm bảo tính sẵn sàng cao.
* **Phương thức kết nối:** Kết nối qua giao thức `mongodb+srv://` tự động cân bằng tải và phát hiện nút chính.
* **Bảo mật truy cập mạng:** Khai báo chính xác địa chỉ IP công cộng của máy chủ EC2 vào mục Network Access / IP Access List trên bảng điều khiển MongoDB Atlas.

#### Kiến trúc Phân quyền IAM & Zero Static Credentials
* **OIDC Identity Provider:**
  * Provider URL: `https://token.actions.githubusercontent.com`
  * Audience: `sts.amazonaws.com`
* **IAM Role 1 (`LearnSphereGitHubDeployRole`):**
  * Trust Policy: Cho phép hành động `sts:AssumeRoleWithWebIdentity` với điều kiện ràng buộc khắt khe: `token.actions.githubusercontent.com:aud` phải bằng `sts.amazonaws.com` và `token.actions.githubusercontent.com:sub` phải trùng khớp chính xác với chuỗi đại diện cho nhánh `main` của repository `LearnSphere`.
  * Permissions Policy: Cấp quyền ECR GetAuthorizationToken, ECR BatchCheckLayerAvailability, ECR PutImage, S3 Sync cho Frontend Bucket, CloudFront CreateInvalidation và SSM SendCommand tới duy nhất Instance ID của EC2.
* **IAM Role 2 (`LearnSphereEc2Role`):**
  * Trust Policy: Cho phép dịch vụ `ec2.amazonaws.com` đảm nhận Role.
  * Attached Policies:
    * `AmazonSSMManagedInstanceCore`: Cho phép SSM Agent duy trì kết nối điều khiển an toàn với dịch vụ AWS Systems Manager.
    * `AmazonEC2ContainerRegistryReadOnly`: Cho phép EC2 kéo Docker Image từ ECR.
    * `S3 Media Custom Policy`: Cấp quyền ListBucket, PutObject, GetObject, DeleteObject, AbortMultipartUpload trên Media Bucket.
    * `CloudWatch Logs Custom Policy`: Cấp quyền CreateLogStream và PutLogEvents trên Log Group `/learnsphere/backend`.
    * `Bedrock Custom Policy`: Cấp quyền InvokeModel và InvokeModelWithResponseStream đối với các mô hình AI.

#### Hệ thống Giám sát CloudWatch & Cảnh báo SNS
* **Gom Log tập trung (CloudWatch Logs):**
  * Log Group Name: `/learnsphere/backend`.
  * Retention Policy: Lưu trữ log trong 30 ngày.
  * Nguồn dữ liệu: Nhận toàn bộ nhật ký hoạt động từ container Docker thông qua cấu hình driver `awslogs`.
* **Cảnh báo 1 (CloudWatch Alarm - EC2 High CPU):**
  * Alarm Name: `LearnSphere-EC2-HighCPU`.
  * Metric Name: `CPUUtilization` (Namespace: `AWS/EC2`).
  * Statistic: Average, Period: 5 minutes.
  * Condition: Greater than 80%.
  * Datapoints to Alarm: 2 out of 2 (Kích hoạt cảnh báo khi CPU vượt 80% liên tục trong 2 chu kỳ, tức 10 phút, nhằm loại bỏ các cảnh báo giả do CPU spike tức thời).
  * Notification Action: Gửi thông báo tới SNS Topic `LearnSphere-Alerts`.
* **Cảnh báo 2 (CloudWatch Alarm - EC2 Status Check Failed):**
  * Alarm Name: `LearnSphere-EC2-StatusCheckFailed`.
  * Metric Name: `StatusCheckFailed` (Namespace: `AWS/EC2`).
  * Statistic: Maximum, Period: 1 minute.
  * Condition: Greater than or equal to 1.
  * Datapoints to Alarm: 1 out of 1 (Phát hiện và phát cảnh báo ngay lập tức trong vòng 60 giây khi máy chủ hỏng hóc hoặc mất kết nối).
  * Notification Action: Gửi thông báo tới SNS Topic `LearnSphere-Alerts`.
* **Amazon SNS Topic & Email Subscription:**
  * Topic Name: `LearnSphere-Alerts` (Type: Standard).
  * Subscription Protocol: Email.
  * Trạng thái xác thực: Confirmed (Người quản trị đã mở email xác nhận từ AWS Notifications).

---

### 3. Bảng So sánh Kiến trúc Truyền thống và Kiến trúc Modern Cloud của LearnSphere

| Tiêu chí | Kiến trúc Truyền thống (Legacy) | Kiến trúc Modern Cloud LearnSphere |
| --- | --- | --- |
| **Cơ chế Xác thực Credential** | Lưu Access Key / Secret Key tĩnh dài hạn trong file `.env` hoặc GitHub Secrets. Rủi ro lộ chìa khóa cao. | **100% Zero Static Credentials.** Dùng OIDC cho CI/CD và IAM Instance Profile (IMDSv2) cho EC2. Tự động hết hạn sau vài giờ. |
| **Cơ chế Quản trị Máy chủ** | Mở cổng SSH 22 cho Internet, quản trị bằng User/Password hoặc SSH Key. Dễ bị tấn công Brute-force. | **Đóng hoàn toàn cổng 22.** Điều khiển máy chủ từ xa 100% qua kênh truyền mã hóa AWS Systems Manager (SSM) Session Manager. |
| **Cấu trúc Tên miền & Phân phối** | Frontend chạy tên miền A, Backend chạy tên miền B hoặc gọi IP EC2 cổng 5000. Dễ dính lỗi CORS và Mixed Content. | **Single Domain HTTPS duy nhất qua CloudFront CDN.** Phân phối S3 FE qua OAC và chuyển tiếp API `/api/*` về EC2. Triệt tiêu hoàn toàn lỗi CORS. |
| **Quy trình Triển khai (Deploy)** | SSH thủ công vào server kéo code `git pull` và restart app. Gây gián đoạn (Downtime) và dễ sập nếu code mới bị lỗi. | **Tự động hóa CI/CD qua GitHub Actions**, đóng gói Docker Multi-stage, test thử trên Candidate Container (Port 5001), Health Check thành công mới tráo đổi cổng 5000. Tự động Rollback nếu lỗi. |
| **Quản lý Tệp đa phương tiện** | Tải tệp qua Backend trung gian gây quá tải máy chủ API, hoặc mở Public S3 Bucket cho phép mọi người truy cập tự do. | **S3 Media Bucket đóng Private 100%.** Trình duyệt tải lên và tải xuống trực tiếp qua Presigned URL có thời hạn do Backend cấp, giảm tải tối đa cho máy chủ EC2. |
