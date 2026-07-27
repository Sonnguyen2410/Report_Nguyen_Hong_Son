---
title: "Chuẩn bị mã nguồn tại Local & Dockerfile"
date: 2026-07-27
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

# 5.4.1. Chuẩn bị mã nguồn tại Local & Dockerfile

Trong bước này, người thực hiện sẽ chuẩn bị mã nguồn dự án **LearnSphere** trên máy cá nhân, kiểm tra cấu trúc Monorepo và viết file `Dockerfile` tối ưu phục vụ đóng gói container cho dịch vụ Backend.

---

### 1. Kiểm tra Cấu trúc Mã nguồn Monorepo tại Local

Mở cửa sổ dòng lệnh Terminal và di chuyển vào thư mục dự án LearnSphere:

```bash
cd LearnSphere
ls -la
```

Cấu trúc thư mục phải bao gồm:
- `LearnSphere_BE/`: Mã nguồn ứng dụng Backend (Node.js/Express).
- `LearnSphere_FE/`: Mã nguồn ứng dụng Frontend (React/Vite).
- `.github/workflows/`: Thư mục chứa các tệp cấu hình tự động hóa triển khai CI/CD.

---

### 2. Viết file Dockerfile tối ưu cho Backend (`LearnSphere_BE`)

Tạo file `Dockerfile` bên trong thư mục `LearnSphere_BE/` với nội dung đóng gói chuẩn Multi-stage build trên nền Linux Alpine siêu nhẹ:

```dockerfile
# Stage 1: Build dependencies & production ready image
FROM node:24-alpine AS builder

WORKDIR /app

# Copy package.json và package-lock.json để tận dụng Docker Cache
COPY package*.json ./

# Cài đặt tất cả phụ thuộc bao gồm cả devDependencies
RUN npm ci

# Copy toàn bộ mã nguồn ứng dụng
COPY . .

# Stage 2: Production Runtime Image
FROM node:24-alpine AS runner

WORKDIR /app

# Tạo group và user non-root để đảm bảo an toàn bảo mật
RUN addgroup -g 1001 -S nodejs && \
    adduser -u 1001 -S nodejs -G nodejs

# Copy ứng dụng và node_modules từ stage builder
COPY --from=builder /app ./

# Cấp quyền sở hữu thư mục ứng dụng cho user non-root
USER nodejs

# Khai báo cổng ứng dụng lắng nghe
EXPOSE 5000

# Chỉ thị kiểm tra sức khỏe container định kỳ
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:5000/health/ready || exit 1

# Lệnh khởi chạy ứng dụng
CMD ["node", "src/server.js"]
```

---

### 3. Tạo file `.dockerignore`

Tạo file `.dockerignore` tại thư mục `LearnSphere_BE/` để loại bỏ các tệp rác khi build image:

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

### 4. Build và Kiểm thử Container tại Máy cá nhân

Thực thi lệnh build Docker Image thử nghiệm ở local:

```bash
cd LearnSphere_BE
docker build -t learnsphere-be:local .
```

Khởi chạy container thử nghiệm trên cổng 5000:

```bash
docker run -d -p 5000:5000 --name test-be --env-file .env.example learnsphere-be:local
```

Kiểm tra trạng thái phản hồi sức khỏe của container:

```bash
curl http://localhost:5000/health/ready
```

**Kết quả mong đợi:** Trình duyệt/Terminal trả về mã phản hồi `200 OK` với thông báo kết nối hệ thống sẵn sàng. Sau khi kiểm thử thành công, xóa container thử nghiệm:

```bash
docker stop test-be && docker rm test-be
```
