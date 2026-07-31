---
title: "Worklog Tuần 7"
date: 2026-07-27
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

## Chủ đề: Triển khai Frontend CloudFront, Tên miền & Tự động hóa CI/CD GitHub Actions

### 1. Mục tiêu tuần 7 (13/07/2026 – 17/07/2026)
* Khởi tạo **Amazon S3 Frontend Bucket** và cấu hình **Amazon CloudFront CDN** tích hợp Origin Access Control (OAC).
* Đăng ký tên miền và cấp phát chứng chỉ số SSL/TLS qua **AWS Certificate Manager (ACM)**.
* Thiết lập xác thực bảo mật **GitHub Actions OIDC Identity Provider** trên AWS IAM.
* Xây dựng pipeline tự động hóa CI/CD triển khai Backend với cơ chế **ASG Instance Refresh & Auto-Rollback**.
* Xây dựng pipeline tự động hóa triển khai Frontend lên S3 và Invalidate CloudFront Cache.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (13/07/2026)** | • Tạo S3 Bucket `learnsphere-frontend` (Block Public Access hoàn toàn).<br>• Cấu hình CloudFront Distribution, tạo Origin Access Control (OAC) cấp quyền cho CloudFront đọc S3 Bucket.<br>• Thêm Behavior định tuyến API `/api/*` về ALB Backend EC2, giải quyết CORS. | • S3 & CloudFront kết nối bảo mật.<br>• Routing SPA & API hoàn chỉnh. |
| **Thứ 3 (14/07/2026)** | • Xin cấp chứng chỉ SSL cho tên miền `*.learnspherev2.id.vn` qua AWS ACM tại vùng `us-east-1`.<br>• Xác thực DNS qua Route 53.<br>• Cập nhật CloudFront dùng tên miền tùy chỉnh `www.learnspherev2.id.vn` và gán chứng chỉ ACM. | • Website bảo mật HTTPS với domain thật.<br>• Tên miền hoạt động chính thức. |
| **Thứ 4 (15/07/2026)** | • Cấu hình **GitHub OIDC Identity Provider** trên IAM, tạo Role `LearnSphereGitHubDeployRole`.<br>• Gán quyền cho Role: ECR Push, SSM Parameter Store (để rollback tag), và Auto Scaling Update.<br>• Đảm bảo không lưu Static Access Keys trên GitHub Repository. | • Bảo mật Zero Static Credentials.<br>• Phân quyền chặt chẽ theo Repository. |
| **Thứ 5 (16/07/2026)** | • Viết GitHub Actions Workflow triển khai Backend (`deploy-backend.yml`).<br>• Cấu hình bước đóng gói Docker, đẩy lên ECR và kích hoạt lệnh `aws autoscaling start-instance-refresh`.<br>• Xây dựng kịch bản Auto-Rollback: Nếu Refresh thất bại, tự động phục hồi phiên bản (Image Tag) cũ từ SSM Parameter Store. | • CI/CD Backend tự động hóa hoàn toàn.<br>• Cơ chế Rollback an toàn khi lỗi. |
| **Thứ 6 (17/07/2026)** | • Viết Workflow triển khai Frontend (`deploy-frontend.yml`): biên dịch React, đồng bộ file lên S3 (`aws s3 sync`).<br>• Thêm lệnh xóa bộ nhớ đệm `aws cloudfront create-invalidation`.<br>• Kích hoạt CI/CD toàn hệ thống bằng lệnh `git push` và họp Review tuần 7. | • CI/CD Frontend hoàn tất.<br>• Pipeline chạy ổn định 100%. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Phân phối Nội dung & Tên miền (CDN & DNS)
* **Amazon CloudFront & OAC:**
  * Thay vì mở public S3 Bucket, dùng OAC ép buộc mọi truy cập phải đi qua CloudFront.
  * Hỗ trợ Routing định tuyến thông minh nhiều Origins (S3 cho tĩnh, ALB cho động).
* **AWS Certificate Manager (ACM):**
  * Quản lý vòng đời chứng chỉ SSL/TLS miễn phí, yêu cầu tạo ở `us-east-1` để dùng cho CloudFront.

#### B. DevOps & Tự động hóa CI/CD
* **GitHub Actions OIDC (OpenID Connect):**
  * Giải pháp bảo mật tiên tiến cho phép GitHub Actions mượn quyền tạm thời từ AWS thông qua Trust Policy thay vì Access Key.
* **Auto Scaling Instance Refresh:**
  * Cơ chế thay máu máy chủ (Rolling Update) tự động của AWS, thay thế từng EC2 cũ bằng EC2 mới mà không gây gián đoạn (Zero Downtime).

---

### 4. Kết quả đạt được (Deliverables)
* Website hoàn chỉnh truy cập qua tên miền bảo mật `https://www.learnspherev2.id.vn/`.
* Tích hợp thành công IAM OIDC bảo mật CI/CD.
* 2 Pipeline triển khai Backend và Frontend chạy tự động và an toàn (tích hợp Rollback).
