---
title: "Tạo và Cấu hình Amazon S3"
date: 2026-07-27
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

Hệ thống LearnSphere sử dụng hai S3 Bucket riêng biệt để phân tách hoàn toàn giữa dữ liệu người dùng tải lên và mã nguồn giao diện tĩnh Frontend.

---

### 3.1. Tạo S3 Bucket 1 — Lưu dữ liệu Media (`learnsphere-media-575620421319`)

Bucket này đảm nhận việc lưu trữ toàn bộ các tệp truyền thông của hệ thống:
- Video bài giảng.
- Tài liệu học tập (PDF, DOCX).
- Thumbnail ảnh bìa khóa học.
- Avatar hình đại diện người dùng.

#### Thao tác cấu hình:

1. Truy cập dịch vụ **Amazon S3** -> chọn **Create bucket**.
2. **Bucket name:** `learnsphere-media-575620421319` (duy nhất trên toàn cầu).
3. **Region:** `ap-southeast-1` (Singapore).
4. **Block Public Access:** Bật công tắc **Block all public access = ON** (Khóa toàn bộ truy cập công khai).
5. **Static Website Hosting:** Giữ ở trạng thái `Disabled` (Tắt).
6. Bấm **Create bucket**.

![Tạo S3 Media Bucket learnsphere-media](/images/5-Workshop/5.4/5.4.3.1.1.png)
<p align="center"><i>Hình 5.4.3.1a — Tạo S3 Bucket riêng tư lưu trữ dữ liệu Media.</i></p>

#### Cấu hình CORS (Cross-Origin Resource Sharing):

Do Frontend React chạy trên tên miền CloudFront cần thực hiện tải video/tài liệu trực tiếp lên S3 qua cơ chế Presigned URL, chúng ta cần cấu hình CORS cho S3 Media Bucket:

1. Mở Bucket `learnsphere-media-575620421319` -> chuyển sang tab **Permissions**.
2. Kéo xuống mục **Cross-origin resource sharing (CORS)** -> chọn **Edit** và dán cấu hình JSON:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT"],
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://d2onzy56n3iw1w.cloudfront.net"
    ],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 3600
  }
]
```

> **Lưu ý quan trọng:** Thông số `ExposeHeaders: ["ETag"]` là bắt buộc để trình duyệt đọc được mã băm ETag, phục vụ quá trình hoàn tất ghép tệp khi tải lên video dung lượng lớn (Multipart Upload).

![Cấu hình CORS để trình duyệt upload/download media bằng presigned URL](/images/5-Workshop/5.4/5.4.3.1.2.png)
<p align="center"><i>Hình 5.4.3.1b — Cấu hình CORS để trình duyệt upload/download media bằng Presigned URL.</i></p>

---

### 3.2. Tạo S3 Bucket 2 — Chứa mã nguồn Frontend (`learnsphere-fe-575620421319`)

Bucket này chuyên dùng để lưu trữ các tệp tĩnh sau khi biên dịch React SPA (`index.html`, JavaScript, CSS, hình ảnh giao diện).

1. Chọn **Create bucket**.
2. **Bucket name:** `learnsphere-fe-575620421319`.
3. **Region:** `ap-southeast-1` (Singapore).
4. **Block Public Access:** Giữ **Block all public access = ON**.
5. **Static Website Hosting:** Giữ ở trạng thái `Disabled` (Tắt).
6. Bấm **Create bucket**.

![S3 bucket riêng tư dùng để lưu bản build Frontend](/images/5-Workshop/5.4/5.4.3.2.png)
<p align="center"><i>Hình 5.4.3.2 — S3 Bucket riêng tư dùng để lưu các tệp tĩnh bản build Frontend.</i></p>

> **Lưu ý:** Bucket này giữ ở trạng thái Private 100%. Phân quyền truy cập sẽ được cấp duy nhất cho CloudFront Origin Access Control (OAC) ở **Bước 7A**.
