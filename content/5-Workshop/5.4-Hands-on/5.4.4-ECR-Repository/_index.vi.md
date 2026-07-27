---
title: "Tạo Kho lưu trữ Amazon ECR"
date: 2026-07-27
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

# 5.4.4. Tạo Kho lưu trữ Amazon ECR (Elastic Container Registry)

Trong bước này, người thực hiện sẽ khởi tạo Private Repository trên **Amazon ECR** để quản lý các Docker Image của Backend và thiết lập quy tắc tự động xóa ảnh cũ (Lifecycle Policy).

---

### 1. Khởi tạo ECR Private Repository

1. Truy cập dịch vụ **Amazon ECR** $\rightarrow$ chọn **Repositories** $\rightarrow$ bấm **Create repository**.
2. **Visibility settings:** Chọn **Private**.
3. **Repository name:** Đặt tên `learnsphere-be`.
4. **Image scan settings:** Bật công tắc **Scan on push** (Tự động quét tìm lỗ hổng an ninh bảo mật CVE khi có Image mới được đẩy lên).
5. Bấm **Create repository**.

---

### 2. Thiết lập Lifecycle Policy Tự động xóa Image cũ

1. Mở Repository `learnsphere-be` $\rightarrow$ chọn mục **Lifecycle policies** bên danh sách trái $\rightarrow$ chọn **Create rule**.
2. **Rule priority:** `1`.
3. **Rule description:** `Keep last 10 tagged images`.
4. **Image status:** `Tagged`.
5. **Tag prefixes:** `latest` hoặc giữ trống để áp dụng cho tất cả tagged images.
6. **Match criteria:** Select `Image count more than` $\rightarrow$ Count: `10`.
7. Bấm **Save**.

---

### 3. Kiểm tra Lệnh Đăng nhập ECR qua AWS CLI

Sử dụng AWS CLI trên máy cá nhân để thử nghiệm lệnh đăng nhập vào ECR Registry:

```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 575620421319.dkr.ecr.ap-southeast-1.amazonaws.com
```

**Kết quả mong đợi:** Terminal hiển thị `Login Succeeded`.
