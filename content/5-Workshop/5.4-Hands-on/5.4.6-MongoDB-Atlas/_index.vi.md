---
title: "Cấu hình Cơ sở dữ liệu MongoDB Atlas"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 5.4.6. </b> "
---

# 5.4.6. Cấu hình Cơ sở dữ liệu Cloud MongoDB Atlas

Trong bước này, người thực hiện sẽ khởi tạo và cấu hình bảo mật cho Cluster cơ sở dữ liệu NoSQL **MongoDB Atlas**, tạo tài khoản truy cập Database User và mở đường mạng kết nối an toàn từ máy chủ EC2.

---

### 1. Khởi tạo Database User trên MongoDB Atlas

1. Đăng nhập trang quản trị **MongoDB Atlas** $\rightarrow$ chọn mục **Database Access** bên danh sách trái.
2. Chọn **Add New Database User**.
3. **Authentication Method:** Chọn **Password**.
4. Khai báo **Username:** `learnsphere_prod`.
5. Tạo mật khẩu phức tạp (Password) và sao chép lại mật khẩu bảo mật này.
6. **Database User Privileges:** Chọn **Read and write to any database** (`readWriteAnyDatabase`).
7. Bấm **Add User**.

---

### 2. Cấu hình Bảo mật Mạng (Network Access IP Whitelist)

1. Chọn mục **Network Access** bên danh sách trái $\rightarrow$ chọn **Add IP Address**.
2. Thêm địa chỉ **IPv4 Public IP** của máy chủ EC2 (hoặc chọn `0.0.0.0/0` trong giai đoạn lab ban đầu).
3. Bấm **Confirm**.

---

### 3. Lấy Chuỗi Kết nối SRV Connection String

1. Chọn mục **Database** $\rightarrow$ tại Cluster vừa tạo bấm nút **Connect**.
2. Chọn phương thức **Drivers** (Node.js).
3. Sao chép chuỗi kết nối chuẩn dạng SRV format:

```text
mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
```

4. Thay thế `<password>` bằng mật khẩu đã tạo ở Bước 1. Chuỗi kết nối này sẽ dùng để khai báo biến môi trường `MONGODB_URI` cho ứng dụng Backend.
