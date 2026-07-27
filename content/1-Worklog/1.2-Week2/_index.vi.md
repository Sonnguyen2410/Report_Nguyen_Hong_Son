---
title: "Worklog Tuần 2"
date: 2026-07-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

## Chủ đề: Phát triển RESTful API Backend & Xác thực JWT Authentication cho LearnSphere

### 1. Mục tiêu tuần 2 (08/06/2026 – 12/06/2026)
* Xây dựng cấu trúc máy chủ Node.js Express Backend chuẩn modular architecture.
* Triển khai hệ thống xác thực người dùng JWT Authentication và phân quyền truy cập Role-Based Access Control (RBAC).
* Phát triển toàn bộ nhóm API quản lý tài khoản (`Auth`), khóa học (`Course`), bài học (`Lesson`) và tiến độ (`Progress`).
* Viết Unit Tests kiểm thử chất lượng mã nguồn Backend bằng Jest và Supertest.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (08/06/2026)** | • Cấu hình cấu trúc thư mục `LearnSphere_BE/src/` (controllers, routes, middlewares, services, utils).<br>• Cài đặt các thư viện lõi: `express`, `mongoose`, `jsonwebtoken`, `bcryptjs`, `cors`, `dotenv`, `helmet`.<br>• Xây dựng Middleware xử lý lỗi tập trung (`error.middleware.js`) và mã hóa mật khẩu. | • Cấu trúc dự án Backend hoàn chỉnh.<br>• Bộ Middleware xử lý lỗi toàn cục. |
| **Thứ 3 (09/06/2026)** | • Xây dựng Auth Controller & Routes (`/api/auth`): Đăng ký, Đăng nhập, Lấy thông tin cá nhân (`/me`), Refresh Token.<br>• Tích hợp thuật toán `bcryptjs` băm mật khẩu 10 salt rounds và sinh JWT Access Token ngắn hạn. | • API Authentication vận hành ổn định.<br>• Mã hóa mật khẩu bảo mật. |
| **Thứ 4 (10/06/2026)** | • Viết Middleware phân quyền truy cập RBAC (`auth.middleware.js`): `verifyToken`, `isInstructor`, `isAdmin`.<br>• Xây dựng các APIs quản lý danh mục khóa học (`/api/courses`): Tạo khóa học, cập nhật thông tin, xóa khóa học. | • Phân quyền RBAC chính xác.<br>• Bộ APIs Quản lý Khóa học hoàn tất. |
| **Thứ 5 (11/06/2026)** | • Phát triển các APIs quản lý bài học (`/api/lessons`): Thêm bài học mới, sắp xếp thứ tự hiển thị bài học.<br>• Xây dựng các APIs ghi nhận tiến độ học tập (`/api/progress`) và đăng ký khóa học (`/api/enrollments`). | • Bộ APIs Bài học & Tiến độ học tập.<br>• Tính năng ghi nhận % học tập. |
| **Thứ 6 (12/06/2026)** | • Khởi tạo bộ test tự động bằng Jest & Supertest kiểm thử chuỗi APIs Authentication và Course CRUD.<br>• Đóng gói Postman Collection (`LearnSphere_BE_APIs.json`) phục vụ kiểm thử thủ công.<br>• Tham gia họp Review tuần 2 với Mentor. | • Bộ Unit Tests Jest chạy sạch 100%.<br>• Postman Collection sẵn sàng demo. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **AWS IAM Roles & Service Access:**
  * Phân biệt giữa Long-term Static Credentials (Access Keys) và Short-term Dynamic Credentials.
  * Nghiên cứu cơ chế IAM Roles dành cho các dịch vụ compute (như EC2).
* **AWS Security Best Practices:**
  * Không lưu cứng (hard-code) Access Keys hay Secrets trong mã nguồn Git.
  * Quản lý biến môi trường an toàn thông qua `.env` và dịch vụ AWS Systems Manager Parameter Store / Secrets Manager.

#### B. Phát triển Backend Node.js / Express
* **Xác thực JWT & Phân quyền RBAC:**
  * Cơ chế sinh Token, mã hóa Payload và kiểm tra chữ ký Digital Signature của JWT.
  * Thiết kế Middleware phân quyền kiểm tra vai trò người dùng (Student, Instructor, Admin).
* **Kiểm thử tự động (Unit & Integration Testing):**
  * Sử dụng framework **Jest** và **Supertest** để mô phỏng HTTP requests và kiểm tra kết quả JSON Response.

---

### 4. Kết quả đạt được (Deliverables)
* Mã nguồn Backend Node.js/Express hoàn chỉnh cho các Module: Auth, Course, Lesson, Progress.
* Bộ Middleware mã hóa mật khẩu, xác thực JWT Token và phân quyền RBAC an toàn.
* Tệp Postman Collection hỗ trợ kiểm thử APIs.
* Bộ Unit Tests Jest đạt tỉ lệ bao phủ mã nguồn (Code Coverage) > 85%.
