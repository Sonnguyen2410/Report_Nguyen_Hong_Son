---
title: "Tạo và Cấu hình Amazon S3"
date: 2026-07-27
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

# 5.4.3. Tạo và Cấu hình Amazon S3 (Buckets Frontend & Media)

Trong bước này, người thực hiện sẽ khởi tạo 2 S3 Buckets riêng biệt với cấu hình bảo mật siết chặt: **S3 Frontend Bucket** (lưu mã nguồn tĩnh React) và **S3 Media Bucket** (lưu bài giảng video/PDF/hình ảnh).

---

### 1. Khởi tạo S3 Bucket 1 (Frontend Static Hosting)

1. Truy cập dịch vụ **Amazon S3** $\rightarrow$ chọn **Create bucket**.
2. Đặt tên Bucket: `learnsphere-fe-575620421319` (tên phải là duy nhất trên toàn cầu).
3. **Region:** Chọn `ap-southeast-1` (Singapore).
4. **Block Public Access:** Giữ nguyên thiết lập **Block all public access = ON** (Khóa toàn bộ truy cập công khai).
5. Bấm **Create bucket**.

---

### 2. Khởi tạo S3 Bucket 2 (Media Storage)

1. Chọn **Create bucket**.
2. Đặt tên Bucket: `learnsphere-media-575620421319`.
3. **Region:** `ap-southeast-1` (Singapore).
4. **Block Public Access:** Giữ nguyên **Block all public access = ON**.
5. Bấm **Create bucket**.

---

### 3. Cấu hình CORS cho S3 Media Bucket

1. Mở Bucket `learnsphere-media-575620421319` $\rightarrow$ chuyển sang tab **Permissions**.
2. Kéo xuống mục **Cross-origin resource sharing (CORS)** $\rightarrow$ chọn **Edit**.
3. Dán đoạn cấu hình JSON cho phép trình duyệt tải file Multipart Upload trực tiếp:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "HEAD"],
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://d2onzy56n3iw1w.cloudfront.net"
    ],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 3600
  }
]
```

4. Bấm **Save changes**.
