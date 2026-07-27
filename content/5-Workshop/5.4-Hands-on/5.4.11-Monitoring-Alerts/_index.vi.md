---
title: "Thiết lập Giám sát & Cảnh báo (CloudWatch & SNS)"
date: 2026-07-27
weight: 11
chapter: false
pre: " <b> 5.4.11. </b> "
---

Sau khi hệ thống đã đi vào vận hành trên Production, **Amazon CloudWatch** được cấu hình để tự động theo dõi tài nguyên máy chủ EC2 và phát cảnh báo tới người quản trị qua **Amazon SNS (Simple Notification Service)** khi xảy ra bất thường.

Toàn bộ các tài nguyên trong bước này được khởi tạo đồng bộ tại Region **Singapore (`ap-southeast-1`)**.

---

### 11.1. Tạo Amazon SNS Topic Cảnh báo

1. Mở **AWS Management Console** -> dịch vụ **Amazon SNS** -> **Topics** -> chọn **Create topic**.
2. **Type:** `Standard`.
3. **Name:** `LearnSphere-Alerts`.
4. Bấm **Create topic**.

---

### 11.2. Đăng ký Email Subscription & Xác nhận Cảnh báo

1. Trong trang chi tiết Topic `LearnSphere-Alerts`, chọn **Create subscription**:
   - **Protocol:** `Email`.
   - **Endpoint:** Nhập địa chỉ email người quản trị (ví dụ `son.nguyenhong2410@hcmut.edu.vn`).
2. Bấm **Create subscription**.
3. Mở Hộp thư Email cá nhân -> Mở thư thông báo từ **AWS Notifications** -> Bấm nút **Confirm subscription**.
4. Kiểm tra trang điều khiển SNS đảm bảo trạng thái Subscription hiển thị mã ARN hợp lệ và không còn ở trạng thái `Pending confirmation`.

![Kênh SNS gửi cảnh báo vận hành LearnSphere qua email](/images/5-Workshop/5.4/5.4.11.2.png)
<p align="center"><i>Hình 5.4.11.2 — Kênh SNS Topic phát thông báo cảnh báo vận hành tới email đã xác nhận.</i></p>

---

### 11.3. Tạo CloudWatch Alarm 1 — EC2 CPUUtilization > 80% trong 10 phút

1. Mở **CloudWatch Console** -> **Alarms** -> **All alarms** -> chọn **Create alarm**.
2. **Select metric:** Chọn `EC2` -> `Per-Instance Metrics` -> chọn metric `CPUUtilization` của EC2 Instance ID `i-008c48e6c120b2978`.
3. Cấu hình điều khiển:
   - **Statistic:** `Average`
   - **Period:** `5 minutes`
   - **Threshold type:** `Static`
   - **Whenever CPUUtilization is:** `Greater than 80%`
   - **Datapoints to alarm:** `2 out of 2` (Cảnh báo khi CPU vượt 80% liên tục trong 2 chu kỳ 5 phút = 10 phút)
4. **Configure actions:** Chọn gửi notification tới SNS Topic `LearnSphere-Alerts`.
5. **Alarm name:** `LearnSphere-EC2-HighCPU`.
6. Bấm **Create alarm**.

![CloudWatch theo dõi CPU EC2 và cảnh báo khi vượt 80% trong 10 phút](/images/5-Workshop/5.4/5.4.11.3.png)
<p align="center"><i>Hình 5.4.11.3 — CloudWatch Alarm theo dõi CPU EC2 và phát cảnh báo khi vượt ngưỡng 80%.</i></p>

---

### 11.4. Tạo CloudWatch Alarm 2 — EC2 StatusCheckFailed >= 1 trong 60 giây

1. Chọn **Create alarm** -> Select metric `StatusCheckFailed` của EC2 Instance ID `i-008c48e6c120b2978`.
2. Cấu hình điều khiển:
   - **Statistic:** `Maximum`
   - **Period:** `1 minute`
   - **Threshold type:** `Static`
   - **Whenever StatusCheckFailed is:** `Greater than or equal to 1`
   - **Datapoints to alarm:** `1 out of 1` (Kích hoạt ngay lập tức trong 60 giây khi phần cứng hoặc mạng máy chủ lỗi)
3. **Notification Action:** Gửi thông báo tới SNS Topic `LearnSphere-Alerts`.
4. **Alarm name:** `LearnSphere-EC2-StatusCheckFailed`.
5. Bấm **Create alarm**.

![CloudWatch phát hiện lỗi trạng thái EC2 và gửi thông báo qua SNS](/images/5-Workshop/5.4/5.4.11.4.png)
<p align="center"><i>Hình 5.4.11.4 — CloudWatch Alarm phát hiện sự cố máy chủ EC2 và phát tín hiệu khẩn cấp qua SNS.</i></p>

---

### 11.5. Kiểm tra Kết quả Giám sát & Vận hành

Mở **CloudWatch** -> **Alarms** -> **All alarms**, xác nhận cả 2 Alarm `LearnSphere-EC2-HighCPU` và `LearnSphere-EC2-StatusCheckFailed` đang ở trạng thái **OK**. Hệ thống sẵn sàng tự động phát thông báo qua email bất cứ khi nào hạ tầng xảy ra sự cố.
