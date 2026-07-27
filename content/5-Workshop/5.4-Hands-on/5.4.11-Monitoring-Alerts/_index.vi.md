---
title: "Thiết lập Giám sát & Cảnh báo (CloudWatch & SNS)"
date: 2026-07-27
weight: 11
chapter: false
pre: " <b> 5.4.11. </b> "
---

# 5.4.11. Thiết lập Giám sát & Cảnh báo (CloudWatch & SNS)

Trong bước này, người thực hiện sẽ cấu hình bộ gom log tập trung trên **Amazon CloudWatch Logs**, khởi tạo **CloudWatch Alarms** và tích hợp **Amazon SNS Topic** để gửi email cảnh báo tự động khi máy chủ gặp sự cố hoặc quá tải CPU.

---

### 1. Tạo Amazon SNS Topic & Xác thực Email Subscription

1. Truy cập dịch vụ **Amazon Simple Notification Service (SNS)** $\rightarrow$ chọn **Topics** $\rightarrow$ chọn **Create topic**.
2. **Type:** `Standard`.
3. **Name:** `LearnSphere-Alerts`.
4. Bấm **Create topic**.
5. Trong trang chi tiết Topic $\rightarrow$ chọn **Create subscription**:
   - **Protocol:** Select `Email`.
   - **Endpoint:** Nhập địa chỉ Email nhận cảnh báo của bạn (ví dụ `son.nguyenhong2410@hcmut.edu.vn`).
   - Bấm **Create subscription**.
6. Mở Hộp thư Email cá nhân $\rightarrow$ Mở thư từ AWS Notifications $\rightarrow$ Bấm nút **Confirm subscription**.

---

### 2. Cấu hình CloudWatch Log Group Tập trung

1. Truy cập dịch vụ **Amazon CloudWatch** $\rightarrow$ mục **Logs** $\rightarrow$ chọn **Log groups**.
2. Chọn **Create log group**.
3. **Log group name:** `/learnsphere/backend`.
4. **Retention setting:** `30 days`.
5. Bấm **Create**.

---

### 3. Tạo CloudWatch Alarm 1 (EC2 High CPU Utilization)

1. Tại CloudWatch Console $\rightarrow$ chọn **Alarms** $\rightarrow$ chọn **Create alarm**.
2. Select metric $\rightarrow$ **EC2** $\rightarrow$ **Per-Instance Metrics** $\rightarrow$ chọn metric `CPUUtilization` của Instance ID EC2 Backend.
3. **Statistic:** `Average`, **Period:** `5 minutes`.
4. **Threshold type:** `Static` $\rightarrow$ **Whenever CPUUtilization is...** `Greater than 80%`.
5. **Datapoints to alarm:** `2 out of 2` (Cảnh báo khi vượt 80% CPU trong 10 phút).
6. **Configure actions:** Select SNS Topic `LearnSphere-Alerts`.
7. **Alarm name:** `LearnSphere-EC2-HighCPU`.
8. Bấm **Create alarm**.

---

### 4. Tạo CloudWatch Alarm 2 (EC2 Status Check Failed)

1. Chọn **Create alarm** $\rightarrow$ Select metric `StatusCheckFailed`.
2. **Statistic:** `Maximum`, **Period:** `1 minute`.
3. **Condition:** `Greater than or equal to 1`.
4. **Datapoints to alarm:** `1 out of 1` (Kích hoạt ngay lập tức trong 60s khi máy chủ sự cố).
5. **Notification Action:** Gửi thông báo tới SNS Topic `LearnSphere-Alerts`.
6. **Alarm name:** `LearnSphere-EC2-StatusCheckFailed`.
7. Bấm **Create alarm**.
