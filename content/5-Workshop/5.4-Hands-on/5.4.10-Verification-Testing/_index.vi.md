---
title: "Kiểm tra & Trải nghiệm Sản phẩm Production"
date: 2026-07-27
weight: 10
chapter: false
pre: " <b> 5.4.10. </b> "
---

Sau khi quy trình CI/CD hoàn tất, người thực hiện tiến hành kiểm tra toàn bộ container, log tập trung, kết nối cơ sở dữ liệu và trải nghiệm sản phẩm trực tiếp trên tên miền Production HTTPS.

---

### 10.1. Kiểm tra Trạng thái Container Backend trên EC2

Kết nối vào máy chủ EC2 qua Session Manager và chạy lệnh kiểm tra container:

```bash
sudo docker ps --filter name=learnsphere-be
```

Xác minh chi tiết trạng thái sức khỏe của container:

```bash
sudo docker inspect \
  --format 'status={{.State.Status}} health={{.State.Health.Status}} restarts={{.RestartCount}} image={{.Config.Image}}' \
  learnsphere-be
```

**Kết quả mong đợi:**
```text
status=running
health=healthy
restarts=0
```

---

### 10.2. Kiểm tra Kết nối Database qua Endpoint Health Check

Chạy lệnh gọi endpoint kiểm tra kết nối tới cơ sở dữ liệu MongoDB Atlas:

```bash
curl -fsS http://127.0.0.1:5000/health/ready
```

**Kết quả trả về:**
```json
{
  "status": "ready",
  "database": "connected"
}
```

![Backend container hoạt động ổn định và kết nối MongoDB thành công](/images/5-Workshop/5.4/5.4.10.2.png)
<p align="center"><i>Hình 5.4.10.2 — Container Backend hoạt động ổn định và kết nối MongoDB Atlas thành công.</i></p>

---

### 10.3. Kiểm tra CloudWatch Logs tập trung

Mở **Amazon CloudWatch** -> **Log groups** -> chọn `/learnsphere/backend`.

Xác nhận:
- Server Node.js khởi động thành công trên cổng 5000.
- MongoDB Atlas kết nối ổn định.
- Không lặp lại bất kỳ lỗi crash/restart nào.
- Nhận đầy đủ HTTP request log được chuyển tiếp từ CloudFront.

![Log Backend được tập trung trên Amazon CloudWatch](/images/5-Workshop/5.4/5.4.10.3.png)
<p align="center"><i>Hình 5.4.10.3 — Nhật ký hệ thống Log Stream Backend được tập trung trên CloudWatch Log Groups.</i></p>

---

### 10.4. Trải nghiệm Sản phẩm trên Tên miền Production

Truy cập trực tiếp tên miền sản phẩm chính thức:

```text
https://www.learnsphere.id.vn/
(Hoặc đường dẫn CloudFront HTTPS: https://d2onzy56n3iw1w.cloudfront.net)
```

#### Thực hiện các bài kiểm thử tính năng đầu-cuối (End-to-End Testing):
1. **Đăng ký / Đăng nhập:** Đăng ký tài khoản mới và đăng nhập nhận JWT Token.
2. **Quản lý Khóa học:** Đăng nhập tài khoản Giáo viên (Tutor) -> Tạo khóa học mới.
3. **Upload Media Presigned URL:** Tải lên video bài giảng, thumbnail và tài liệu PDF. Xác minh trình duyệt nhận Presigned PUT URL và upload trực tiếp lên S3 Media Bucket mà không bị chặn CORS.
4. **Học tập & Stream Video:** Đăng nhập tài khoản Học viên (Student) -> Xem bài học -> Trình duyệt phát video trực tiếp từ S3 qua Presigned GET URL.
5. **Thi Quiz & AI Assistant:** Thực hiện bài thi Quiz trắc nghiệm và tương tác hỏi đáp trực tiếp với AI Assistant.

![Sản phẩm LearnSphere sau khi triển khai hoàn chỉnh lên AWS](/images/5-Workshop/5.4/5.4.10.4.png)
<p align="center"><i>Hình 5.4.10.4 — Trải nghiệm ứng dụng LearnSphere vận hành hoàn chỉnh trên hạ tầng AWS Production.</i></p>
