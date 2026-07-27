---
title : "Các bước chuẩn bị"
date : 2024-01-01 
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

Để bài thực hành Workshop triển khai ứng dụng LearnSphere lên hạ tầng AWS diễn ra thuận lợi và đạt kết quả tốt nhất, người thực hiện cần chuẩn bị đầy đủ các tài khoản, quyền hạn truy cập, công cụ môi trường local và mã nguồn dự án theo danh sách dưới đây.

### 1. Yêu cầu Tài khoản và Quyền truy cập Đám mây

**Tài khoản đám mây AWS (AWS Account):**
* Tài khoản AWS ở trạng thái hoạt động bình thường (Active), ưu tiên các tài khoản còn trong thời gian Free Tier để tối ưu chi phí.
* **Vùng triển khai (Region):** Tất cả tài nguyên trong bài workshop sẽ được khởi tạo đồng bộ tại Region Singapore (`ap-southeast-1`).
* **Quyền hạn IAM:** Tài khoản cần có quyền quản trị viên (`AdministratorAccess`) hoặc có đầy đủ nhóm quyền quản trị trên các dịch vụ: IAM, EC2, S3, ECR, CloudFront, CloudWatch, SNS và Systems Manager.

**Tài khoản lưu trữ mã nguồn GitHub:**
* Tài khoản GitHub cá nhân và một Repository đặt tên `LearnSphere` đã được khởi tạo để lưu trữ mã nguồn dự án.
* Quyền truy cập thiết lập GitHub Repository Settings để khai báo các Repository Secrets cho quy trình CI/CD.

**Tài khoản Cơ sở dữ liệu MongoDB Atlas:**
* Tài khoản MongoDB Atlas đã khởi tạo sẵn một Cluster cơ sở dữ liệu (có thể dùng gói miễn phí M0 Sandbox hoặc gói M2 Shared).
* Đã lấy được chuỗi kết nối tiêu chuẩn SRV phục vụ khai báo môi trường cho ứng dụng Backend.

---

### 2. Chuẩn bị Công cụ trên Môi trường Máy cá nhân (Local Environment)

Người thực hiện cần cài đặt và kiểm tra sẵn các công cụ phần mềm sau trên máy tính cá nhân (Windows, macOS hoặc Linux):

**Môi trường Runtime Node.js và npm:**
* Cài đặt Node.js phiên bản `v24 LTS` trở lên.
* Công cụ npm đi kèm để cài đặt bộ thư viện dependencies, chạy kiểm thử unit test cho Backend và biên dịch mã nguồn tĩnh cho Frontend.

**Công cụ quản lý phiên bản Git CLI:**
* Git đã được cài đặt và cấu hình thông tin cá nhân (email, username).
* Đã liên kết và xác thực thành công với GitHub Repository của dự án.

**Nền tảng đóng gói Docker Desktop:**
* Phần mềm Docker Desktop đã được cài đặt và đang ở trạng thái hoạt động (Running).
* Sử dụng để thực thi lệnh build thử nghiệm Docker Image của Backend và kiểm thử container tại môi trường máy cá nhân trước khi đẩy lên mây.

**Công cụ tương tác AWS CLI (Version 2):**
* Đã cài đặt công cụ dòng lệnh AWS CLI phiên bản 2.
* Được sử dụng để kiểm tra xác thực danh tính phân quyền IAM và thử nghiệm tương tác với dịch vụ Amazon S3 từ cửa sổ dòng lệnh.

**Trình soạn thảo mã nguồn và Terminal:**
* Trình soạn thảo mã nguồn ưu tiên như Visual Studio Code.
* Cửa sổ dòng lệnh Terminal (macOS/Linux) hoặc PowerShell (Windows) để thực thi các câu lệnh điều khiển.

---

### 3. Chuẩn bị Mã nguồn Dự án và Tệp Cấu hình Mẫu

**Cấu trúc Thư mục Monorepo LearnSphere:**
* Mã nguồn dự án tại local phải đảm bảo cấu trúc thư mục hoàn chỉnh gồm thư mục `LearnSphere_BE` (chứa mã nguồn ứng dụng Backend Express.js), thư mục `LearnSphere_FE` (chứa mã nguồn giao diện React/Vite) và thư mục `.github/workflows` (chứa file cấu hình tự động hóa triển khai).

**File Cấu hình Môi trường Mẫu (`.env.example`):**
* Chuẩn bị sẵn danh sách các biến môi trường cần thiết cho ứng dụng Backend như: cổng dịch vụ `PORT=5000`, môi trường `NODE_ENV=production`, chuỗi kết nối `MONGODB_URI`, khóa mã hóa `JWT_SECRET`, tên miền `FRONTEND_URL`, Region AWS và tên S3 Bucket lưu trữ truyền thông.

---

### 4. Danh mục Kiểm tra Sẵn sàng (Checklist)

Trước khi bắt đầu bước thực hành đầu tiên, người thực hiện nên rà soát lại toàn bộ bảng kiểm tra dưới đây:

- [x] Đã đăng nhập thành công vào trang quản trị AWS Management Console tại Region Singapore (`ap-southeast-1`).
- [x] Đã khởi tạo và truy cập được vào bảng điều khiển MongoDB Atlas Cluster.
- [x] Máy tính cá nhân đã mở sẵn Docker Desktop và cửa sổ dòng lệnh Terminal.
- [x] Mã nguồn dự án tại local đã chạy thử nghiệm thành công các lệnh kiểm thử và biên dịch mà không phát sinh lỗi.