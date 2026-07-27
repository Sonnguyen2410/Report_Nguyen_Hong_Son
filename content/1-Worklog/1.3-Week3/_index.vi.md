---
title: "Worklog Tuần 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

## Chủ đề: Khởi tạo máy chủ Amazon EC2, cấu hình S3 Bucket và triển khai API Presigned URL

### 1. Mục tiêu tuần 3
* Thành thạo quy trình tạo, cấu hình bảo mật và quản lý máy chủ ảo Amazon EC2 trong mô hình VPC.
* Hiểu sâu bản chất lưu trữ đối tượng của Amazon S3 và cơ chế bảo mật cấp quyền tạm thời bằng Presigned URL.
* Triển khai kéo (pull) Docker Container từ Amazon ECR lên máy chủ EC2 và cho chạy ứng dụng Backend thực tế.
* Xây dựng bộ API cốt lõi quản lý Authentication, Khóa học (Courses), Bài học (Lessons) và module xử lý Presigned URL tích hợp AWS SDK v3.

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Máy chủ ảo Amazon EC2 (Elastic Compute Cloud) & Hạ tầng Mạng
* **Khởi tạo và Cấu hình EC2 Instance:**
  * **Tìm hiểu cách chọn Hệ điều hành (AMI):** Sử dụng Ubuntu Server 22.04 LTS 64-bit (x86).
  * **Lựa chọn Instance Type:** `t2.micro` hoặc `t3.micro` (đảm bảo nằm trong gói AWS Free Tier).
  * **Khái niệm Elastic IP (EIP):** Địa chỉ IP tĩnh công khai cố định. Hiểu rõ tại sao nếu không dùng EIP, mỗi lần Stop/Start instance thì Public IP sẽ bị đổi làm gián đoạn kết nối của ứng dụng.
* **Cấu hình Security Group (EC2 Stateful Firewall):**
  * **Inbound Rule 1:** Type SSH, Port 22, Source My IP (Chỉ cho phép máy tính cá nhân của admin SSH vào server).
  * **Inbound Rule 2:** Type Custom TCP, Port 5000, Source 0.0.0.0/0 (Cho phép client gọi Backend RESTful API).
  * **Inbound Rule 3:** Type HTTP/HTTPS, Port 80/443, Source 0.0.0.0/0 (Chuẩn bị cho domain/web traffic).
* **Kết nối và Phân quyền Máy chủ:**
  * Quản lý cặp khóa bảo mật Key Pair (`.pem` file) và thiết lập phân quyền file trên Linux (`chmod 400 key.pem`).
  * **Gán IAM Role cho EC2 (EC2 Instance Profile):** Cấp chính sách `AmazonEC2ContainerRegistryReadOnly` để EC2 tự động có quyền vĩnh viễn lấy auth token và pull image từ Amazon ECR mà không cần lưu AWS Access Key cứng trên server.
  * Cài đặt môi trường runtime trên EC2: Docker Engine, Docker Compose và AWS CLI v2.

#### B. Dịch vụ Amazon S3 & Cơ chế Presigned URL
* **Cấu hình Amazon S3 Bucket:**
  * Tạo Bucket đặt tên `ai-learning-platform-vhd` tại Region `ap-southeast-1`.
  * Giữ nguyên thiết lập **Block All Public Access = ON** (Đảm bảo tất cả đối tượng trong bucket bị ẩn hoàn toàn với Internet).
  * Thiết lập **CORS Configuration (Cross-Origin Resource Sharing)** trên S3 Console để cho phép Frontend (ví dụ `http://localhost:5173` hoặc CloudFront URL) gửi trực tiếp HTTP PUT request chứa file lên S3:
    ```json
    [
      {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "PUT", "POST", "HEAD"],
        "AllowedOrigins": ["*"],
        "ExposeHeaders": ["ETag"]
      }
    ]
    ```
* **Chuyên sâu về AWS S3 Presigned URL:**
  * **Vấn đề bài toán:** Nếu để bucket Private, người dùng thông thường không thể tải file/xem video bài học. Nếu mở Public, bất kỳ ai cũng có thể kéo trộm dữ liệu hoặc làm bùng nổ chi phí băng thông S3.
  * **Giải pháp Presigned URL:** Backend sử dụng Secret Credentials signing ra một liên kết ngắn hạn.
  * **Luồng Presigned Upload URL:**
    1. Frontend gửi request kèm tên file, loại file (`mimeType`) lên Backend `/api/files/upload-url`.
    2. Backend dùng `@aws-sdk/s3-request-presigner` tạo ra đường link dạng `https://ai-learning-platform-vhd.s3.ap-southeast-1.amazonaws.com/uploads/...&X-Amz-Signature=...`.
    3. Frontend dùng lệnh `fetch(presignedUrl, { method: 'PUT', body: file })` để đẩy file thẳng lên AWS S3.
  * **Luồng Presigned Download URL:**
    1. Khi học viên mở bài học, Backend sinh link GET Presigned URL có thời gian sống 900 giây (15 phút).
    2. Video / Tài liệu hiển thị mượt mà trên trình duyệt và tự động hết hiệu lực sau 15 phút để chống xem lén.

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Triển khai Máy chủ EC2 và Chạy Docker Container Backend:**
  * Khởi tạo EC2 Instance, gán Elastic IP và Security Group theo thiết kế.
  * Kết nối SSH vào máy chủ: `ssh -i "learnsphere-key.pem" ubuntu@<Elastic_IP>`
  * Cài đặt Docker trên EC2:
    ```bash
    sudo apt-get update
    sudo apt-get install -y docker.io
    sudo systemctl start docker
    sudo systemctl enable docker
    sudo usermod -aG docker ubuntu
    ```
  * Thực hiện đăng nhập ECR và kéo Docker Container:
    ```bash
    aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com
    docker pull <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest
    docker run -d -p 5000:5000 --name backend-api --env-file .env <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest
    ```

* **Xây dựng Module `file.service.js` trong Backend:**
  * Cài đặt các thư viện AWS SDK v3: `npm install @aws-sdk/client-s3 @aws-sdk/s3-request-presigner`
  * Khởi tạo S3 Client với cấu hình Region `ap-southeast-1`:
    ```javascript
    import { S3Client, PutObjectCommand, GetObjectCommand } from "@aws-sdk/client-s3";
    import { getSignedUrl } from "@aws-sdk/s3-request-presigner";

    const s3Client = new S3Client({
      region: process.env.AWS_REGION,
      credentials: {
        accessKeyId: process.env.AWS_ACCESS_KEY_ID,
        secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
      },
    });
    ```
  * Viết hàm tạo Upload URL với cấu hình hết hạn 300s (`S3_UPLOAD_URL_EXPIRES_IN=300`).
  * Viết hàm tạo Download URL với cấu hình hết hạn 900s (`S3_DOWNLOAD_URL_EXPIRES_IN=900`).

* **Phát triển và Đóng gói bộ RESTful API Cốt lõi:**
  * **Authentication Controller (`auth.service.js` & `auth.controller.js`):**
    * Implement API `POST /api/auth/register`: Kiểm tra email trùng lặp, mã hóa mật khẩu bằng `bcryptjs` (salt 10 rounds), lưu User mới vào MongoDB Atlas, trả về Token JWT.
    * Implement API `POST /api/auth/login`: So sánh mật khẩu mã hóa, tạo chuỗi Token JWT chứa `userId` và `role` với thời gian hết hạn 7 ngày.
    * Implement API `GET /api/auth/me`: Middleware `auth.middleware.js` giải mã Header `Authorization: Bearer <token>` để trả về thông tin người dùng hiện tại.
  * **Course Controller (`course.service.js`):**
    * Implement API `GET /api/courses`: Tìm kiếm, lọc khóa học theo danh mục và trạng thái (Published).
    * Implement API `POST /api/courses`: Dành cho Instructor/Admin khởi tạo thông tin khóa học mới.
  * **Lesson Controller (`lesson.service.js`):**
    * Implement API `POST /api/lessons`: Thêm bài học mới vào khóa học, lưu thông tin `s3_key` của tài liệu/video.
    * Implement API `GET /api/lessons/:id`: Trả về chi tiết bài học kèm Presigned URL tải/xem video an toàn.

---

### 4. Kết quả đạt được (Deliverables)
* Máy chủ EC2 Ubuntu hoạt động ổn định trên AWS với Elastic IP cố định, Docker Container Backend lắng nghe trên cổng 5000.
* S3 Bucket `ai-learning-platform-vhd` được thiết lập Private hoàn toàn kèm cấu hình CORS chuẩn.
* Module `file.service.js` được lập trình bằng AWS SDK v3 sinh ra các Presigned URLs chính xác và bảo mật.
* Bộ API Auth (Register, Login, Me), Course và Lesson đã được test thành công qua Postman trên môi trường thật `http://<Elastic_IP>:5000/api` và sẵn sàng kết nối với Frontend.
