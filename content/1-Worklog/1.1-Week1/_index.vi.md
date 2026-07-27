---
title: "Worklog Tuần 1"
date: 2026-06-19
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

## Chủ đề: Onboarding, Tìm hiểu AWS VPC/IAM & Thiết kế Database Schema cho LearnSphere

### 1. Mục tiêu tuần 1
* Tham gia Onboarding chương trình **AWS First Cloud AI Journey (FCAJ)** và nhận tài nguyên máy chủ/tài khoản thực hành.
* Nắm vững kiến thức nền tảng về mạng đám mây AWS VPC, cơ chế bảo mật AWS IAM và cách kết nối MongoDB Atlas.
* Hoàn thành thiết kế cấu trúc dữ liệu (Database Schema) gồm 11 Mongoose Models cho hệ thống LearnSphere.
* Chuẩn hóa tài liệu thiết kế API (`API_DESIGN.md`) và khởi tạo cấu trúc thư mục dự án (Monorepo setup).

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (15/06/2026)** | • Tham gia buổi họp Onboarding khởi động kỳ thực tập với Ban Quản lý FCAJ và Mentor.<br>• Tiếp nhận tài khoản AWS Sandbox, thiết lập cấu hình AWS CLI v2 trên máy cá nhân.<br>• Thảo luận với Mentor về mục tiêu và yêu cầu bài toán dự án E-Learning LearnSphere. | • Tiếp nhận môi trường AWS thành công.<br>• Thống nhất định hướng bài toán LearnSphere. |
| **Thứ 3 (16/06/2026)** | • Tìm hiểu kiến thức chuyên sâu về mạng ảo AWS VPC (Virtual Private Cloud).<br>• Phân tích sự khác biệt giữa Public Subnet và Private Subnet.<br>• Nghiên cứu cơ chế điều hướng giao thông mạng qua Internet Gateway (IGW) và Route Table.<br>• Học cách thiết lập luật Security Group cho các cổng (22, 80, 443, 5000). | • Nắm vững sơ đồ kiến trúc mạng VPC.<br>• Bảng quy hoạch cổng Security Group. |
| **Thứ 4 (17/06/2026)** | • Tìm hiểu dịch vụ AWS IAM (Identity and Access Management): User, Group, Role, Policy.<br>• Đào sâu nguyên tắc cấp quyền tối thiểu (*Principle of Least Privilege*).<br>• Khởi tạo Cluster M0 miễn phí trên MongoDB Atlas, thiết lập IP Access List và kết nối từ local. | • Hiểu rõ cơ chế phân quyền IAM.<br>• Khởi tạo kết nối MongoDB Atlas thành công. |
| **Thứ 5 (18/06/2026)** | • Khởi tạo cấu trúc thư mục Monorepo `LearnSphere` chứa `LearnSphere_BE` và `LearnSphere_FE`.<br>• Tiến hành thiết kế 11 tệp Mongoose Model quản lý dữ liệu hệ thống (User, Course, Lesson, Enrollment, LessonProgress, Quiz, QuizAttempt, AIMessage, CourseDiscussion, Notification, RequestMetric). | • Cấu hình xong khung dự án Monorepo.<br>• Hoàn thành mã nguồn 11 Mongoose Models. |
| **Thứ 6 (19/06/2026)** | • Soạn thảo tài liệu chuẩn hóa danh sách các API Endpoints (`API_DESIGN.md`).<br>• Kiểm thử kết nối chuỗi mã hóa SRV MongoDB Atlas từ Node.js Express Backend.<br>• Tham gia họp Review tiến độ tuần 1 với Mentor và ghi nhận feedback. | • Tài liệu `API_DESIGN.md` hoàn chỉnh.<br>• Báo cáo tiến độ tuần 1 đạt yêu cầu. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

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

### 4. Kết quả đạt được (Deliverables)
* Nắm rõ lý thuyết cấu trúc VPC, Subnet, Security Group và IAM trên AWS.
* Hoàn thành 11 file Mongoose Models trong thư mục `LearnSphere_BE/src/models/`.
* Cấu hình thành công kết nối MongoDB Atlas Cluster.
* Tài liệu `API_DESIGN.md` hoàn chỉnh phục vụ cho quá trình lập trình các tuần tiếp theo.
