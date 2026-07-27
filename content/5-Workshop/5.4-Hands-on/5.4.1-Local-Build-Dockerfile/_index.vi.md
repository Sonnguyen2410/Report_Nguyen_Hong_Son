---
title: "Chuẩn bị mã nguồn tại Local & Dockerfile"
date: 2026-07-27
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

Trong bước này, người thực hiện sẽ kiểm thử mã nguồn dự án **LearnSphere** trên máy cá nhân, kiểm tra khả năng biên dịch Frontend, và chuẩn bị tệp `Dockerfile` Multi-stage tối ưu để đóng gói ứng dụng Backend.

---

### 1.1. Kiểm tra Cấu trúc Dự án & Chạy Kiểm thử Local

Mã nguồn dự án LearnSphere được tổ chức dưới dạng cấu trúc Monorepo bao gồm 2 phần chính:

```text
LearnSphere/
├── LearnSphere_BE/       # Backend Node.js / Express.js REST API
├── LearnSphere_FE/       # Frontend React.js / Vite Single Page Application
├── .github/workflows/    # Quy trình tự động hóa CI/CD
└── docs/                 # Tài liệu hướng dẫn kỹ thuật
```

#### Thực hiện kiểm thử Backend trên môi trường local:

Mở cửa sổ dòng lệnh Terminal/PowerShell và thực thi lệnh kiểm thử bộ test case của Backend:

```powershell
cd LearnSphere_BE
npm ci
npm test
```

> **Yêu cầu đạt được:** Tất cả các Unit Test backend phải vượt qua thành công (`Pass`), không có test case thất bại (`Failed`).

#### Thực hiện kiểm thử biên dịch Frontend:

Di chuyển sang thư mục `LearnSphere_FE` và chạy lệnh đóng gói tĩnh của Vite:

```powershell
cd ..\LearnSphere_FE
npm ci
npm run build
```

> **Yêu cầu đạt được:** Trình biên dịch TypeScript không phát sinh lỗi và Vite tạo ra thư mục chứa sản phẩm tĩnh `LearnSphere_FE/dist`.

---

### 1.2. Viết file Dockerfile Multi-stage cho Backend

Để đóng gói Backend Node.js đạt tiêu chuẩn Production-Grade, chúng ta xây dựng tệp `Dockerfile` tại thư mục `LearnSphere_BE/` sử dụng kỹ thuật **Multi-stage Build** trên nền hình ảnh `node:24-alpine` siêu nhẹ:

```dockerfile
# Stage 1: Build Dependencies
FROM node:24-alpine AS builder

WORKDIR /app

# Copy các tệp mô tả thư viện để tận dụng bộ nhớ đệm (Layer Cache) của Docker
COPY package*.json ./

# Cài đặt toàn bộ phụ thuộc với npm ci
RUN npm ci

# Copy toàn bộ mã nguồn
COPY . .

# Stage 2: Production Runtime
FROM node:24-alpine AS runner

WORKDIR /app

# Tạo nhóm và người dùng hệ thống non-root (UID 1001) để tăng cường bảo mật
RUN addgroup -g 1001 -S nodejs && \
    adduser -u 1001 -S nodejs -G nodejs

# Sao chép mã nguồn và node_modules từ stage builder
COPY --from=builder /app ./

# Cấp quyền sở hữu thư mục ứng dụng cho user non-root
USER nodejs

# Khai báo cổng 5000 ứng dụng lắng nghe
EXPOSE 5000

# Chỉ thị kiểm tra sức khỏe container định kỳ (Health Check)
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:5000/health/ready || exit 1

# Lệnh khởi chạy server Node.js
CMD ["node", "src/server.js"]
```

---

### 1.3. Tạo tệp `.dockerignore`

Tạo tệp `.dockerignore` cùng cấp với `Dockerfile` trong thư mục `LearnSphere_BE/` để loại bỏ các tệp không cần thiết và thông tin nhạy cảm khỏi Docker Image:

```text
node_modules
.env
.env.*
.git
.gitignore
README.md
dist
logs
```

---

### 1.4. Kiểm thử Đóng gói Docker Image tại Local

Chạy lệnh đóng gói Docker Image trên máy cá nhân để xác minh cú pháp:

```powershell
cd LearnSphere_BE
docker build -t learnsphere-be:local .
```

Khởi chạy container chạy thử ở local:

```powershell
docker run -d -p 5000:5000 --name test-be --env-file .env.example learnsphere-be:local
```

Kiểm tra endpoint sức khỏe của ứng dụng:

```powershell
curl http://localhost:5000/health/ready
```

**Kết quả mong đợi:** Trả về kết quả JSON `{"status":"ready"}` với mã HTTP Status `200 OK`. Dừng và xóa container thử nghiệm sau khi hoàn tất:

```powershell
docker stop test-be && docker rm test-be
```

![Đóng gói Backend LearnSphere thành Docker image](/images/5-Workshop/5.4/5.4.1.png)
<p align="center"><i>Hình 5.4.1 — Kiểm tra mã nguồn và đóng gói Backend LearnSphere thành Docker image.</i></p>
