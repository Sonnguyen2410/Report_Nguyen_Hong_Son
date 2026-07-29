---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# [AWS ARCHITECTURE] PHÂN TÍCH KIẾN TRÚC HỆ THỐNG PLATFORM HỌC TẬP TÍCH HỢP AI (LEARNSPHERE)

Chào mọi người trong cộng đồng AWS Study Group,

Hôm nay mình xin chia sẻ bài viết phân tích chi tiết sơ đồ kiến trúc hệ thống backend & frontend cho dự án LearnSphere / AI Learning Platform mà nhóm mình vừa thiết kế.

Khác với các bài viết lý thuyết thông thường, kiến trúc này kết hợp giữa Serverless Static Hosting, Containerized Backend trên EC2, pipeline CI/CD tự động và các dịch vụ AI/Database external.

---

### 1. Tổng quan về Kiến trúc Hệ thống

Hệ thống được triển khai trên AWS Cloud tại Region Singapore (`ap-southeast-1`), đảm bảo độ trễ thấp cho người dùng khu vực Đông Nam Á. Kiến trúc được chia thành 4 thành phần chính:

- **Frontend Hosting:** Amazon S3 + Amazon CloudFront (CDN)
- **Backend Services:** Amazon EC2 (trong VPC / Public Subnet) + Amazon ECR
- **CI/CD Pipeline & Monitoring:** GitHub Actions + CloudWatch
- **External Services Integration:** OpenAI API + MongoDB Atlas + S3 Storage

---

### 2. Phân tích chi tiết các thành phần & Luồng hoạt động

#### A. Frontend Layer (Lớp giao diện)
- **Storage:** Frontend tĩnh (React/Next.js/Vue) được lưu trữ tại S3 Bucket: `learnsphere-fe-static`.
- **Content Delivery Network (CDN):** Amazon CloudFront đóng vai trò là CDN đứng trước S3 bucket.
- **Luồng truy cập:** Khi USER truy cập website, request sẽ qua CloudFront để lấy dữ liệu tĩnh từ S3. Điều này giúp tối ưu tốc độ tải trang (caching ở Edge Location) và giảm thiểu chi phí băng thông trực tiếp từ S3.

#### B. Backend Layer (Lớp xử lý ứng dụng)
- **VPC & Subnet:** Hệ thống khởi tạo một VPC chứa Availability Zone (`ap-southeast-1`) và một Public Subnet.
- **Compute:** Backend ứng dụng chạy trên Amazon EC2 Instance nằm trong Public Subnet, kết nối với Internet thông qua Internet Gateway.
- **Container Registry:** Các Docker Image của backend được lưu trữ và quản lý tập trung trên Amazon ECR (Docker Registry). Khi có phiên bản mới, EC2 Instance sẽ pull Docker Image từ ECR về để thực thi.

#### C. CI/CD & Monitoring (Tự động hóa & Giám sát)
- **GitHub Actions:** Đóng vai trò là trung tâm CI/CD:
  - Khi có code mới, GitHub Actions tự động Build & Push Docker Image lên Amazon ECR.
  - Tự động trigger/deploy ứng dụng lên EC2 Instance.
  - Cập nhật các tài nguyên/assets tĩnh lên S3 buckets.
- **CloudWatch (Logs & Alarms):** EC2 Instance đẩy toàn bộ log ứng dụng và chỉ số hệ thống về Amazon CloudWatch để phục vụ việc giám sát, cảnh báo sự cố và truy vết lỗi.

#### D. Data & Third-Party Integration (Dữ liệu & Tích hợp dịch vụ ngoài)
EC2 Backend xử lý logic và tương tác trực tiếp với các dịch vụ bổ trợ:
- **Storage Bucket (`ai-learning-platform-vhd`):** S3 Bucket riêng biệt dùng để lưu trữ dữ liệu media, tệp tin bài học hoặc VHD/file của nền tảng AI.
- **OpenAI API:** Backend gửi request đến OpenAI API để xử lý các tính năng thông minh (như AI Tutor, sinh câu hỏi, phân tích nội dung học tập).
- **MongoDB Atlas:** Database NoSQL được quản lý hoàn toàn (Fully Managed) nằm ngoài AWS, backend EC2 kết nối trực tiếp để truy vấn và lưu trữ dữ liệu người dùng/khoá học.

---

### 3. Lợi ích & Điểm mạnh của Kiến trúc này

- **Tách biệt Frontend & Backend (Decoupled Architecture):** Frontend phục vụ qua CloudFront/S3 giúp trang web tải cực nhanh, độc lập hoàn toàn với server backend.
- **Quy trình CI/CD hoàn chỉnh:** Sử dụng GitHub Actions kết hợp ECR giúp đóng gói ứng dụng dạng Docker Container, đảm bảo môi trường Dev/Staging/Prod nhất quán và deployment tự động 100%.
- **Linh hoạt tích hợp AI & Managed Services:** Kết hợp sức mạnh của OpenAI cho tính năng thông minh và MongoDB Atlas cho quản lý dữ liệu linh hoạt mà không mất công vận hành database cluster trên EC2.

---

### 4. Hướng phát triển & Tối ưu tiếp theo (Suggestions)

Nhóm mình cũng đang cân nhắc một số điểm tối ưu cho các phiên bản tiếp theo:
- **Security:** Chuyển EC2 Instance vào Private Subnet và dùng Application Load Balancer (ALB) ở Public Subnet để tăng cường bảo mật.
- **Scalability:** Đổi EC2 đơn lẻ sang Auto Scaling Group hoặc dịch vụ AWS ECS / EKS khi lượng user tăng cao.
- **Caching Database:** Tích hợp Amazon ElastiCache (Redis) để cache kết quả API từ OpenAI / MongoDB nhằm giảm chi phí call API và tăng tốc độ phản hồi.

---

### 📷 Sơ đồ Kiến trúc Hệ thống (Architecture Diagram)

![Sơ đồ Kiến trúc LearnSphere AWS Architecture](/images/LEARNSHPHERE.drawio.png)

---

Mọi người trong group có nhận xét hoặc đóng góp gì cho mô hình kiến trúc dự án LearnSphere này của nhóm mình không? Rất mong nhận được phản hồi và trao đổi từ anh em!

**Nguồn bài viết trên Facebook:** [AWS Study Group Post](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222081271890166/)
