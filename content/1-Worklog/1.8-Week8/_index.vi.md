---
title: "Worklog Tuần 8"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---

## Chủ đề: Cấu hình giám sát hệ thống với AWS CloudWatch Logs/Alarms và phát triển trang Admin System Monitoring

### 1. Mục tiêu tuần 8
* Thành thạo việc cấu hình và quản lý hệ thống giám sát AWS CloudWatch (Logs & Alarms) để theo dõi nhật ký hoạt động và hiệu năng máy chủ Amazon EC2.
* Xây dựng trang quản trị hệ thống Admin System Monitoring (`SystemMonitoringPage.tsx`) hiển thị các chỉ số HTTP Request Metrics, dung lượng bộ nhớ, tổng số người dùng và lưu lượng truy cập theo thời gian thực.
* Thực hiện kiểm thử toàn diện End-to-End (E2E Testing) toàn bộ quy trình của ứng dụng từ đăng ký, tạo khóa học, upload file S3, sinh Quiz bằng AI đến làm bài thi và chấm điểm.
* Đánh giá bảo mật (Security Audit), tối ưu hóa ngân sách vận hành AWS, hoàn thiện tài liệu dự án và đóng gói báo cáo kết thúc kỳ thực tập.

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Giám sát Hạ tầng Đám mây với AWS CloudWatch
* **AWS CloudWatch Logs:**
  * Tìm hiểu cách đẩy nhật ký hoạt động (Application Logs) từ Docker Container trên máy chủ EC2 về CloudWatch Log Group.
  * Sử dụng thư viện `@aws-sdk/client-cloudwatch-logs` hoặc Docker Log Driver (`awslogs`) để đồng bộ log tự động.
  * Kỹ thuật tìm kiếm và lọc nhật ký (Log Insights) để phát hiện nhanh các lỗi hệ thống (như lỗi 500, lỗi kết nối DB, timeout OpenAI API).
* **AWS CloudWatch Metrics & Alarms (Cảnh báo Sự cố):**
  * Theo dõi các chỉ số hạ tầng EC2: CPU Utilization, Network In/Out, Disk Read/Write.
  * Thiết lập CloudWatch Alarm: Cấu hình ngưỡng cảnh báo khi CPU EC2 vượt quá 85% liên tục trong 5 phút.
  * Tích hợp Amazon SNS (Simple Notification Service) để tự động gửi Email thông báo sự cố tức thì cho Quản trị viên (Admin).

#### B. Đánh giá Bảo mật & Tối ưu hóa Chi phí (Security Audit & Cost Optimization)
* **Kiểm tra Bảo mật Hạ tầng (Security Audit):**
  * Rà soát lại AWS Security Groups: Chỉ cho phép mở các cổng cần thiết (22, 80, 443, 5000), khóa các cổng quản trị DB công khai.
  * Đảm bảo S3 Bucket Private: Kiểm tra cấu hình Block Public Access trên S3 và xác nhận 100% file nhạy cảm đều dùng Presigned URL có thời gian hết hạn ngắn.
  * Kiểm tra GitHub Secrets: Xác nhận không có bất kỳ API Key (OpenAI, AWS Access Key, MongoDB URI) nào bị hardcode trực tiếp trong mã nguồn.
* **Tối ưu hóa Chi phí Vận hành (AWS Cost Explorer):**
  * Phân tích biểu đồ chi phí thực tế trên AWS Billing Dashboard.
  * Đảm bảo các tài nguyên EC2, S3, CloudFront nằm trong mức tối ưu của gói AWS Free Tier / ngân sách ước tính (chỉ từ 8,30 – 14,80 USD/tháng).

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Phát triển Module Ghi nhận Chỉ số Hệ thống Backend (`stats.service.js`):**
  * Viết Express Middleware `metrics.middleware.js`: Tự động đo thời gian phản hồi (Response Time ms), Status Code (2xx, 4xx, 5xx), Endpoint URL của mỗi HTTP Request và lưu vào `RequestMetric.model.js` trong MongoDB Atlas.
  * Viết các API dành riêng cho Admin:
    * `GET /api/stats/overview`: Thống kê tổng số Học viên, Giảng viên, Khóa học, Bài học và bài Quiz đã tạo.
    * `GET /api/stats/system-metrics`: Trả về biểu đồ lưu lượng Request theo giờ, thời gian phản hồi trung bình (Average Latency) và tỷ lệ lỗi hệ thống.

* **Dựng Trang Quản trị Giám sát Hệ thống (`SystemMonitoringPage.tsx`):**
  * Xây dựng giao diện Dashboard chuyên nghiệp cho Admin:
    * **Khối Thống kê Tổng quan (Metric Cards):** Tổng User, Khóa học hoạt động, Tổng lượt thi Quiz, Tổng dung lượng lưu trữ S3.
    * **Biểu đồ Lưu lượng HTTP Traffic (Line Chart / Bar Chart):** Hiển thị số lượng request thành công (200 OK) và request thất bại (4xx/5xx).
    * **Bảng Nhật ký Hoạt động Hệ thống (System Logs Table):** Hiển thị danh sách các sự kiện mới nhất và nhật ký lỗi từ CloudWatch.
  * **Trang Quản lý Người dùng (`AdminUsersPage.tsx`):** Cho phép Admin xem danh sách tài khoản, khóa/mở khóa tài khoản người dùng hoặc thay đổi phân quyền.

* **Kiểm thử Toàn diện End-to-End (E2E Testing) & Đóng gói Báo cáo:**
  * Thực hiện kịch bản kiểm thử toàn diện trên đường dẫn live CloudFront/EC2:
    * **KỊCH BẢN 1:** Giảng viên đăng ký tài khoản → Tạo khóa học mới → Upload video & tài liệu PDF lên AWS S3 qua Presigned URL → Yêu cầu AI trích xuất tài liệu → Sử dụng Question Builder sinh bài Quiz tự động bằng OpenAI API.
    * **KỊCH BẢN 2:** Sinh viên đăng nhập → Tìm kiếm khóa học → Xem bài giảng video → Đặt câu hỏi cho AI Tutor → Làm bài thi Quiz trực tuyến → Nhận kết quả chấm điểm tự động và cập nhật tiến độ 100%.
  * Cập nhật file `README.md` hướng dẫn chi tiết cách chạy dự án local và quy trình triển khai AWS CI/CD.
  * Đóng gói mã nguồn Clean Code và hoàn thiện Báo cáo Thực tập chính thức.

---

### 4. Kết quả đạt được (Deliverables)
* AWS CloudWatch Logs & Alarms được cấu hình hoàn chỉnh, tự động gửi Email cảnh báo khi máy chủ EC2 có dấu hiệu quá tải.
* Trang `SystemMonitoringPage.tsx` và `AdminUsersPage.tsx` chạy mượt mà, giúp Admin dễ dàng giám sát sức khỏe hệ thống và quản lý người dùng.
* Ứng dụng LearnSphere trải qua kiểm thử E2E thành công 100% trên hạ tầng AWS thực tế (S3, CloudFront, EC2, ECR, GitHub Actions, MongoDB Atlas, OpenAI API).
* Hoàn thiện bộ tài liệu hướng dẫn `README.md`, slide thuyết trình và Báo cáo Thực tập 8 tuần chính thức sẵn sàng cho việc bảo vệ/nghiệm thu dự án.
