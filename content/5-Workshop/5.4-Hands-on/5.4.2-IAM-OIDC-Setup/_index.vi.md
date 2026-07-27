---
title: "Thiết lập Phân quyền và Bảo mật (AWS IAM & OIDC)"
date: 2026-07-27
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

# 5.4.2. Thiết lập Phân quyền và Bảo mật (AWS IAM & OIDC)

Trong bước này, người thực hiện sẽ cấu hình cơ chế bảo mật **Zero Static Credentials** bằng cách thiết lập GitHub OIDC Identity Provider và khởi tạo các IAM Roles cho máy chủ EC2 và pipeline CI/CD GitHub Actions.

---

### 1. Khởi tạo OpenID Connect (OIDC) Identity Provider cho GitHub

1. Truy cập **AWS Management Console** $\rightarrow$ dịch vụ **IAM** $\rightarrow$ mục **Identity providers** $\rightarrow$ chọn **Add provider**.
2. Chọn loại Provider: **OpenID Connect**.
3. Khai báo các thông số:
   - **Provider URL:** `https://token.actions.githubusercontent.com`
   - **Audience:** `sts.amazonaws.com`
4. Bấm **Get thumbprint** để AWS tự động lấy vân tay bảo mật từ GitHub, sau đó chọn **Add provider**.

---

### 2. Tạo IAM Role 1 cho GitHub Actions (`LearnSphereGitHubDeployRole`)

1. Tại bảng điều khiển IAM, chuyển đến mục **Roles** $\rightarrow$ chọn **Create role**.
2. Chọn **Web identity** $\rightarrow$ Chọn Identity Provider `token.actions.githubusercontent.com` $\rightarrow$ Audience chọn `sts.amazonaws.com`.
3. Nhập tên GitHub Repository: `Sonnguyen2410/Report_Nguyen_Hong_Son` (hoặc tên repo LearnSphere của bạn).
4. Tại bước **Permissions**, đính kèm các quyền ngắn hạn:
   - `AmazonEC2ContainerRegistryPowerUser`
   - Cấp quyền `s3:Sync` tới Bucket Frontend.
   - Cấp quyền `cloudfront:CreateInvalidation`.
   - Cấp quyền `ssm:SendCommand` tới máy chủ EC2.
5. Đặt tên Role: `LearnSphereGitHubDeployRole` và hoàn tất tạo Role.
6. Sao chép lại chuỗi **Role ARN** (ví dụ: `arn:aws:iam::575620421319:role/LearnSphereGitHubDeployRole`).

---

### 3. Tạo IAM Role 2 cho Máy chủ EC2 (`LearnSphereEc2Role`)

1. Chọn **Create role** $\rightarrow$ chọn loại **AWS service** $\rightarrow$ Use case chọn **EC2**.
2. Đính kèm các chính sách bảo mật (Managed Policies):
   - `AmazonSSMManagedInstanceCore`: Cho phép SSM Agent duy trì kết nối điều khiển an toàn với AWS Systems Manager mà không cần mở cổng SSH 22.
   - `AmazonEC2ContainerRegistryReadOnly`: Cho phép EC2 kéo Docker Images từ ECR.
3. Tạo thêm Custom Policy cấp quyền truy cập S3 Media Bucket (`LearnSphereMediaPolicy`) và CloudWatch Logs Group (`/learnsphere/backend`).
4. Đặt tên Role: `LearnSphereEc2Role` và lưu Role.
