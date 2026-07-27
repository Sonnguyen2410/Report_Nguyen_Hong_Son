---
title: "Khởi tạo và Cấu hình Máy chủ EC2"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---

# 5.4.5. Khởi tạo và Cấu hình Máy chủ EC2 (Amazon Linux 2023 & Security Group)

Trong bước này, người thực hiện sẽ khởi tạo máy chủ **Amazon EC2**, gán IAM Role bảo mật, cấu hình Security Group khép kín và thiết lập bộ nhớ ảo Swap 2.0GB để chống tràn RAM.

---

### 1. Khởi tạo EC2 Instance

1. Truy cập dịch vụ **Amazon EC2** $\rightarrow$ chọn **Launch instance**.
2. **Name:** `LearnSphere-Backend-Server`.
3. **Application and OS Images (AMI):** Chọn **Amazon Linux 2023 AMI** 64-bit (x86).
4. **Instance type:** Chọn `t3.small` (2 vCPU, 2.0 GiB Memory).
5. **Key pair (login):** Chọn **Proceed without a key pair** (Do chúng ta sẽ quản trị máy chủ qua SSM Session Manager, không dùng SSH).
6. **Network settings:**
   - **VPC:** Select Default VPC.
   - **Auto-assign public IP:** `Enable`.
   - **Security Group:** Chọn Create Security Group `learnsphere-backend-sg`.
   - **Inbound Security Group Rules:** Thêm luật Custom TCP, Port `5000`, Source trỏ tới AWS Managed Prefix List `com.amazonaws.global.cloudfront.origin-facing` (hoặc tạm thời `0.0.0.0/0` trong quá trình setup). Xóa bỏ cổng 22.
7. **Advanced Details:**
   - **IAM instance profile:** Chọn Role `LearnSphereEc2Role`.
8. Bấm **Launch instance**.

---

### 2. Thiết lập Bộ nhớ RAM Swap 2.0GB trên EC2

Kết nối vào máy chủ EC2 qua **AWS SSM Session Manager** (trên AWS Console chọn **Connect** $\rightarrow$ **Session Manager**), sau đó chạy chuỗi lệnh khởi tạo tệp Swap:

```bash
# Tạo file swap dung lượng 2GB
sudo dd if=/dev/zero of=/swapfile bs=1M count=2048

# Cấp quyền siết chặt 600
sudo chmod 600 /swapfile

# Định dạng bộ nhớ swap
sudo mkswap /swapfile

# Kích hoạt bộ nhớ swap
sudo swapon /swapfile

# Kiểm tra bộ nhớ sau khi tạo
free -h

# Đăng ký cấu hình tự động kích hoạt khi khởi động lại
echo '/swapfile swap swap defaults 0 0' | sudo tee -a /etc/fstab
```

---

### 3. Cài đặt Docker & AWS CLI v2 trên EC2

Thực thi kịch bản cài đặt môi trường Docker trên Amazon Linux 2023:

```bash
# Cài đặt Docker
sudo dnf install -y docker

# Bật dịch vụ Docker tự động chạy khi khởi động
sudo systemctl enable --now docker

# Thêm user ec2-user và ssm-user vào nhóm docker
sudo usermod -aG docker ec2-user
sudo usermod -aG docker ssm-user
```
