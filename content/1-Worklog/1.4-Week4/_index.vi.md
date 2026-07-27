---
title: "Worklog Tuần 4"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---

## Chủ đề: Lưu trữ Frontend tĩnh trên Amazon S3, cấu hình CloudFront CDN và thiết lập CI/CD với GitHub Actions

### 1. Mục tiêu tuần 4
* Thành thạo việc đóng gói và lưu trữ ứng dụng Web Frontend dạng tĩnh (Static Single Page Application - SPA) trên Amazon S3.
* Nắm vững nguyên lý hoạt động của mạng phân phối nội dung Amazon CloudFront (CDN), cấu hình HTTPS và cơ chế phân phối ứng dụng React/Vite.
* Xây dựng quy trình tự động hóa tích hợp và triển khai liên tục CI/CD (Continuous Integration / Continuous Deployment) bằng GitHub Actions cho cả Frontend và Backend.
* Hoàn thiện giao diện người dùng chính trên React 18, TypeScript, Tailwind CSS kết nối trực tiếp với RESTful APIs trên EC2.

---

### 2. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Amazon S3 Static Website Hosting & Amazon CloudFront CDN
* **Lưu trữ Frontend tĩnh trên Amazon S3:**
  * Hiểu cơ chế đóng gói (Build process) của Vite: Chuyển đổi mã nguồn React/TypeScript thành tập hợp các file tĩnh bao gồm `index.html`, JavaScript (`.js`), CSS (`.css`) và assets lưu trong thư mục `dist/`.
  * Tạo Bucket S3 mới đặt tên `learnsphere-fe-static` tại Region `ap-southeast-1`.
  * Cấu hình **Static Website Hosting** trên S3: Chỉ định `index.html` làm Index Document và Error Document (đảm bảo cơ chế Routing của Client-side React Router hoạt động bình thường).
* **Amazon CloudFront CDN (Content Delivery Network):**
  * Khái niệm Edge Locations và cách CloudFront giúp tăng tốc độ tải trang bằng cách lưu bộ nhớ đệm (Caching) ở các trạm gần người dùng nhất trên toàn cầu.
  * **Tạo CloudFront Distribution:**
    * **Origin Domain:** Trỏ tới S3 Bucket `learnsphere-fe-static`.
    * **Viewer Protocol Policy:** Cấu hình *Redirect HTTP to HTTPS* đảm bảo toàn bộ kết nối Web đều được mã hóa an toàn.
  * **Cấu hình Custom Error Responses trong CloudFront:** Xử lý lỗi 403 Forbidden và 404 Not Found bằng cách trả về file `/index.html` với HTTP Status Code 200 OK (Bắt buộc đối với các ứng dụng Single Page Application như React/Vite để tránh lỗi trắng trang khi reload URL con).
  * **Khái niệm CloudFront Cache Invalidation:** Cách phát lệnh xóa cache (`/*`) khi có bản build Frontend mới được đẩy lên S3.

#### B. Tự động hóa CI/CD với GitHub Actions
* **Khái niệm về GitHub Actions:**
  * Các thành phần cốt lõi: Workflow, Event (`push`, `pull_request`), Job, Step, Action và Runner (`ubuntu-latest`).
  * Khai báo và bảo mật các biến bí mật trong GitHub Secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `EC2_HOST`, `EC2_SSH_KEY`).
* **Xây dựng Pipeline CI/CD 2 luồng (Dual-path Deployment):**
  * **Luồng 1 (Deploy Frontend):**
    Checkout code → Setup Node.js → Install dependencies → Run `npm run build` → Sync thư mục `dist/` lên S3 Bucket `learnsphere-fe-static` bằng lệnh `aws s3 sync` → Tạo lệnh CloudFront Invalidation.
  * **Luồng 2 (Deploy Backend Docker):**
    Checkout code → Set up Docker Buildx → Log in to Amazon ECR → Build Docker Image và Tag → Push Image lên ECR → SSH vào máy chủ EC2 via SSH Action → Kéo (`docker pull`) Image mới và khởi chạy lại Container (`docker run`).

---

### 3. Công việc triển khai thực tế (Work Tasks)

* **Khởi tạo và Cấu hình Hạ tầng AWS cho Frontend (S3 + CloudFront):**
  * Tạo Bucket S3 `learnsphere-fe-static` trên AWS Console, bật tính năng Static Website Hosting và thiết lập Bucket Policy cho phép CloudFront OID/OAC (Origin Access Control) đọc dữ liệu.
  * Khởi tạo CloudFront Distribution trỏ tới S3 `learnsphere-fe-static`, bật HTTPS mã hóa SSL và cấu hình Custom Error Page trỏ lỗi 404 về `/index.html`.

* **Phát triển Giao diện React Frontend (`LearnSphere_FE`):**
  * Xây dựng hệ thống Routing với React Router DOM:
    * **Trang chủ (`HomePage.tsx`):** Banner giới thiệu, danh sách khóa học nổi bật.
    * **Trang đăng ký/đăng nhập (`LoginPage.tsx`, `SignupPage.tsx`):** Form xác thực người dùng, tích hợp lưu JWT Token vào LocalStorage/State.
    * **Trang danh mục khóa học (`CourseCatalogPage.tsx`):** Tìm kiếm, lọc khóa học theo danh mục.
    * **Trang chi tiết khóa học & Bài học (`CourseDetailPage.tsx`, `LessonDetailPage.tsx`):** Hiển thị lộ trình bài học, tích hợp trình phát Video và hiển thị nút tải tài liệu qua S3 Presigned URL.
  * Cấu hình biến môi trường Frontend `VITE_API_BASE_URL` kết nối tới Backend EC2 API.

* **Viết Workflow GitHub Actions CI/CD (`.github/workflows/deploy.yml`):**
  Khởi tạo file cấu hình `.github/workflows/deploy.yml` tại root thư mục dự án:
  ```yaml
  name: Deploy LearnSphere Full-Stack App
  on:
    push:
      branches: [ main ]
  jobs:
    deploy-frontend:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Setup Node.js
          uses: actions/setup-node@v3
          with:
            node-version: 18
        - name: Install & Build FE
          run: |
            cd LearnSphere_FE
            npm install
            npm run build
        - name: Deploy FE to S3
          uses: jakejarvis/s3-sync-action@master
          with:
            args: --delete
          env:
            AWS_S3_BUCKET: 'learnsphere-fe-static'
            AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
            AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            AWS_REGION: 'ap-southeast-1'
            SOURCE_DIR: 'LearnSphere_FE/dist'
        - name: Invalidate CloudFront
          run: |
            aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} --paths "/*"
    deploy-backend:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Configure AWS Credentials
          uses: aws-actions/configure-aws-credentials@v2
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: ap-southeast-1
        - name: Log in to Amazon ECR
          id: login-ecr
          uses: aws-actions/amazon-ecr-login@v1
        - name: Build, Tag & Push Docker Image to ECR
          run: |
            cd LearnSphere_BE
            docker build -t learnsphere-be:latest .
            docker tag learnsphere-be:latest ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
            docker push ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
        - name: Deploy to EC2 via SSH
          uses: appleboy/ssh-action@master
          with:
            host: ${{ secrets.EC2_HOST }}
            username: ubuntu
            key: ${{ secrets.EC2_SSH_KEY }}
            script: |
              aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin ${{ steps.login-ecr.outputs.registry }}
              docker stop backend-api || true
              docker rm backend-api || true
              docker pull ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
              docker run -d -p 5000:5000 --name backend-api --env-file /home/ubuntu/.env ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
  ```

---

### 4. Kết quả đạt được (Deliverables)
* Giao diện Web React Frontend hoàn thiện các trang cơ bản, giao diện responsive mượt mà trên trình duyệt.
* S3 Bucket `learnsphere-fe-static` và Amazon CloudFront Distribution được thiết lập thành công, ứng dụng có thể truy cập toàn cầu qua giao thức HTTPS.
* Workflow GitHub Actions CI/CD chạy thành công 100%: Mỗi khi developer thực hiện `git push` lên nhánh `main`, hệ thống tự động build Frontend cập nhật lên S3/CloudFront và tự động đóng gói Docker deploy bản mới lên máy chủ EC2.
