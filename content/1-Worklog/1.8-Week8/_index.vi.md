---
title: "Worklog Tuần 8"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

## Chủ đề: Tích hợp Database/AI, Giám sát CloudWatch/SNS, Kiểm thử Production & Dọn dẹp

### 1. Mục tiêu tuần 8 (20/07/2026 – 24/07/2026)
* Tích hợp an toàn cơ sở dữ liệu **MongoDB Atlas** bằng IP Access List giới hạn qua NAT Gateway.
* Cấu hình **Amazon CloudWatch** thu thập log và thiết lập **Alarm** giám sát tài nguyên (CPU, Health Check).
* Khởi tạo **Amazon SNS Topic** để gửi cảnh báo tự động qua Email khi hệ thống gặp sự cố.
* Thực hiện kiểm thử End-to-End (E2E) toàn bộ tính năng trên môi trường Production thực tế.
* Nghiên cứu và thực hành quy trình dọn dẹp tài nguyên (Clean-up) đúng thứ tự để không phát sinh chi phí.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (20/07/2026)** | • Lấy 2 địa chỉ Elastic IP của 2 NAT Gateway.<br>• Truy cập MongoDB Atlas, thêm 2 IP này vào danh sách Network Access (Whitelist).<br>• Cấu hình quy tắc CORS trên S3 Media Bucket để cho phép frontend upload file. | • MongoDB an toàn, chặn truy cập ngoài.<br>• Upload video, PDF hoạt động tốt. |
| **Thứ 3 (21/07/2026)** | • Mở AWS Console $\rightarrow$ Dịch vụ **Amazon SNS** $\rightarrow$ Tạo Topic mới `LearnSphere-Alerts`.<br>• Tạo Email Subscription trỏ tới email quản trị và xác nhận.<br>• Cấu hình Docker đẩy log tập trung về **CloudWatch Logs** (`/learnsphere/backend`). | • Topic SNS sẵn sàng nhận tin.<br>• Log tập trung dễ dàng debug. |
| **Thứ 4 (22/07/2026)** | • Khởi tạo 2 **CloudWatch Alarms** giám sát tài nguyên EC2 của ASG (`TargetTrackingScaling` hoặc ngưỡng CPU thủ công).<br>• Thiết lập Alarm kích hoạt gửi thông báo qua SNS nếu Target Group báo lỗi Unhealthy. | • Hệ thống giám sát chủ động.<br>• Nhận cảnh báo Email thành công. |
| **Thứ 5 (23/07/2026)** | • Truy cập tên miền chính thức `https://www.learnspherev2.id.vn/` tiến hành kiểm thử toàn bộ luồng.<br>• Kiểm thử tính năng cốt lõi: Đăng ký/Đăng nhập, Tạo khóa học, Upload video/PDF, Làm bài Quiz và Chat với AI Tutor. | • Sản phẩm vận hành hoàn hảo.<br>• E2E Testing Pass 100%. |
| **Thứ 6 (24/07/2026)** | • Lập Bảng kiểm tra nghiệm thu dọn dẹp (Clean-up Checklist Table) theo thứ tự chuẩn xác.<br>• Thực hành các bước vô hiệu hóa CloudFront, Empty/Delete S3 Buckets, Xóa ALB, ASG, NAT Gateway và VPC.<br>• Họp tổng kết tiến độ kỹ thuật tuần 8 với Mentor. | • Nắm vững quy trình dọn dẹp.<br>• Không phát sinh chi phí ảo. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS & System Operations
* **Amazon CloudWatch Logs & Alarms:**
  * Thu thập log tập trung từ các nguồn khác nhau, giúp theo dõi trạng thái hệ thống theo thời gian thực.
  * Thiết lập Alarm dựa trên các Metrics quan trọng (CPUUtilization, UnHealthyHostCount).
* **Amazon SNS (Simple Notification Service):**
  * Mô hình Publisher/Subscriber linh hoạt để phát thông báo khẩn cấp khi hệ thống phát sinh sự cố.
* **Quy trình Dọn dẹp tài nguyên (Resource Clean-up):**
  * Hiểu rõ sự phụ thuộc chéo giữa các dịch vụ mạng (VPC, NAT, ALB, ASG, Security Group).
  * Tuân thủ việc xóa tài nguyên theo chiều ngược lại (Reverse Dependency Order).

---

### 4. Kết quả đạt được (Deliverables)
* Tích hợp thành công MongoDB Atlas và S3 CORS an toàn.
* Kênh giám sát và cảnh báo tự động CloudWatch & SNS hoạt động ổn định.
* Sản phẩm LearnSphere vượt qua quá trình kiểm thử E2E trên Production.
* Bảng Clean-up Checklist chính xác đảm bảo quản lý tài nguyên hiệu quả.
