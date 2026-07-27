---
title: "Kiểm tra & Trải nghiệm Sản phẩm Production"
date: 2026-07-27
weight: 10
chapter: false
pre: " <b> 5.4.10. </b> "
---

# 5.4.10. Kiểm tra & Trải nghiệm Sản phẩm Production

Trong bước này, người thực hiện sẽ truy cập ứng dụng LearnSphere chính thức trên môi trường Production qua miền CloudFront HTTPS và thực hiện bài kiểm thử đầu-cuối (End-to-End Verification).

---

### 1. Truy cập Tên miền CloudFront HTTPS

1. Mở trình duyệt web và truy cập địa chỉ CloudFront Distribution: `https://d2onzy56n3iw1w.cloudfront.net`.
2. Kiểm tra chứng chỉ **TLS/SSL Padlock** trên trình duyệt thông báo kết nối an toàn HTTPS.

---

### 2. Kiểm thử Luồng Đăng nhập & Đăng ký (Authentication Verification)

1. Thực hiện Đăng ký tài khoản Học viên mới.
2. Kiểm tra yêu cầu API `/api/v1/auth/register` được chuyển tiếp thành công về EC2 Backend.
3. Đăng nhập và xác minh chuỗi JWT Token lưu giữ an toàn tại LocalStorage.

---

### 3. Kiểm thử Tải lên & Xem Bài giảng Video qua Presigned URL

1. Đăng nhập tài khoản Giáo viên $\rightarrow$ Tạo khóa học mới.
2. Thử nghiệm tải tệp Video bài giảng $\rightarrow$ Xác minh trình duyệt gọi API lấy Presigned PUT URL và tải trực tiếp lên S3 Media Bucket mà không bị chặn lỗi CORS.
3. Đăng nhập tài khoản Học viên $\rightarrow$ Bấm xem bài học $\rightarrow$ Trình duyệt nhận Presigned GET URL và phát video dạng Stream mượt mà.

---

### 4. Kiểm thử F5 Reload trên Sub-routes (SPA Routing Check)

1. Truy cập trực tiếp đường dẫn phụ: `https://d2onzy56n3iw1w.cloudfront.net/courses`.
2. Nhấn phím **F5** để tải lại trang.
3. **Kết quả mong đợi:** CloudFront Function can thiệp sửa đổi URI thành `/index.html`, React Router hiển thị đúng trang thông tin khóa học mà không bị lỗi 404 Not Found từ S3.
