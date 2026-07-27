---
title: "Clean-up (Dọn dẹp tài nguyên)"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### 5.5.1. Tổng quan quy trình Dọn dẹp Tài nguyên

Sau khi hoàn thành bài thực hành triển khai và nghiệm thu hệ thống **LearnSphere** trên AWS, việc dọn dẹp toàn bộ tài nguyên đám mây đã khởi tạo là bước bắt buộc. Mục đích của quy trình này là giải phóng hoàn toàn các tài nguyên không còn sử dụng, phòng tránh tối đa nguy cơ phát sinh chi phí ngoài ý muốn trên tài khoản AWS.

Thứ tự dọn dẹp phải tuân thủ nghiêm ngặt theo **chiều ngược lại của chuỗi phụ thuộc tài nguyên (Reverse Dependency Order)** để tránh các lỗi bị từ chối xóa do tài nguyên đang được dịch vụ khác tham chiếu:

```text
[1. CloudFront Distribution] ➔ [2. S3 Buckets] ➔ [3. EC2 Instance] ➔ [4. ECR Repository] ➔ [5. CloudWatch & SNS] ➔ [6. IAM Roles & OIDC]
```

---

### 5.5.2. Chi tiết các bước Dọn dẹp Tài nguyên AWS

#### Bước 1: Vô hiệu hóa và Xóa Amazon CloudFront Distribution

CloudFront Distribution cần được vô hiệu hóa trước khi có thể xóa hoàn toàn:

1. Truy cập **AWS Management Console** -> Tìm và chọn dịch vụ **CloudFront**.
2. Tại danh sách Distributions, chọn Distribution đại diện cho ứng dụng LearnSphere (Distribution ID: `EQRDOBSCG5MC8`).
3. Chọn nút **Disable** và xác nhận. Cần chờ khoảng 3 - 5 phút để trạng thái của Distribution chuyển từ `Enabled` sang `Disabled` (hoàn tất triển khai cấu hình vô hiệu hóa trên toàn cầu).
4. Sau khi trạng thái đã chuyển sang `Disabled`, chọn Distribution đó một lần nữa và nhấn nút **Delete** để xóa hoàn toàn.

#### Bước 2: Xóa dữ liệu và Xóa các Amazon S3 Buckets

S3 Bucket chỉ có thể xóa sau khi đã dọn dẹp sạch toàn bộ các đối tượng (Objects) bên trong:

1. Truy cập dịch vụ **Amazon S3**.
2. **Dọn dẹp Media Bucket (`learnsphere-media-575620421319`):**
   - Chọn tên bucket `learnsphere-media-575620421319`.
   - Nhấn nút **Empty**, nhập từ khóa `permanently delete` để xác nhận xóa toàn bộ video, hình ảnh và tệp tài liệu lưu trữ.
   - Sau khi bucket đã trống, quay lại danh sách Buckets, chọn bucket này và nhấn **Delete**.
3. **Dọn dẹp Frontend Bucket (`learnsphere-fe-575620421319`):**
   - Thực hiện tương tự: Chọn bucket `learnsphere-fe-575620421319` -> Nhấn **Empty** để xóa các tệp tĩnh React -> Nhấn **Delete** để xóa bucket.

#### Bước 3: Hủy máy chủ ảo Amazon EC2

Hủy máy chủ EC2 sẽ tự động giải phóng địa chỉ IP công cộng và ổ đĩa lưu trữ EBS đính kèm:

1. Truy cập dịch vụ **Amazon EC2** -> Chọn mục **Instances**.
2. Chọn máy chủ EC2 của ứng dụng LearnSphere (Instance ID: `i-008c48e6c120b2978`).
3. Chọn menu **Instance state** -> Chọn **Terminate instance**.
4. Nhấn **Terminate** để xác nhận. Trạng thái máy chủ sẽ chuyển từ `Running` -> `Shutting-down` -> `Terminated`.

#### Bước 4: Xóa Kho lưu trữ Amazon ECR

1. Truy cập dịch vụ **Amazon ECR** -> Chọn mục **Private repositories**.
2. Chọn kho lưu trữ `learnsphere-be`.
3. Nhấn nút **Delete**, nhập tên repository `learnsphere-be` để xác nhận xóa kho lưu trữ cùng toàn bộ các bản đóng gói Docker Image tags bên trong.

#### Bước 5: Xóa Hệ thống Giám sát CloudWatch & Amazon SNS

1. **Xóa CloudWatch Alarms:**
   - Truy cập **CloudWatch** -> Chọn **Alarms** -> **All alarms**.
   - Chọn 2 cảnh báo: `LearnSphere-EC2-HighCPU` và `LearnSphere-EC2-StatusCheckFailed`.
   - Chọn **Actions** -> Nhấn **Delete**.
2. **Xóa CloudWatch Log Group:**
   - Chọn mục **Logs** -> **Log groups**.
   - Tìm Log Group `/learnsphere/backend`.
   - Chọn **Actions** -> Nhấn **Delete log group**.
3. **Xóa Amazon SNS Topic & Subscriptions:**
   - Truy cập dịch vụ **Amazon SNS** -> Chọn mục **Topics**.
   - Chọn Topic `LearnSphere-Alerts` -> Nhấn **Delete**.
   - Chọn mục **Subscriptions** -> Chọn Subscription Email liên quan -> Nhấn **Delete**.

#### Bước 6: Xóa Phân quyền IAM Roles & OIDC Provider

1. **Xóa IAM Roles:**
   - Truy cập dịch vụ **IAM** -> Chọn mục **Roles**.
   - Tìm và xóa Role `LearnSphereGitHubDeployRole` (Role của GitHub Actions).
   - Tìm và xóa Role `LearnSphereEc2Role` (Role của máy chủ EC2).
2. **Xóa Identity Provider:**
   - Tại menu IAM -> Chọn mục **Identity providers**.
   - Chọn Provider `token.actions.githubusercontent.com` -> Nhấn **Delete**.

---

### 5.5.3. Bảng Kiểm tra Xác nhận Nghiệm thu Dọn dẹp (Clean-up Checklist)

| Tài nguyên AWS | Tên / Định danh Tài nguyên | Trạng thái sau Dọn dẹp |
|---|---|---|
| CloudFront | Distribution ID: `EQRDOBSCG5MC8` | Đã xóa hoàn toàn (Deleted) |
| S3 Media Bucket | `learnsphere-media-575620421319` | Đã làm trống & xóa Bucket |
| S3 FE Bucket | `learnsphere-fe-575620421319` | Đã làm trống & xóa Bucket |
| EC2 Instance | Instance ID: `i-008c48e6c120b2978` | Đã hủy (Terminated) |
| ECR Repository | `learnsphere-be` | Đã xóa Repository |
| CloudWatch Alarms | `LearnSphere-EC2-HighCPU` & `LearnSphere-EC2-StatusCheckFailed` | Đã xóa Alarms |
| CloudWatch Logs | Log Group: `/learnsphere/backend` | Đã xóa Log Group |
| Amazon SNS | Topic: `LearnSphere-Alerts` | Đã xóa Topic & Subscriptions |
| IAM Roles | `LearnSphereGitHubDeployRole` & `LearnSphereEc2Role` | Đã xóa Roles |
| IAM OIDC | `token.actions.githubusercontent.com` | Đã xóa Provider |