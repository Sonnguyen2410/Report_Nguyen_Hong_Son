---
title: "Worklog Tuần 8"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

## Chủ đề: Thiết lập Giám sát CloudWatch/SNS, Kiểm thử Production, Dọn dẹp & Complete Báo cáo

### 1. Mục tiêu tuần 8
* Khởi tạo **Amazon SNS Topic** (`LearnSphere-Alerts`) và xác nhận Email Subscription nhận cảnh báo hệ thống.
* Tạo mới 2 **CloudWatch Alarms** (`LearnSphere-EC2-HighCPU` & `LearnSphere-EC2-StatusCheckFailed`) theo dõi tài nguyên EC2.
* Đẩy nhật ký hệ thống Docker Container tập trung về **Amazon CloudWatch Logs** (`/learnsphere/backend`).
* Thực hiện nghiệm thu và kiểm thử End-to-End toàn bộ tính năng sản phẩm trên tên miền chính thức **`https://www.learnsphere.id.vn/`**.
* Thực hành quy trình Dọn dẹp tài nguyên đám mây (Clean-up) theo chiều ngược lại (Reverse Dependency Order) và lập Bảng nghiệm thu.
* Hoàn thiện toàn bộ báo cáo thực tập (Hugo Site), kiểm tra định dạng và tham gia lễ tổng kết kỳ thực tập.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (03/08/2026)** | • Mở AWS Console $\rightarrow$ Dịch vụ **Amazon SNS** $\rightarrow$ Tạo Topic mới `LearnSphere-Alerts`.<br>• Tạo Email Subscription trỏ tới địa chỉ email người quản trị (`son.nguyenhong2410@hcmut.edu.vn`).<br>• Mở hộp thư cá nhân và bấm xác nhận **Confirm subscription** thành công. | • Topic SNS `LearnSphere-Alerts` hoạt động.<br>• Email Subscription sẵn sàng nhận tin. |
| **Thứ 3 (04/08/2026)** | • Mở **CloudWatch Console** $\rightarrow$ Khởi tạo Alarm 1 `LearnSphere-EC2-HighCPU` (cảnh báo khi CPU > 80% trong 10 phút).<br>• Khởi tạo Alarm 2 `LearnSphere-EC2-StatusCheckFailed` (cảnh báo khẩn cấp khi EC2 lỗi phần cứng/mạng trong 60 giây).<br>• Cấu hình Docker AWS Logs Driver đẩy toàn bộ log Backend về CloudWatch Log Group `/learnsphere/backend`. | • 2 CloudWatch Alarms ở trạng thái OK.<br>• Log Group `/learnsphere/backend` tập trung. |
| **Thứ 4 (05/08/2026)** | • Truy cập tên miền chính thức **`https://www.learnsphere.id.vn/`** tiến hành kiểm thử nghiệm thu End-to-End toàn bộ sản phẩm.<br>• Kiểm thử các luồng: Đăng ký/Đăng nhập, Tạo khóa học, Upload video/PDF qua Presigned PUT URL, Học viên xem video qua Presigned GET URL, Thi Quiz và Hỏi đáp với Trợ lý AI Tutor. | • Sản phẩm vận hành hoàn hảo trên AWS.<br>• 100% các tính năng hoạt động ổn định. |
| **Thứ 5 (06/08/2026)** | • Nghiên cứu quy trình Dọn dẹp tài nguyên đám mây theo chiều ngược lại (*Reverse Dependency Order*).<br>• Thực hành các bước dọn dẹp thử nghiệm: Vô hiệu hóa CloudFront, Empty & Delete S3 Buckets, Terminate EC2, Delete ECR Repo, Delete CloudWatch/SNS & IAM Roles.<br>• Lập Bảng kiểm tra xác nhận nghiệm thu dọn dẹp (Clean-up Checklist Table). | • Quy trình Dọn dẹp chuẩn hóa.<br>• Bảng Clean-up Checklist 100% chính xác. |
| **Thứ 6 (07/08/2026)** | • Rà soát và hoàn thiện toàn bộ mã nguồn website Báo cáo thực tập trên nền Hugo Relearn Theme.<br>• Kiểm tra tính đồng bộ nội dung giữa 2 phiên bản Tiếng Việt và Tiếng Anh.<br>• Tham gia Lễ tổng kết kỳ thực tập chương trình **AWS First Cloud AI Journey (FCAJ)** với Ban Quản lý và Mentor. | • Báo cáo Thực tập hoàn thiện 100%.<br>• Hoàn thành xuất sắc kỳ thực tập FCAJ. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS & System Operations
* **Amazon CloudWatch Logs & Alarms:**
  * Thu thập log tập trung từ Docker Containers và định nghĩa các chỉ số cảnh báo ngưỡng CPU/Hardware.
* **Amazon SNS (Simple Notification Service):**
  * Mô hình Publisher/Subscriber phát thông báo khẩn cấp qua Email khi hệ thống phát sinh sự cố.
* **AWS Resource Lifecycle & Clean-up Best Practices:**
  * Nguyên tắc giải phóng hoàn toàn tài nguyên không còn sử dụng để phòng tránh phát sinh chi phí ngoài ý muốn.

---

### 4. Kết quả đạt được (Deliverables)
* Kênh giám sát và cảnh báo tự động CloudWatch & SNS hoạt động ổn định.
* Sản phẩm LearnSphere vận hành hoàn hảo trên tên miền chính thức `https://www.learnsphere.id.vn/`.
* Quy trình Dọn dẹp tài nguyên AWS chuẩn hóa kèm Bảng kiểm tra nghiệm thu.
* Website Báo cáo thực tập hoàn thiện 100% chuẩn bị nộp cho Ban Quản lý chương trình FCAJ.
