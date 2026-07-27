---
title: "Worklog Tuần 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---
## Chủ đề: Làm quen với AWS VPC, IAM và Thiết kế Database Schema cho LearnSphere

### 1. Mục tiêu tuần 1
* Nắm vững kiến thức nền tảng về mạng đám mây AWS VPC, cơ chế bảo mật AWS IAM và cách kết nối MongoDB Atlas.
* Hoàn thành thiết kế cấu trúc dữ liệu (Database Schema) gồm 11 Mongoose Models cho hệ thống LearnSphere.
* Chuẩn hóa tài liệu thiết kế API (`API_DESIGN.md`) và khởi tạo cấu trúc thư mục dự án (Monorepo setup).

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **AWS VPC (Virtual Private Cloud):**
  * Khái niệm mạng ảo cô lập trên AWS Cloud và cách chia subnet.
  * Phân biệt **Public Subnet** (cho phép truy cập Internet qua Internet Gateway) và **Private Subnet** (dành cho Database/Internal Services).
  * Tìm hiểu cách cấu hình **Internet Gateway (IGW)** và **Route Table** để điều hướng traffic.
* **Security Groups (Firewall):**
  * Học cách tạo và thiết lập luật cho phép truy cập (Inbound/Outbound Rules) cho các cổng: 22 (SSH), 80/443 (HTTP/HTTPS), 5000 (Backend API).
* **AWS IAM (Identity and Access Management):**
  * Khái niệm IAM User, IAM Group, IAM Role và IAM Policy.
  * Nguyên tắc cấp quyền tối thiểu (**Principle of Least Privilege**).
  * Học cách tạo Access Key / Secret Access Key an toàn cho ứng dụng giao tiếp với dịch vụ AWS (như S3).

#### B. Cơ sở dữ liệu & Kiến trúc API (Database & API Design)
* **MongoDB Atlas & Mongoose ODMs:**
  * Tìm hiểu mô hình cơ sở dữ liệu NoSQL, cách khởi tạo Cluster M0 miễn phí trên MongoDB Atlas.
  * Học cách cấu hình Network Access (IP Whitelist) kết nối an toàn từ Node.js server đến Atlas.
  * Cách định nghĩa Schema, Indexes và Relationships giữa các collection trong Mongoose.
* **Chuẩn RESTful API:**
  * Các quy tắc đặt tên Endpoint, HTTP Verbs (GET, POST, PUT, PATCH, DELETE).
  * Chuẩn hóa cấu trúc JSON Response (Success, Error Handling, HTTP Status Codes).

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Khởi tạo và cấu hình môi trường dự án:**
  * Tạo thư mục gốc `LearnSphere` chứa 2 ứng dụng độc lập: `LearnSphere_BE` (Backend Node.js/Express) và `LearnSphere_FE` (Frontend React/Vite/Tailwind).
  * Khởi tạo file `.env` mẫu quản lý các biến môi trường (`PORT`, `MONGODB_URI`, `JWT_SECRET`, `AWS_REGION`, v.v.).

* **Xây dựng Database Schema (11 Models):**
  Định nghĩa các Schema bằng Mongoose:
  * `User.model.js`: Quản lý tài khoản, phân quyền (student, instructor, admin), mật khẩu mã hóa bcrypt.
  * `Course.model.js` & `Lesson.model.js`: Quản lý thông tin khóa học, lộ trình bài học, video URL & tài liệu đính kèm.
  * `Enrollment.model.js` & `LessonProgress.model.js`: Theo dõi tiến độ đăng ký và phần trăm hoàn thành của học viên.
  * `Quiz.model.js` & `QuizAttempt.model.js`: Cấu hình ngân hàng câu hỏi (Trắc nghiệm, Đúng/Sai, Điền từ, Tự luận) và lưu kết quả thi.
  * `AIMessage.model.js`: Lưu lịch sử hội thoại giữa học viên và Trợ lý AI Tutor.
  * `CourseDiscussion.model.js` & `Notification.model.js`: Tính năng thảo luận bài học và hệ thống thông báo.
  * `RequestMetric.model.js`: Lưu chỉ số HTTP requests cho trang Admin Monitoring.

* **Viết tài liệu `API_DESIGN.md`:**
  * Đóng gói chi tiết danh sách tất cả API Endpoints cho các nhóm chức năng: Auth, Course, Lesson, Quiz, AI, Progress, Metrics.

---

### 4. Kết quả đạt được (Deliverables)
* Nắm rõ lý thuyết cấu trúc VPC, Subnet, Security Group và IAM trên AWS.
* Hoàn thành 11 file Mongoose Models trong thư mục `LearnSphere_BE/src/models/`.
* Cấu hình thành công kết nối MongoDB Atlas Cluster.
* Tài liệu `API_DESIGN.md` hoàn chỉnh phục vụ cho quá trình lập trình các tuần tiếp theo.
