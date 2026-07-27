---
title: "Các bước thực hành"
date: 2026-07-27
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Overview

Trong phần này, bạn sẽ trực tiếp thực thi tuần tự 11 bước thực hành chi tiết để đưa toàn bộ hệ thống LearnSphere từ môi trường phát triển local lên hạ tầng đám mây AWS khu vực Singapore (`ap-southeast-1`).

Bạn sẽ làm quen và làm chủ quy trình thực hành trên hai môi trường tương tác chính:

- **AWS Management Console & SSM Session Manager:** Khởi tạo và kết nối các dịch vụ lưu trữ (S3), kho container (ECR), mạng lưới CDN (CloudFront), dịch vụ giám sát (CloudWatch, SNS) và điều khiển máy chủ EC2 an toàn từ xa qua kênh truyền mã hóa của Systems Manager mà không cần mở cổng SSH 22.
- **Cửa sổ dòng lệnh Terminal & GitHub Actions Pipeline:** Thực thi các lệnh kiểm thử mã nguồn, đóng gói Docker Image Multi-stage, khai báo các biến môi trường an toàn và cấu hình tự động hóa toàn bộ quy trình tích hợp và triển khai liên tục (CI/CD) với cơ chế tự động Rollback khi gặp sự cố.

---

### Content

1. [Step 1: Chuẩn bị mã nguồn tại Local & Tạo Dockerfile Multi-stage](5.4.1-Local-Build-Dockerfile/)
2. [Step 2: Thiết lập Phân quyền và Bảo mật (AWS IAM & OIDC)](5.4.2-IAM-OIDC-Setup/)
3. [Step 3: Tạo và Cấu hình Amazon S3 (Media & Frontend Buckets)](5.4.3-S3-Buckets/)
4. [Step 4: Tạo Kho lưu trữ Amazon ECR](5.4.4-ECR-Repository/)
5. [Step 5: Khởi tạo và Cấu hình Máy chủ EC2 (Swap RAM & SSM Engine)](5.4.5-EC2-Initialization/)
6. [Step 6: Cấu hình Kết nối Cơ sở dữ liệu MongoDB Atlas](5.4.6-MongoDB-Atlas/)
7. [Step 7: Cấu hình Amazon CloudFront (CDN, OAC & SPA Routing)](5.4.7-CloudFront-Distribution/)
8. [Step 8: Cấu hình Môi trường Backend và Phân quyền trên EC2](5.4.8-EC2-Environment/)
9. [Step 9: Cấu hình GitHub Secrets & Pipeline CI/CD](5.4.9-CICD-Pipeline/)
10. [Step 10: Kiểm tra & Trải nghiệm Sản phẩm Production](5.4.10-Verification-Testing/)
11. [Step 11: Thiết lập Giám sát & Cảnh báo (CloudWatch Alarms & Amazon SNS)](5.4.11-Monitoring-Alerts/)
