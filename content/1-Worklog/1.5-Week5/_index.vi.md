---
title: "Worklog Tuần 5"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

## Chủ đề: Đóng gói Container Docker Multi-stage & Khởi tạo Máy chủ Amazon EC2

### 1. Mục tiêu tuần 5 (29/06/2026 – 03/07/2026)
* Đóng gói mã nguồn Backend `LearnSphere_BE` bằng kỹ thuật **Multi-stage Docker Build** trên nền Linux Alpine siêu nhẹ.
* Tối ưu hóa tính an toàn container: Chạy ứng dụng dưới quyền người dùng không phải root (non-root user).
* Khởi tạo Amazon ECR Private Repository `learnsphere-be` và đẩy bản đóng gói Docker Image đầu tiên.
* Khởi tạo máy chủ **Amazon EC2 (`t3.small`)** tại Region Singapore (`ap-southeast-1`), cấu hình IAM Role `LearnSphereEc2Role` và bộ nhớ Swap 2GB.
* Thiết lập Security Group đóng cổng 22 SSH và kết nối quản trị từ xa qua **AWS Systems Manager (SSM) Session Manager**.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (29/06/2026)** | • Viết `Dockerfile` Multi-stage cho Backend: Stage 1 (Build dependencies `node:24-alpine`) và Stage 2 (Production runtime minimal).<br>• Cấu hình bảo mật container: Tạo nhóm/người dùng non-root (`nodejs`/`expressuser`) để thực thi process Node.js.<br>• Thêm kịch bản kiểm tra sức khỏe `HEALTHCHECK` gọi endpoint `/health/ready`. | • File `Dockerfile` Multi-stage tối ưu dung lượng (chỉ ~180MB).<br>• Container chạy quyền non-root an toàn. |
| **Thứ 3 (30/06/2026)** | • Viết file `docker-compose.yml` để chạy thử nghiệm môi trường Backend và cơ sở dữ liệu local.<br>• Chạy lệnh `docker build -t learnsphere-be:latest .` và kiểm thử container trên cổng 5000.<br>• Xác nhận container phản hồi status 200 OK từ endpoint health check. | • Môi trường Docker Compose local hoàn thiện.<br>• Kiểm thử thành công image Backend local. |
| **Thứ 4 (01/07/2026)** | • Mở AWS Console $\rightarrow$ Dịch vụ **Amazon ECR** $\rightarrow$ Tạo Private Repository `learnsphere-be`.<br>• Bật tính năng tự động quét lỗ hổng bảo mật (*Scan on push*).<br>• Thực hiện đăng nhập ECR bằng AWS CLI và đẩy bản build đầu tiên lên ECR: `docker push <account-id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest`. | • Kho chứa ECR `learnsphere-be` hoạt động.<br>• Đẩy bản Docker Image đầu tiên lên ECR. |
| **Thứ 5 (02/07/2026)** | • Khởi tạo máy chủ **Amazon EC2** (`t3.small`, Amazon Linux 2023) tại Region Singapore (`ap-southeast-1`).<br>• Tạo IAM Role `LearnSphereEc2Role` đính kèm policy `AmazonSSMManagedInstanceCore` & `AmazonEC2ContainerRegistryReadOnly` rồi gán vào EC2.<br>• Chạy User Data script khởi tạo bộ nhớ **Swap 2GB** phòng chống sự cố tràn bộ nhớ (Out-Of-Memory). | • Máy chủ EC2 `i-008c48e6c120b2978` hoạt động.<br>• IAM Role & Swap 2GB cấu hình thành công. |
| **Thứ 6 (03/07/2026)** | • Cấu hình Security Group cho EC2: Đóng hoàn toàn cổng SSH (Port 22), chỉ cho phép nhận kết nối cổng 5000 nội bộ.<br>• Thực hiện kiểm thử kết nối máy chủ từ xa qua **AWS Systems Manager (SSM) Session Manager** từ AWS Console.<br>• Cài đặt Docker Engine trên EC2 và tham gia họp Review tuần 5 với Mentor. | • Bảo mật EC2 100% không mở cổng SSH 22.<br>• Điều khiển máy chủ qua SSM thành công. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **Amazon ECR (Elastic Container Registry):**
  * Lưu trữ và quản lý Docker Images mã hóa riêng tư.
  * Tích hợp tính năng Scan on Push phát hiện lỗ hổng bảo mật trong các thư viện npm/OS.
* **Amazon EC2 & IAM Instance Profile (IMDSv2):**
  * Gán IAM Role trực tiếp cho EC2 Instance giúp máy chủ tự động lấy Temporary Credentials từ dịch vụ IMDSv2 mà không cần lưu file Access Key.
* **AWS Systems Manager (SSM) Session Manager:**
  * Giải pháp điều khiển máy chủ Linux từ xa qua kênh truyền mã hóa TLS của AWS mà không cần mở cổng 22 SSH hay cấp IP Public công khai.

#### B. Công nghệ Docker & Containerization
* **Multi-stage Docker Builds:**
  * Loại bỏ tệp thừa, devDependencies và trình biên dịch sau bước build, giúp thu nhỏ kích thước Image từ >800MB xuống còn ~180MB.

---

### 4. Kết quả đạt được (Deliverables)
* File `Dockerfile` Multi-stage chuẩn bảo mật non-root và kích thước tối ưu.
* ECR Private Repository `learnsphere-be` chứa Docker Image đã quét bảo mật.
* Máy chủ EC2 `i-008c48e6c120b2978` cài sẵn Docker, bộ nhớ Swap 2GB và IAM Role SSM.
* Thiết lập kết nối quản trị an toàn SSH-less qua AWS SSM Session Manager.
