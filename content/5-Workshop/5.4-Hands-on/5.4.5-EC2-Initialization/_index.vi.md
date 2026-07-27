---
title: "Khởi tạo và Cấu hình Máy chủ EC2"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---

Trong bước này, người thực hiện sẽ khởi tạo máy chủ **Amazon EC2**, gán IAM Instance Profile, cài đặt Docker Engine và thiết lập bộ nhớ ảo Swap RAM 2GB.

---

### 5.1. Khởi tạo EC2 Instance

1. Mở **AWS Management Console** -> dịch vụ **Amazon EC2** -> chọn **Launch instance**.
2. **Name:** `LearnSphere-Backend-Server`.
3. Thông số kỹ thuật lựa chọn:

| Thuộc tính | Giá trị |
|---|---|
| AMI | Amazon Linux 2023 64-bit (x86) |
| Instance type | `t3.small` (2 vCPU, 2.0 GiB RAM) |
| Key pair (login) | **Proceed without a key pair** (Quản trị 100% qua Systems Manager) |
| IAM instance profile | `LearnSphereEc2Role` |
| Instance ID | `i-008c48e6c120b2978` |

4. **Network settings:**
   - **Security Group:** Tạo Security Group mới `learnsphere-backend-sg`.
   - **Inbound Rules:** Thêm luật Custom TCP, Port `5000`, Source trỏ tới AWS Managed Prefix List `com.amazonaws.global.cloudfront.origin-facing` (chỉ cho phép CloudFront gửi request tới port 5000).
   - **SSH Port 22:** Xóa bỏ luật SSH Port 22.
5. Bấm **Launch instance**.

![Khởi tạo EC2 instance chạy Backend LearnSphere](/images/5-Workshop/5.4/5.4.5.1.png)
<p align="center"><i>Hình 5.4.5.1 — Khởi tạo máy chủ EC2 t3.small gắn IAM Role và Security Group bảo mật.</i></p>

---

### 5.2. Cài đặt Docker Engine & Cấu hình Swap RAM 2GB

Kết nối tới EC2 qua **AWS SSM Session Manager** (trên console chọn **Connect** -> **Session Manager**), thực thi kịch bản cài đặt:

```bash
# Cập nhật hệ thống và cài đặt Docker
sudo yum update -y
sudo yum install -y docker

# Khởi chạy dịch vụ Docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user

# Kiểm tra phiên bản Docker & AWS CLI
docker --version
aws --version
```

#### Thiết lập 2.0GB Swap RAM phòng chống tràn bộ nhớ:

Do Backend Node.js thực hiện xử lý tài liệu PDF và nhận diện hình ảnh OCR, máy chủ được bổ sung 2.0GB tệp bộ nhớ Swap:

```bash
# Khởi tạo tệp swap 2GB
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Cấu hình tự động mount khi khởi động lại
echo '/swapfile swap swap defaults 0 0' | sudo tee -a /etc/fstab

# Kiểm tra dung lượng bộ nhớ
free -h
```

> **Kết quả:** Lệnh `free -h` hiển thị khoảng 1.9 GB RAM vật lý và 2.0 GB Swap sẵn sàng sử dụng.

![Môi trường Docker và tài nguyên bộ nhớ Swap trên EC2](/images/5-Workshop/5.4/5.4.5.2.png)
<p align="center"><i>Hình 5.4.5.2 — Môi trường Docker Engine và tài nguyên bộ nhớ ảo Swap 2.0GB được cấu hình thành công.</i></p>

---

### 5.3. Xác nhận IAM Instance Profile & Connection

Kiểm tra trong EC2 Console:
- IAM Role đã đính kèm chính xác là `LearnSphereEc2Role`.
- Máy chủ đã hiển thị trạng thái `Managed` trên AWS Systems Manager.
- Kết nối thành công bằng **Session Manager** mà không cần mở cổng 22 SSH.
