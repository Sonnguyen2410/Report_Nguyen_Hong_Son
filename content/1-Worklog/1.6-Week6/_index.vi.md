---
title: "Worklog Tuần 6"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

## Chủ đề: Triển khai Backend High Availability với Application Load Balancer & Auto Scaling Group

### 1. Mục tiêu tuần 6 (06/07/2026 – 10/07/2026)
* Sử dụng **AWS Systems Manager (SSM) Parameter Store** để quản lý các biến môi trường nhạy cảm một cách an toàn.
* Khởi tạo **EC2 Launch Template** chứa cấu hình khởi động (User Data) cài đặt Docker và kéo image từ ECR.
* Thiết lập **Application Load Balancer (ALB)** phân phối tải HTTP/HTTPS giữa nhiều máy chủ Backend.
* Triển khai **Auto Scaling Group (ASG)** giúp tự động scale máy chủ Backend trải dài trên 2 Availability Zones.
* Thực hiện xác minh tính High Availability (HA Validation) khi một máy chủ gặp sự cố.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (06/07/2026)** | • Lưu trữ các cấu hình nhạy cảm (`MONGO_URI`, `GROQ_API_KEY`, `JWT_SECRET`) vào **Systems Manager Parameter Store** (SecureString).<br>• Cấp quyền `ssm:GetParameters` cho IAM Role `LearnSphere-Backend-Role` để EC2 có thể đọc secrets. | • Quản lý secrets an toàn, không hardcode.<br>• IAM Policy cập nhật. |
| **Thứ 3 (07/07/2026)** | • Tạo **Launch Template** `LearnSphere-Backend-Template` sử dụng AMI Amazon Linux 2023, loại `t3.small`.<br>• Viết kịch bản khởi động (User Data) tự động cài đặt Docker, xác thực ECR, lấy secrets từ SSM và chạy container `learnsphere-be:latest` trên cổng 5000. | • Launch Template sẵn sàng phục vụ tự động hóa.<br>• User Data bootstrap EC2 hoàn chỉnh. |
| **Thứ 4 (08/07/2026)** | • Tạo **Target Group** `LearnSphere-Backend-TG` cổng 5000, giao thức HTTP.<br>• Thiết lập Health Check định kỳ gọi API `/health/ready` (cổng 5000) để theo dõi trạng thái container Backend.<br>• Khởi tạo **Application Load Balancer** public tại 2 Public Subnets, chuyển tiếp traffic port 443 về Target Group. | • Cấu hình Load Balancing hoàn tất.<br>• Health Check theo dõi sức khỏe ứng dụng. |
| **Thứ 5 (09/07/2026)** | • Khởi tạo **Auto Scaling Group** dựa trên Launch Template vừa tạo.<br>• Cấu hình trải dài trên 2 Private Subnets (Availability Zone `1a`, `1b`).<br>• Thiết lập tham số scale: `Desired capacity = 2`, `Minimum capacity = 2`, `Maximum capacity = 4`.<br>• Liên kết Auto Scaling Group với Target Group của ALB. | • ASG duy trì ổn định 2 EC2 Instances.<br>• Kiến trúc High Availability (HA) hoàn thiện. |
| **Thứ 6 (10/07/2026)** | • Truy cập DNS Name của ALB để kiểm thử luồng hoạt động tổng thể của Backend.<br>• Thực hành HA Validation: Cố tình tắt máy (Terminate) 1 máy chủ EC2 InService để quan sát ASG tự động phát hiện lỗi và khởi chạy máy chủ thay thế.<br>• Họp Review tuần 6 với Mentor. | • Backend phân phối tải thành công qua ALB.<br>• ASG tự phục hồi (Self-healing) hiệu quả. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **Application Load Balancer (ALB) & Target Group:**
  * ALB hoạt động ở lớp 7 (Layer 7), có khả năng đọc HTTP/HTTPS header để định tuyến thông minh.
  * Target Group Health Check quyết định máy chủ nào "khỏe mạnh" để nhận lượng truy cập.
* **Auto Scaling Group (ASG) & Launch Template:**
  * ASG giúp hệ thống chịu lỗi (Fault Tolerant) nhờ khả năng thay thế instance bị hỏng.
  * Tái sử dụng cấu hình thông qua Launch Template, quản lý các phiên bản cấu hình dễ dàng.
* **AWS Systems Manager Parameter Store:**
  * Mã hóa biến môi trường bằng KMS, truyền động qua môi trường chạy thay vì lưu file `.env`.

---

### 4. Kết quả đạt được (Deliverables)
* Secrets quản lý tập trung và mã hóa an toàn trên AWS SSM.
* ALB Public Endpoint phân phối lưu lượng Backend ổn định.
* Auto Scaling Group duy trì High Availability với khả năng tự phục hồi.
