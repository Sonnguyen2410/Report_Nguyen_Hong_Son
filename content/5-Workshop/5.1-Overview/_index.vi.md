---
title: "Giới thiệu"
date: 2026-07-27
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Giới thiệu tổng quan dự án LearnSphere

**LearnSphere** là một nền tảng học tập trực tuyến (E-Learning Platform) hiện đại, hỗ trợ toàn bộ quy trình dạy và học dành cho cả Giáo viên (Tutor) và Học viên (Student). Hệ thống được thiết kế theo cấu trúc Monorepo đơn giản, giúp quản lý đồng bộ mã nguồn và tối ưu hóa quy trình kiểm thử:

- **Frontend (`LearnSphere_FE`)**: Ứng dụng giao diện người dùng Single Page Application (SPA) phát triển bằng React.js, TypeScript và Vite, mang lại trải nghiệm mượt mà và tốc độ phản hồi nhanh.
- **Backend (`LearnSphere_BE`)**: Hệ thống dịch vụ RESTful API phát triển trên nền Node.js và Express.js, đảm nhận các tác vụ xử lý nghiệp vụ, quản lý phiên đăng nhập, xác thực quyền truy cập và kết nối với các mô hình trí tuệ nhân tạo (AI).
- **Cơ sở dữ liệu (MongoDB Atlas)**: Hệ quản trị cơ sở dữ liệu NoSQL dạng Document-oriented lưu trữ toàn bộ dữ liệu người dùng, cấu trúc khóa học, tiến trình học tập và bài thi Quiz.
- **Lưu trữ đối tượng (Amazon S3)**: Quản lý các tệp truyền thông dung lượng lớn như video bài giảng, tài liệu học tập PDF và hình ảnh khóa học.

![Sơ đồ Kiến trúc LearnSphere Production trên AWS](/images/LEARNSHPHERE.drawio.png)

---

### 🌐 Liên kết Dự án & Tài nguyên (Project Links & Resources)

| Tài nguyên | Liên kết (URL) | Mô tả chi tiết |
| --- | --- | --- |
| 🌐 **Website Sản phẩm (Production)** | [https://www.learnsphere.id.vn/](https://www.learnsphere.id.vn/) | Website ứng dụng LearnSphere chính thức đang vận hành thực tế trên AWS |
| 🐙 **GitHub Repository** | [https://github.com/HoiaeKHMT/LearnSphere](https://github.com/HoiaeKHMT/LearnSphere) | Mã nguồn dự án LearnSphere (Backend Express.js & Frontend React Monorepo) |
| 🎬 **Video Demo** | [Xem Video Demo trên Google Drive](https://drive.google.com/file/d/1J6heEzrB1jZO3C5Z3tuz1LBwdkRozMh4/view) | Video giới thiệu các tính năng và toàn bộ quy trình vận hành hệ thống |

---

### 2. Mục tiêu kỹ thuật của Bài Workshop

Mục tiêu chính của bài workshop này là hướng dẫn từng bước đưa ứng dụng LearnSphere từ môi trường máy cá nhân lên hệ thống hạ tầng đám mây **AWS khu vực Singapore (`ap-southeast-1`)** đạt chuẩn Production-Grade.

Sau khi hoàn thành bài workshop, người thực hiện sẽ làm chủ các tiêu chuẩn Cloud Native và DevOps cốt lõi:

* **Bảo mật Zero Static Credentials**: Loại bỏ hoàn toàn rủi ro rò rỉ Access Key/Secret Key dài hạn. Cấu hình GitHub Actions OIDC để lấy thông tin xác thực ngắn hạn từ AWS STS khi chạy pipeline, kết hợp gán IAM Instance Profile (IMDSv2) cho máy chủ EC2 để tự động truy cập dịch vụ AWS.
* **An toàn Mạng và Quản trị Không Cần SSH**: Thiết lập Security Group đóng hoàn toàn cổng kết nối SSH (Port 22) và các cổng public từ Internet. Việc điều khiển và quản trị máy chủ EC2 được thực hiện 100% qua kênh truyền mã hóa AWS Systems Manager (SSM) Session Manager.
* **Phân phối Nội dung Tối ưu qua CDN**: Triển khai Amazon CloudFront làm điểm truy cập HTTPS duy nhất cho toàn bộ hệ thống. Phân phối mã nguồn tĩnh Frontend từ S3 Private qua cơ chế Origin Access Control (OAC), đồng thời chuyển tiếp các truy vấn API `/api/*` về máy chủ EC2, loại bỏ triệt để lỗi CORS và Mixed Content. Đính kèm CloudFront Function xử lý SPA Routing để tránh lỗi 404 khi người dùng tải lại trang trên các đường dẫn phụ.
* **Tự động hóa CI/CD Zero-Downtime & Auto-Rollback**: Đóng gói Backend bằng Multi-stage Docker Build trên nền Linux Alpine chạy dưới quyền non-root. Tự động hóa pipeline triển khai: kiểm thử container thử nghiệm trên cổng tạm thời, thực hiện Health Check định kỳ, chỉ chuyển đổi sang container chính khi kiểm thử thành công và tự động hoàn tác (Rollback) về phiên bản cũ nếu gặp sự cố.
* **Giám sát Tập trung và Cảnh báo Chủ động**: Đẩy toàn bộ log ứng dụng về Amazon CloudWatch Logs tập trung. Khởi tạo CloudWatch Alarms theo dõi mức độ sử dụng CPU và trạng thái phần cứng/mạng của máy chủ, tích hợp Amazon SNS để gửi email cảnh báo tức thì tới người quản trị khi hệ thống xảy ra bất thường.

---

### 3. Bảng tổng hợp Cấu hình Kỹ thuật

| Thành phần | Công nghệ / Dịch vụ AWS | Vai trò & Chi tiết cấu hình |
| --- | --- | --- |
| **Mạng & Phân phối CDN** | Amazon CloudFront | Tối ưu hóa phân phối nội dung HTTPS, bảo mật dữ liệu tĩnh qua OAC, tự động điều hướng đường dẫn ứng dụng trang đơn React. |
| **Lưu trữ Frontend** | Amazon S3 Frontend | Lưu trữ các tệp tĩnh sau khi biên dịch React ở trạng thái Private hoàn toàn. |
| **Máy chủ Backend** | Amazon EC2 (`t3.small`) | Vận hành Docker Container ứng dụng Node.js/Express trên cổng nội bộ 5000, cấu hình bộ nhớ Swap 2GB phòng chống tràn RAM. |
| **Kho lưu trữ Container** | Amazon ECR | Lưu trữ các bản đóng gói Docker Image của Backend, bật tính năng tự động quét lỗ hổng bảo mật khi đẩy image mới. |
| **Lưu trữ Truyền thông** | Amazon S3 Media | Lưu trữ Video, PDF, Hình ảnh bài học. Toàn bộ thao tác tải lên và tải xuống bắt buộc thông qua Presigned URL có thời hạn được sinh từ Backend. |
| **Cơ sở dữ liệu** | MongoDB Atlas | Lưu trữ dữ liệu hệ thống dạng Document, kết nối an toàn từ EC2 qua chuỗi kết nối mã hóa SRV. |
| **Tự động hóa CI/CD** | GitHub Actions + OIDC | Tự động biên dịch, kiểm thử, đóng gói, triển khai và hoàn tác an toàn qua AWS Systems Manager. |
| **Giám sát & Cảnh báo** | CloudWatch Logs & Alarms + SNS | Quản lý log tập trung, tự động theo dõi hiệu năng hệ thống và gửi thông báo cảnh báo qua Email. |

---

### 4. Kết quả đạt được sau khi hoàn thành

Sau khi hoàn tất bài workshop, hệ thống LearnSphere sẽ vận hành hoàn chỉnh trên môi trường Production dưới tên miền chính thức **[https://www.learnsphere.id.vn/](https://www.learnsphere.id.vn/)** (kết nối qua CloudFront HTTPS Distribution). Mọi thao tác từ đăng ký, đăng nhập, tải bài giảng, xem video, làm bài thi Quiz đến tương tác với AI Assistant đều diễn ra tự động, bảo mật và có tính sẵn sàng cao.
