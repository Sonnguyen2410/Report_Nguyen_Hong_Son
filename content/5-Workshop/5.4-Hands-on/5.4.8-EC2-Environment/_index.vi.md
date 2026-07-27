---
title: "Cấu hình Môi trường Backend trên EC2"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 5.4.8. </b> "
---

# 5.4.8. Cấu hình Môi trường Backend trên Máy chủ EC2

Trong bước này, người thực hiện sẽ khởi tạo tệp cấu hình chứa các biến môi trường Production (`/home/ec2-user/.env`) trên máy chủ EC2 để ứng dụng Backend tự động đọc thông số khi container khởi chạy.

---

### 1. Tạo tệp `.env` Production trên EC2

Kết nối vào máy chủ EC2 qua **AWS SSM Session Manager**, khởi tạo tệp `.env` tại thư mục cá nhân:

```bash
cat << 'EOF' > /home/ec2-user/.env
PORT=5000
NODE_ENV=production
TRUST_PROXY=true
MONGODB_URI=mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
JWT_SECRET=c84ac761c5224c53b96ad34fc94a8194c84ac761c5224c53b96ad34fc94a8194
FRONTEND_URL=https://d2onzy56n3iw1w.cloudfront.net
AWS_REGION=ap-southeast-1
AWS_S3_BUCKET=learnsphere-media-575620421319
GROQ_API_KEY=gsk_learnsphere_ai_inference_key_sample
EOF
```

---

### 2. Thiết lập Phân quyền Bảo mật Tệp Biến Môi trường

Siết chặt phân quyền tệp `.env` chỉ cho phép người dùng hệ thống có quyền đọc/ghi:

```bash
# Phân quyền 600
chmod 600 /home/ec2-user/.env

# Đảm bảo SSM Agent có quyền đọc tệp khi chạy lệnh deploy tự động
sudo chmod 644 /home/ec2-user/.env
```

---

### 3. Kiểm tra Trạng thái Kết nối ECR & Docker Daemon

Thực thi câu lệnh kéo (pull) thử nghiệm từ ECR để xác minh IAM Instance Profile của EC2 đã có đủ quyền:

```bash
# Đăng nhập ECR
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 575620421319.dkr.ecr.ap-southeast-1.amazonaws.com

# Kiểm tra Docker Daemon đang lắng nghe
docker ps
```

**Kết quả mong đợi:** Docker Daemon sẵn sàng tiếp nhận lệnh khởi chạy container từ pipeline CI/CD.
