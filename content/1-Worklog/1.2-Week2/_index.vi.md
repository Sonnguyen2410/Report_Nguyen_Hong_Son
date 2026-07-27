---
title: "Worklog Tuần 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

## Chủ đề: Đóng gói Docker Container cho Backend và Đẩy Image lên Amazon ECR

### 1. Mục tiêu tuần 2
* Nắm vững kiến thức về công nghệ Containerization với Docker, cách đóng gói ứng dụng Node.js/Express.
* Thành thạo dịch vụ Amazon ECR (Elastic Container Registry) trên AWS để quản lý kho lưu trữ Docker Images.
* Đóng gói thành công ứng dụng `LearnSphere_BE` thành Docker Image, kiểm thử chạy container ở môi trường Localhost và đẩy (push) thành công Image lên Amazon ECR.

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đóng gói Ứng dụng với Docker (Containerization)
* **Khái niệm cơ bản về Docker:**
  * Khác biệt giữa Virtual Machine (VM) và Docker Container (nhẹ hơn, khởi động nhanh hơn, dùng chung OS Kernel).
  * Phân biệt **Docker Image** (Bản thiết kế/Template) và **Docker Container** (Thực thể đang chạy).
* **Tối ưu hóa Dockerfile cho Node.js:**
  * Học cách chọn Base Image nhẹ (ví dụ: `node:18-alpine` hoặc `node:18-slim`).
  * Sử dụng file `.dockerignore` để loại bỏ `node_modules`, `.env`, `.git` và file rác giúp giảm dung lượng Image.
  * Tận dụng cơ chế **Docker Layer Caching** (sắp xếp lệnh `COPY package*.json` trước `RUN npm install` rồi mới `COPY . .`) để tăng tốc độ Re-build.
  * Thiết lập môi trường chạy an toàn (Non-root user) và lệnh khởi chạy container (`CMD ["npm", "start"]`).

#### B. Tìm hiểu Dịch vụ Amazon ECR (Elastic Container Registry)
* **Khái niệm về Amazon ECR:**
  * Amazon ECR là gì? Vai trò của ECR trong kiến trúc hạ tầng AWS (Kho lưu trữ Docker Image riêng tư và an toàn).
  * Cơ chế phân quyền ECR với AWS IAM (quyền `ecr:GetAuthorizationToken`, `ecr:BatchCheckLayerAvailability`, `ecr:PutImage`, v.v.).
* **Thao tác CLI kết nối Amazon ECR:**
  * Sử dụng AWS CLI để lấy token xác thực và đăng nhập Docker vào ECR:
    ```bash
    aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com
    ```
  * Cách tag Image phù hợp với cấu trúc ECR URI:
    ```bash
    docker tag learnsphere-be:latest <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest
    ```
  * Cách đẩy Image lên ECR (`docker push`) và kéo Image về (`docker pull`).

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Xây dựng cấu hình Docker cho Backend (`LearnSphere_BE`):**
  * Viết file `Dockerfile` trong thư mục `LearnSphere_BE`:
    * Khởi tạo môi trường Node.js.
    * Cài đặt các thư viện cần thiết (bao gồm cả các phụ thuộc cho OCR/Tesseract nếu có).
    * Mở cổng port 5000.
  * Viết file `.dockerignore` để tối ưu hóa quá trình build.

* **Kiểm thử Containerization ở Localhost:**
  * Chạy lệnh build image local: `docker build -t learnsphere-be:v1.0 .`
  * Chạy container thử nghiệm: `docker run -d -p 5000:5000 --env-file .env learnsphere-be:v1.0`
  * Sử dụng Postman / Browser kiểm tra các route cơ bản (`http://localhost:5000/api/health`) và khả năng kết nối tới MongoDB Atlas.

* **Khởi tạo Amazon ECR Repository & Đẩy Image:**
  * Truy cập AWS Management Console -> Dịch vụ Amazon ECR.
  * Tạo một Private Repository mới đặt tên là `learnsphere-be` tại Region `ap-southeast-1`.
  * Thực hiện chuỗi lệnh AWS CLI trên máy cá nhân để đăng nhập, tag và push Docker Image `learnsphere-be:v1.0` (hoặc tag `latest`) lên Amazon ECR.

---

### 4. Kết quả đạt được (Deliverables)
* File `Dockerfile` và `.dockerignore` chuẩn hóa cho `LearnSphere_BE`.
* Container Backend chạy thành công ở môi trường Localhost, kết nối ổn định với MongoDB Atlas.
* Repository `learnsphere-be` được tạo thành công trên Amazon ECR (`ap-southeast-1`).
* Docker Image đầu tiên của dự án được đẩy thành công lên Amazon ECR và sẵn sàng cho việc pull về máy chủ EC2 ở Tuần 3.
