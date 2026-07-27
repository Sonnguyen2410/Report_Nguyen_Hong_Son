---
title : "Các bước chuẩn bị chi tiết"
date : 2024-01-01 
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

Để đảm bảo quá trình thực hành triển khai hạ tầng cho ứng dụng LearnSphere lên đám mây AWS diễn ra mượt mà, không gặp gián đoạn hay sự cố kỹ thuật, người thực hiện cần chuẩn bị và rà soát kỹ lưỡng toàn bộ hệ thống tài khoản, phân quyền, công cụ local và cấu trúc mã nguồn theo các yêu cầu chi tiết dưới đây.

### 1. Chi tiết về Tài khoản đám mây và Phân quyền truy cập

**Tài khoản đám mây AWS (AWS Account & IAM Permissions):**
* **Trạng thái tài khoản:** Tài khoản AWS phải ở trạng thái hoạt động bình thường, không bị nợ cước hay bị giới hạn dịch vụ. Ưu tiên các tài khoản còn trong thời gian 12 tháng AWS Free Tier để tối ưu chi phí thử nghiệm.
* **Vùng triển khai (Region):** Khuyến nghị chọn cố định vùng Singapore (`ap-southeast-1`) cho tất cả dịch vụ để giảm độ trễ (latency) về Việt Nam và đảm bảo tính đồng bộ dữ liệu giữa EC2, S3, ECR và CloudWatch.
* **Tài khoản truy cập:** Khuyên dùng một tài khoản IAM User được gán nhóm quyền Quản trị viên (`AdministratorAccess`) hoặc gán đầy đủ các nhóm quyền chuyên biệt bên dưới, thay vì dùng trực tiếp tài khoản AWS Root Account:
  * **IAM:** Cấp quyền tạo OIDC Provider, IAM Role và gắn Instance Profile.
  * **EC2:** Cấp quyền khởi tạo Instance, Security Group, Elastic IP và tích hợp SSM.
  * **S3:** Cấp quyền tạo Bucket, cấu hình CORS, Bucket Policy và OAC.
  * **ECR:** Cấp quyền tạo Repository, đẩy/kéo Docker Image và cấu hình Lifecycle Policy.
  * **CloudFront:** Cấp quyền tạo Distribution, OAC, CloudFront Function và Create Invalidation.
  * **CloudWatch & SNS:** Cấp quyền tạo Log Group, Alarms, SNS Topic và Email Subscription.
  * **Systems Manager (SSM):** Cấp quyền chạy lệnh điều khiển xa qua RunCommand.

**Tài khoản lưu trữ mã nguồn GitHub (Repository & CI/CD Access):**
* Đã tạo một Repository cá nhân hoặc tổ chức mang tên `LearnSphere` trên GitHub.
* Nhánh mã nguồn chính (Default Branch) được thống nhất đặt tên là `main`.
* Có quyền truy cập vào mục **Settings** -> **Secrets and variables** -> **Actions** của Repository để khai báo các thông số cấu hình bảo mật phục vụ cho quy trình tự động hóa triển khai.
* **Cơ chế kết nối OIDC:** GitHub Actions sẽ tự động phát sinh một web identity token tới URL `token.actions.githubusercontent.com` với định danh audience `sts.amazonaws.com` để AWS xác thực.

**Tài khoản Cơ sở dữ liệu Cloud MongoDB Atlas:**
* Đã khởi tạo một dự án (Project) và một Cluster cơ sở dữ liệu trên MongoDB Atlas (dùng bản miễn phí M0 Sandbox hoặc gói M2/M5 Shared).
* **Vị trí Cluster:** Chọn vị trí địa lý của Cluster gần với Region `ap-southeast-1` (ưu tiên AWS Singapore cluster) để tốc độ truy vấn từ EC2 đến Database đạt hiệu năng cao nhất.
* **Tài khoản truy cập Database (Database User):** Đã tạo một User riêng phục vụ môi trường Production, được gán quyền đọc/ghi (`readWriteAnyDatabase`) trên cơ sở dữ liệu `learnsphere`, có mật khẩu phức tạp (gồm chữ hoa, chữ thường, chữ số và ký tự đặc biệt).
* **Chuỗi kết nối (Connection String):** Đã sao chép sẵn chuỗi kết nối dạng SRV tiêu chuẩn.

---

### 2. Chi tiết về Công cụ và Môi trường máy cá nhân (Local Tooling)

Người thực hiện cần chuẩn bị một máy tính cá nhân (hệ điều hành Windows 10/11, macOS hoặc Linux) và cài đặt đầy đủ các phần mềm công cụ dòng lệnh sau:

**Môi trường Runtime Node.js và Công cụ Quản lý Gói npm:**
* **Phiên bản khuyến nghị:** Node.js `v24.x LTS` trở lên, đi kèm công cụ npm phiên bản `v10.x` trở lên.
* **Mục đích:** Chạy thử nghiệm ứng dụng Express.js ở môi trường local, cài đặt các thư viện phụ thuộc (dependencies) cho cả 2 thư mục Backend và Frontend, chạy các kịch bản kiểm thử tự động (Unit Test) và đóng gói bản build mã nguồn tĩnh cho React SPA.

**Công cụ Quản lý Mã nguồn Git CLI:**
* Git được cài đặt phiên bản mới nhất, đã cấu hình thông tin định danh cá nhân (`user.name` và `user.email`).
* **File loại bỏ theo dõi (`.gitignore`):** Phải được cấu hình chặt chẽ tại cả thư mục gốc, thư mục Backend và Frontend để tuyệt đối không lưu vết các tệp chứa bí mật (file `.env`), thư mục thư viện (`node_modules`), các tệp mã nguồn tạm hoặc bản build tĩnh (`dist`) lên GitHub.

**Nền tảng Đóng gói Container Docker Desktop:**
* Docker Desktop (trên Windows/macOS) hoặc Docker Engine (trên Linux) phiên bản mới nhất.
* **Cấu hình tài nguyên:** Cấp tối thiểu 4GB RAM và 2 CPU Core cho Docker Daemon để đảm bảo tiến trình biên dịch Docker Image Multi-stage cho Backend diễn ra nhanh chóng, không bị thiếu hụt bộ nhớ.
* **Trạng thái:** Docker Daemon phải ở trạng thái đang chạy (Engine Running). Công cụ được dùng để build thử nghiệm image tại local và kiểm tra tính chính xác của file Dockerfile trước khi đẩy code lên pipeline.

**Công cụ Dòng lệnh AWS CLI (Version 2):**
* Cài đặt gói AWS CLI v2 chuẩn cho hệ điều hành tương ứng.
* Cấu hình khởi tạo sẵn bằng lệnh `aws configure` với thông số mặc định Region là `ap-southeast-1` và định dạng đầu ra mặc định là `json`.
* Công cụ được dùng để xác minh tính hợp lệ của quyền IAM, kiểm tra kết nối với S3 và hỗ trợ thao tác nhanh với hạ tầng từ dòng lệnh máy cá nhân.

**Trình soạn thảo Mã nguồn và Công cụ Terminal:**
* Trình soạn thảo Visual Studio Code (VS Code) có cài đặt các tiện ích mở rộng (Extensions) hỗ trợ như: Docker, GitLens, YAML, Prettier và Environment Sensitive Settings.
* Cửa sổ dòng lệnh Terminal chuyên dụng như PowerShell (trên Windows) hoặc Bash/Zsh Terminal (trên macOS/Linux).

---

### 3. Chi tiết Cấu trúc Mã nguồn và Tệp Môi trường Mẫu

**Cây Thư mục Dự án Monorepo LearnSphere:**
* **Thư mục `LearnSphere_BE`:** Chứa mã nguồn ứng dụng Backend Node.js/Express, điểm tệp khởi chạy chính `src/server.js`, các tệp xử lý tuyến đường `routes`, bộ điều khiển `controllers`, mô hình dữ liệu Mongoose `models`, chỉ thị kiểm thử health check tại `/health/ready` và tệp `Dockerfile` phục vụ đóng gói container.
* **Thư mục `LearnSphere_FE`:** Chứa mã nguồn ứng dụng Frontend React/Vite, cấu hình mã nguồn TypeScript, trang quản trị Dashboard, giao diện xem video khóa học, giao diện bài thi Quiz, tệp cấu hình đóng gói `vite.config.ts` và tệp chỉ định biến môi trường giao diện.
* **Thư mục `.github/workflows`:** Chứa tệp cấu hình tự động hóa `deploy.yml` chịu trách nhiệm điều phối toàn bộ chuỗi công việc CI/CD từ đẩy image ECR đến ra lệnh deploy trên EC2 qua SSM.

**Chuẩn bị Danh sách Biến Môi trường Mẫu (`.env.example`):**
* Người thực hiện cần nắm rõ các thông số bắt buộc phải khai báo cho ứng dụng Backend bao gồm:
  * Cổng dịch vụ (`PORT`): `5000`.
  * Môi trường thực thi (`NODE_ENV`): `production`.
  * Cấu hình ủy quyền (`TRUST_PROXY`): `true` (cho phép nhận biết header từ CloudFront).
  * Chuỗi kết nối MongoDB (`MONGODB_URI`): Chuỗi SRV kết nối tới MongoDB Atlas.
  * Chuỗi khóa bảo mật JWT (`JWT_SECRET`): Chuỗi ngẫu nhiên có độ dài tối thiểu 64 ký tự.
  * Tên miền Frontend (`FRONTEND_URL`): Địa chỉ HTTPS chính thức do CloudFront cấp.
  * Vùng dịch vụ AWS (`AWS_REGION`): `ap-southeast-1`.
  * Tên S3 Bucket truyền thông (`AWS_S3_BUCKET`): Tên duy nhất của bucket lưu video/hình ảnh.
  * Khóa dịch vụ AI (`GROQ_API_KEY`): Khóa API phục vụ tính năng trợ lý học tập AI.

---

### 4. Bảng Rà soát Điều kiện Tiền đề (Pre-flight Checklist)

Trước khi chuyển sang **Phần 5.3 (Mô tả kiến trúc)** và **Phần 5.4 (Các bước thực hành)**, người thực hiện phải xác nhận đạt 100% các tiêu chuẩn trong bảng kiểm tra dưới đây:

- **Tài khoản AWS:** Đã đăng nhập vào AWS Management Console tại Region Singapore (`ap-southeast-1`) bằng tài khoản có quyền IAM phù hợp.
- **Cơ sở dữ liệu:** Đã kết nối thử nghiệm thành công tới MongoDB Atlas Cluster qua phần mềm MongoDB Compass hoặc ứng dụng test tại local.
- **Mã nguồn Local:** Đã chạy thử lệnh kiểm thử backend thành công và chạy lệnh build frontend tạo ra thư mục `dist` mà không có bất kỳ lỗi biên dịch TypeScript nào.
- **Docker Local:** Đã thực thi lệnh build thử nghiệm Docker Image cho backend tại máy cá nhân và container khởi chạy phản hồi trạng thái 200 OK tại endpoint `/health/ready`.
- **GitHub Repository:** Mã nguồn đã được đưa lên nhánh `main` của GitHub Repository và sẵn sàng cho việc thiết lập OIDC cùng Repository Secrets.
- **Môi trường Máy cá nhân:** Đã mở sẵn cửa sổ dòng lệnh Terminal, trình duyệt web và phần mềm Docker Desktop ở trạng thái sẵn sàng làm việc.