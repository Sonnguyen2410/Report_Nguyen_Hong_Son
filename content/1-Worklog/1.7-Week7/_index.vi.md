---
title: "Worklog Tuần 7"
date: 2026-07-31
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

## Chủ đề: Tự động hóa CI/CD Pipeline với GitHub Actions & SSM RunCommand Rollback

### 1. Mục tiêu tuần 7
* Khai báo toàn bộ Repository Secrets trên GitHub Actions (`AWS_GITHUB_ROLE_ARN`, `EC2_INSTANCE_ID`, `VITE_API_BASE_URL`, `S3_FE_BUCKET`, `CLOUDFRONT_FE_DISTRIBUTION_ID`).
* Xây dựng kịch bản tự động hóa triển khai `.github/workflows/deploy.yml` gồm 2 Jobs: `deploy-backend` và `deploy-frontend`.
* Tự động hóa quy trình triển khai Backend qua **AWS Systems Manager (SSM) RunCommand** không cần mở cổng SSH 22.
* Xây dựng kịch bản kiểm tra sức khỏe container thử nghiệm (`candidate`), chuyển đổi Zero-Downtime Swapping và tự động hoàn tác (**Auto-Rollback**) về phiên bản `rollback` nếu phát sinh lỗi.
* Tự động hóa biên dịch Frontend React, đồng bộ dữ liệu S3 và hủy bộ nhớ đệm CloudFront Invalidation.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (27/07/2026)** | • Mở GitHub Repository $\rightarrow$ **Settings** $\rightarrow$ **Secrets and variables** $\rightarrow$ **Actions**.<br>• Khai báo 5 biến Secrets quan trọng phục vụ CI/CD.<br>• Xác minh tuyệt đối không lưu trữ bất kỳ Static Access Keys nào trên GitHub. | • 5 Secrets CI/CD được khai báo an toàn.<br>• Bảo mật Zero Static Credentials. |
| **Thứ 3 (28/07/2026)** | • Viết tệp workflow `.github/workflows/deploy.yml` cấu hình Job `deploy-backend`.<br>• Cấu hình bước `aws-actions/configure-aws-credentials@v4` xác thực qua OIDC.<br>• Viết bước đóng gói Docker Image Backend với tag SHA Git Commit và đẩy lên ECR. | • Workflow `deploy.yml` Job Backend.<br>• Tự động build & push Docker Image ECR. |
| **Thứ 4 (29/07/2026)** | • Viết kịch bản Bash script triển khai EC2 qua `aws ssm send-command`.<br>• Cấu hình luồng chạy container thử nghiệm `candidate` trên cổng tạm 5001 và vòng lặp Health Check endpoint `/health/ready` 24 lần.<br>• Xây dựng logic đổi tên container Zero-Downtime Swapping và kịch bản Auto-Rollback khi Health Check thất bại. | • SSM RunCommand Deployment Script.<br>• Cơ chế Zero-Downtime & Auto-Rollback. |
| **Thứ 5 (30/07/2026)** | • Viết tiếp Job `deploy-frontend` trong file `deploy.yml` chạy sau khi Job Backend thành công.<br>• Thực hiện biên dịch mã nguồn React (`npm run build`), đồng bộ thư mục `dist` lên S3 Frontend Bucket qua lệnh `aws s3 sync --delete`.<br>• Tạo lệnh hủy bộ nhớ đệm `aws cloudfront create-invalidation --paths "/*"`. | • Job `deploy-frontend` hoàn chỉnh.<br>• Tự động sync S3 & Invalidate CDN cache. |
| **Thứ 6 (31/07/2026)** | • Thực hiện lệnh `git push origin main` kích hoạt tự động toàn bộ pipeline CI/CD.<br>• Khắc phục sự cố IAM Trust Policy ban đầu (do sai lệch chuỗi `repo:username/repository:ref:refs/heads/main`).<br>• Xác nhận toàn bộ 2 Jobs `deploy-backend` và `deploy-frontend` hoàn thành thành công và họp review tuần 7. | • Pipeline GitHub Actions chạy xanh 100%.<br>• Khắc phục sự cố OIDC thành công. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS & DevOps
* **AWS Systems Manager (SSM) RunCommand:**
  * Gửi và thực thi kịch bản lệnh shell mã hóa trực tiếp trên máy chủ EC2 từ xa qua API của AWS mà không cần kết nối SSH.
* **Tự động hóa CI/CD với GitHub Actions:**
  * Quản lý sự phụ thuộc giữa các Jobs (`needs: deploy-backend`).
  * Sử dụng các official Actions: `actions/checkout@v4`, `aws-actions/configure-aws-credentials@v4`, `aws-actions/amazon-ecr-login@v2`.
* **Chiến lược Zero-Downtime Deployment & Container Health Check:**
  * Thử nghiệm ứng dụng trên cổng phụ trước khi chuyển traffic cổng chính.
  * Tự động xóa bỏ container lỗi và khôi phục container cũ (`learnsphere-be-rollback`) đảm bảo hệ thống luôn sẵn sàng.

---

### 4. Kết quả đạt được (Deliverables)
* Workflow `.github/workflows/deploy.yml` tự động hóa toàn bộ quy trình triển khai Backend & Frontend.
* Kịch bản triển khai SSM RunCommand tích hợp Health Check và Auto-Rollback an toàn.
* Tự động làm mới bộ nhớ đệm CloudFront CDN `/*` khi có phiên bản Frontend mới.
