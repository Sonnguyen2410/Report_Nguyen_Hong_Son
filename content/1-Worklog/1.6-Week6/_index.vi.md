---
title: "Worklog Tuần 6"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

## Chủ đề: Cấu hình Phân quyền IAM OIDC, Amazon S3 Buckets & CloudFront CDN

### 1. Mục tiêu tuần 6 (06/07/2026 – 10/07/2026)
* Thiết lập kết nối xác thực **GitHub Actions OIDC Identity Provider** trên AWS IAM, loại bỏ rủi ro lộ static access keys.
* Tạo IAM Role `LearnSphereGitHubDeployRole` đính kèm Trust Policy giới hạn chính xác Repository GitHub `repo:username/repository:ref:refs/heads/main`.
* Khởi tạo **Amazon S3 Frontend Bucket** (`learnsphere-fe-575620421319`) và **S3 Media Bucket** (`learnsphere-media-575620421319`) ở chế độ Block Public Access 100%.
* Triển khai **Amazon CloudFront CDN Distribution** (`EQRDOBSCG5MC8`) bảo mật S3 Frontend qua **Origin Access Control (OAC)** và chuyển tiếp API `/api/*` về máy chủ EC2.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (06/07/2026)** | • Mở AWS IAM Console $\rightarrow$ **Identity providers** $\rightarrow$ Thêm OIDC Provider `token.actions.githubusercontent.com` với Audience `sts.amazonaws.com`.<br>• Lấy vân tay bảo mật Thumbprint của GitHub tự động.<br>• Tạo IAM Role `LearnSphereGitHubDeployRole` với Trust Policy gắn thẻ `sub` ràng buộc repository. | • OIDC Identity Provider cấu hình xong.<br>• IAM Role cho GitHub Actions bảo mật. |
| **Thứ 3 (07/07/2026)** | • Khởi tạo S3 Frontend Bucket `learnsphere-fe-575620421319` tại Singapore (`ap-southeast-1`).<br>• Khởi tạo S3 Media Bucket `learnsphere-media-575620421319`, bật tính năng mã hóa Server-side Encryption (SSE-S3).<br>• Thiết lập quy tắc CORS JSON trên Media Bucket hỗ trợ upload từ trình duyệt. | • 2 S3 Buckets được tạo thành công.<br>• Bật Block Public Access 100% & CORS. |
| **Thứ 4 (08/07/2026)** | • Mở dịch vụ **Amazon CloudFront** $\rightarrow$ Khởi tạo Distribution mới `EQRDOBSCG5MC8`.<br>• Cấu hình Origin 1 trỏ tới S3 Frontend Bucket, tạo mới **Origin Access Control (OAC)**.<br>• Thêm S3 Bucket Policy chỉ cho phép duy nhất CloudFront OAC được đọc đối tượng (`s3:GetObject`). | • CloudFront Distribution được khởi tạo.<br>• Cấu hình OAC bảo mật S3 Private. |
| **Thứ 5 (09/07/2026)** | • Cấu hình Origin 2 trỏ tới DNS/IP của máy chủ EC2 Backend (Port 5000).<br>• Tạo Cache Behavior ưu tiên cho đường dẫn `/api/*` với chế độ `CachingDisabled` và chuyển tiếp toàn bộ Headers.<br>• Viết **CloudFront Function** đính kèm vào Viewer Request tự động sửa đổi URI đường dẫn phụ về `/index.html` xử lý SPA Client-side Routing. | • Routing CloudFront CDN hoàn thiện.<br>• Giải quyết triệt để lỗi CORS & 404 SPA. |
| **Thứ 6 (10/07/2026)** | • Kiểm thử khả năng phân phối mã nguồn tĩnh Frontend từ CloudFront HTTPS domain `d2onzy56n3iw1w.cloudfront.net`.<br>• Xác nhận các cuộc gọi API `/api/health` qua CloudFront chuyển tiếp mượt mà về EC2.<br>• Tham gia họp Review tuần 6 với Mentor. | • Toàn bộ luồng CDN Phân phối mượt mà.<br>• Báo cáo tiến độ tuần 6 thành công. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **AWS IAM OIDC Identity Provider:**
  * Giải pháp bảo mật **Zero Static Credentials**: GitHub Actions lấy thông tin xác thực ngắn hạn từ AWS STS qua giao thức OpenID Connect.
* **Amazon CloudFront & Origin Access Control (OAC):**
  * Thay thế cơ chế cũ OAI bằng OAC tiên tiến hỗ trợ mã hóa HTTP/HTTPS chuẩn AWS.
  * Phân phối toàn bộ ứng dụng qua một tên miền HTTPS duy nhất, loại bỏ lỗi Mixed Content và CORS.
* **CloudFront Edge Functions:**
  * Lập trình kịch bản nhẹ chạy trên điểm mạng biên (Edge Location) để điều hướng đường dẫn ứng dụng trang đơn React.

---

### 4. Kết quả đạt được (Deliverables)
* IAM OIDC Provider & Role `LearnSphereGitHubDeployRole` sẵn sàng cho CI/CD.
* 2 S3 Buckets private bảo mật 100%.
* CloudFront Distribution `EQRDOBSCG5MC8` tích hợp OAC, API Forwarding và CloudFront SPA Function.
