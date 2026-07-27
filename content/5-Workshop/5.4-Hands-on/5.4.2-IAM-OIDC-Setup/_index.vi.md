---
title: "Thiết lập Phân quyền và Bảo mật (AWS IAM & OIDC)"
date: 2026-07-27
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

Trong bước này, người thực hiện sẽ cấu hình cơ chế bảo mật **Zero Static Credentials** bằng cách tạo GitHub OpenID Connect (OIDC) Provider, khởi tạo IAM Role triển khai cho GitHub Actions và IAM Role gán cho máy chủ EC2.

---

### 2.1. Tạo GitHub OIDC Provider trong AWS IAM

Cơ chế OIDC cho phép GitHub Actions nhận thông tin xác thực tạm thời ngắn hạn từ AWS Security Token Service (STS) khi chạy pipeline. Nhờ đó, hệ thống tuyệt đối **không cần lưu `AWS_ACCESS_KEY_ID` và `AWS_SECRET_ACCESS_KEY` tĩnh** trên GitHub Secrets hoặc máy chủ.

1. Đăng nhập **AWS Management Console** -> dịch vụ **IAM** -> chọn **Identity providers** -> bấm **Add provider**.
2. Chọn loại Provider: **OpenID Connect**.
3. Cấu hình thông số:
   - **Provider URL:** `https://token.actions.githubusercontent.com`
   - **Audience:** `sts.amazonaws.com`
4. Bấm nút **Get thumbprint** để AWS tự động xác thực vân tay bảo mật của GitHub.
5. Chọn **Add provider** để hoàn tất.

![Cấu hình GitHub OIDC Provider trong AWS IAM](/images/5-Workshop/5.4/5.4.2.1.png)
<p align="center"><i>Hình 5.4.2.1 — Cấu hình GitHub OIDC Provider trong AWS IAM.</i></p>

---

### 2.2. Tạo IAM Role cho GitHub Actions (`LearnSphereGitHubDeployRole`)

1. Tại bảng điều khiển IAM, chuyển đến mục **Roles** -> chọn **Create role**.
2. **Selected trusted entity:** Chọn **Web identity**.
   - **Identity provider:** Select `token.actions.githubusercontent.com`.
   - **Audience:** Select `sts.amazonaws.com`.
3. Đặt tên Role: `LearnSphereGitHubDeployRole`.
4. Cấu hình **Trust Policy** siết chặt chỉ cho phép duy nhất nhánh `main` của repository LearnSphere thực hiện lệnh `sts:AssumeRoleWithWebIdentity`:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::575620421319:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:Sonnguyen2410/Report_Nguyen_Hong_Son:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

![Trust policy giới hạn GitHub repository được phép assume role](/images/5-Workshop/5.4/5.4.2.2.1.png)
<p align="center"><i>Hình 5.4.2.2a — Trust policy giới hạn duy nhất nhánh main của GitHub repository.</i></p>

5. Gắn các chính sách phân quyền tối thiểu (**Least Privilege**):
   - Đẩy Docker Image vào ECR repository `learnsphere-be`.
   - Đẩy bản build Frontend vào S3 bucket `learnsphere-fe-575620421319`.
   - Tạo CloudFront invalidation để dọn đệm CDN.
   - Gửi lệnh `AWS-RunShellScript` tới máy chủ EC2 thông qua AWS Systems Manager (SSM).

![Các quyền triển khai được gắn cho GitHub Actions](/images/5-Workshop/5.4/5.4.2.2.2.png)
<p align="center"><i>Hình 5.4.2.2b — Các chính sách phân quyền triển khai gắn cho GitHub Actions.</i></p>

---

### 2.3. Tạo IAM Role cho Máy chủ EC2 (`LearnSphereEc2Role`)

Role này được gán trực tiếp vào EC2 Instance Profile, giúp ứng dụng Backend Node.js và AWS CLI trên máy chủ tự động nhận temporary credentials từ Instance Metadata Service (IMDSv2) mà không lưu Access Key trong file `.env`.

1. Tại IAM Roles Console, chọn **Create role** -> Trusted entity chọn **AWS service** -> Use case chọn **EC2**.
2. Đặt tên Role: `LearnSphereEc2Role`.
3. Đính kèm các nhóm quyền tối thiểu:
   - `AmazonSSMManagedInstanceCore`: Cho phép kết nối và điều khiển từ xa qua Systems Manager Session Manager.
   - `AmazonEC2ContainerRegistryReadOnly`: Cho phép kéo Docker Image từ ECR.
   - **S3 Media Custom Policy:** Cho phép `ListBucket`, `PutObject`, `GetObject`, `DeleteObject`, `AbortMultipartUpload` trên bucket `learnsphere-media-575620421319`.
   - **CloudWatch Logs Custom Policy:** Cấp quyền tạo log stream và ghi log vào Log Group `/learnsphere/backend`.
   - **Amazon Bedrock Policy:** Cấp quyền `InvokeModel` và `InvokeModelWithResponseStream`.

![IAM Role cung cấp quyền AWS cho Backend trên EC2](/images/5-Workshop/5.4/5.4.2.3.png)
<p align="center"><i>Hình 5.4.2.3 — IAM Role cung cấp quyền tự động cho Backend Node.js trên máy chủ EC2.</i></p>
