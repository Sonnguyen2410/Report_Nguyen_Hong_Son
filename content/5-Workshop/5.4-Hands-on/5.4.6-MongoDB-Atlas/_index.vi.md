---
title: "Cấu hình Cơ sở dữ liệu MongoDB Atlas"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 5.4.6. </b> "
---

Hệ thống LearnSphere tiếp tục sử dụng **MongoDB Atlas** làm hệ quản trị cơ sở dữ liệu trên môi trường Production thay vì chuyển sang Amazon RDS/DynamoDB vì các lý do kỹ thuật sau:
- Mã nguồn Backend đã viết hoàn chỉnh bằng Mongoose ODM.
- Cấu trúc dữ liệu người dùng, khóa học, quiz có thiết kế Document-oriented NoSQL phù hợp tuyệt đối với MongoDB.
- Giữ nguyên cơ sở dữ liệu giúp giảm thiểu nguy cơ phát sinh lỗi trong quá trình chuyển đổi.

---

### 6.1. Khởi tạo Production Database User

1. Đăng nhập trang quản trị **MongoDB Atlas** -> chọn mục **Database Access** bên menu trái.
2. Chọn **Add New Database User**.
3. **Authentication Method:** Select **Password**.
4. **Username:** `learnsphere_prod`.
5. Khởi tạo mật khẩu phức tạp dài trên 32 ký tự (lưu giữ bảo mật, không commit lên Git).
6. **Database User Privileges:** Chọn **Read and write to any database** (`readWriteAnyDatabase`).
7. Bấm **Add User** để hoàn tất.

---

### 6.2. Cấu hình Mạng & IP Access List cho EC2

1. Chọn mục **Network Access** bên menu trái -> chọn **Add IP Address**.
2. Nhập địa chỉ **IPv4 Public IP** của máy chủ EC2 `i-008c48e6c120b2978`.
3. Bấm **Confirm** để lưu quy tắc Network Access List.

![MongoDB Atlas được sử dụng làm cơ sở dữ liệu production](/images/5-Workshop/5.4/5.4.6.png)
<p align="center"><i>Hình 5.4.6 — Quản trị CSDL MongoDB Atlas Cluster và phân quyền truy cập cho máy chủ EC2.</i></p>

---

### 6.3. Lấy chuỗi SRV Connection String & Kiểm tra Health Check

1. Chọn mục **Database** -> tại Cluster Production bấm nút **Connect**.
2. Chọn **Drivers** (Node.js).
3. Sao chép chuỗi SRV Connection String:

```text
mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
```

4. Chuỗi kết nối này được điền vào biến môi trường `MONGODB_URI` trên máy chủ EC2 ở **Bước 8**.
5. Cơ chế kiểm tra sức khỏe của Backend (`/health/ready`) được lập trình để chỉ trả về trạng thái `ready` khi kết nối tới MongoDB Atlas Cluster thành công.
