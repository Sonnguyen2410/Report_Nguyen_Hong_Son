---
title: "Workshop"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Project Kỹ thuật chính: Triển khai và Vận hành Hệ thống LearnSphere Chuẩn Production trên Hạ tầng AWS

#### Tổng quan

**LearnSphere** là nền tảng học tập trực tuyến (E-Learning Platform) hiện đại thiết kế theo mô hình Monorepo (Frontend React/Vite SPA và Backend Node.js/Express REST API), tích hợp cơ sở dữ liệu **MongoDB Atlas** cùng các tính năng AI nâng cao.

Trong bài workshop này, chúng ta sẽ tiến hành triển khai toàn bộ ứng dụng LearnSphere lên hạ tầng đám mây **AWS (Region Singapore - ap-southeast-1)** tuân thủ các tiêu chuẩn kỹ thuật hàng đầu trong vận hành hệ thống thực tế:

* **Zero Static Credentials**: Tự động hóa xác thực giữa GitHub Actions và AWS STS qua **OpenID Connect (OIDC)**; gán **IAM Instance Profile (IMDSv2)** cho EC2 nhằm loại bỏ hoàn toàn việc lưu trữ Access Key tĩnh trong file mã nguồn hay biến môi trường.
* **Bảo mật mạng & Quản trị từ xa**: Chặn hoàn toàn cổng kết nối SSH (Port 22) từ Internet; điều khiển và thực thi script trên máy chủ EC2 an toàn 100% qua **AWS Systems Manager (SSM) Session Manager**.
* **Tối ưu hóa Phân phối CDN (Single Domain)**: Phân phối tài nguyên tĩnh Frontend từ S3 Private qua **CloudFront Origin Access Control (OAC)** và chuyển tiếp `/api/*` về máy chủ EC2 cổng 5000, giải quyết triệt tiêu lỗi CORS và Mixed Content.
* **Quy trình CI/CD & Auto-Rollback**: Tự động hóa hoàn toàn quy trình đóng gói Multi-stage Docker image, push ECR, thử nghiệm Container Candidate (cổng 5001) với cơ chế tự động Rollback an toàn không gây gián đoạn dịch vụ (Zero-Downtime).
* **Giám sát chủ động (Proactive Monitoring)**: Tự động thu thập log ứng dụng về **CloudWatch Logs** và phát cảnh báo tức thì tới email quản trị viên qua **CloudWatch Alarms** và **Amazon SNS**.

---

#### Mục lục nội dung

1. [5.1. Overview (Giới thiệu)](5.1-Overview/)
2. [5.2. Prerequisite (Các bước chuẩn bị)](5.2-Prerequisite/)
3. [5.3. Mô tả kiến trúc](5.3-Architecture/)
4. [5.4. Các bước thực hành](5.4-Hands-on/)
5. [5.5. Clean-up (Dọn dẹp tài nguyên)](5.5-Cleanup/)