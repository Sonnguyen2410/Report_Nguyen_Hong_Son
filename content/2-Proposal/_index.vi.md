---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# LearnSphere - Smart AI-Powered E-Learning Platform
## Nền tảng học tập trực tuyến thông minh tích hợp AI

### 1. Tóm tắt điều hành
LearnSphere là nền tảng học tập trực tuyến (E-Learning) thế hệ mới được thiết kế nhằm nâng cao hiệu quả giảng dạy và học tập trong môi trường giáo dục hiện đại. Nền tảng kết hợp ứng dụng web full-stack (React/Vite & Express/MongoDB) với hạ tầng đám mây AWS (EC2, CloudFront, S3, ECR, AWS Systems Manager, CloudWatch, SNS), quy trình tự động hóa CI/CD qua GitHub Actions và Trí tuệ Nhân tạo tốc độ cao từ Groq API (LLM Inference Engine). Hệ thống hỗ trợ phân quyền linh hoạt cho 3 nhóm người dùng (Student, Instructor, Admin), tích hợp các tính năng nổi bật như Trợ lý AI hỗ trợ giải đáp thắc mắc 24/7, tự động trích xuất tài liệu (PDF/Word/Ảnh scan OCR) để tạo bài kiểm tra Quiz thông minh, lưu trữ tài nguyên đa phương tiện bảo mật qua S3 Media Bucket, và giám sát chỉ số hệ thống thời gian thực qua AWS CloudWatch kết hợp tự động gửi cảnh báo email đến Admin qua Amazon SNS.

---

### 2. Tuyên bố vấn đề

#### Vấn đề hiện tại
Các hệ thống E-Learning truyền thống thiếu tính cá nhân hóa và khả năng hỗ trợ tức thì cho học viên ngoài giờ lên lớp. Giảng viên mất quá nhiều thời gian thủ công để đọc tài liệu, tóm tắt và biên soạn từng câu hỏi kiểm tra cho học viên. Bên cạnh đó, các tài liệu bài giảng dạng file PDF, file Word (.docx) hoặc tài liệu dạng ảnh scan (OCR) chưa được tự động hóa để chuyển đổi thành dữ liệu bài học. Về mặt vận hành, việc triển khai ứng dụng thiếu tự động hóa (chưa có CI/CD) và lưu trữ trực tiếp các video dung lượng lớn trên server gây quá tải hệ thống và khó quản lý log vận hành.

#### Giải pháp
LearnSphere triển khai kiến trúc hạ tầng AWS sản xuất (ap-southeast-1) tối ưu: Frontend (React/Vite) được build tĩnh lưu trữ trên Amazon S3 (`S3 Frontend`) và phân phối qua Amazon CloudFront CDN. Backend (Express.js) được đóng gói dạng Docker container, quản lý trên Amazon ECR và triển khai tự động lên Amazon EC2 Instance trong VPC (Public Subnet qua Internet Gateway) thông qua quy trình GitHub Actions CI/CD kết hợp AWS Systems Manager (SSM) và IAM. Cơ sở dữ liệu sử dụng MongoDB Atlas, trong khi các file đa phương tiện và tài liệu bài học được lưu trữ trên Amazon S3 (`S3 Media`). Tính năng thông minh tích hợp Groq API LLM Inference kết hợp các thư viện xử lý văn bản (`pdf-parse`, `mammoth`, `tesseract.js` OCR) giúp tự động hóa khâu tóm tắt bài học, vận hành Trợ lý AI (AI Tutor) và sinh ngân hàng câu hỏi Quiz đa dạng. Hệ thống được theo dõi chặt chẽ qua AWS CloudWatch (Logs & Alarms), tự động kích hoạt Amazon SNS (`LearnSphere-Alerts`) gửi thông báo tức thì đến Gmail Admin khi xảy ra sự cố.


#### Lợi ích và hoàn vốn đầu tư (ROI)
- **Tối ưu hóa thời gian:** Tự động hóa đến 80% thời gian tạo bài tập/Quiz cho giảng viên.
- **Hỗ trợ học tập 24/7:** Cung cấp trợ lý học tập cá nhân hóa 24/7 cho sinh viên.
- **Tối ưu chi phí vận hành:** Việc đóng gói Docker kết hợp CloudFront và EC2 `t2.micro`/`t3.micro` giúp tối ưu hóa ngân sách vận hành, ước tính chi phí hạ tầng khoảng 8,30 – 14,80 USD/tháng (~99,60 – 177,60 USD cho 12 tháng).
- **Tăng tốc triển khai:** Quy trình CI/CD giảm 90% thời gian triển khai sản phẩm.
- **Tốc độ hoàn vốn:** Thời gian hoàn vốn và mang lại hiệu quả rõ rệt đạt từ 1–3 tháng nhờ tiết kiệm hàng trăm giờ làm việc thủ công và nâng cao chất lượng đào tạo.

---

### 3. Kiến trúc giải pháp
Nền tảng áp dụng kiến trúc AWS Cloud Production-ready trong vùng `ap-southeast-1` kết hợp với Docker containerization và quy trình tự động hóa CI/CD qua GitHub Actions. Giao diện người dùng React được phân phối qua Amazon CloudFront CDN kết nối với S3 Frontend. Backend Express.js vận hành trên Amazon EC2 Instance trong VPC Public Subnet qua Internet Gateway, tương tác trực tiếp với MongoDB Atlas, S3 Media Bucket, Groq API (LLM Inference) và hệ thống giám sát CloudWatch / SNS.

![LearnSphere AWS Architecture](/images/LEARNSHPHERE.drawio.png)

{{< mermaid >}}
graph TD
    subgraph Users_Dev ["Người dùng & Deployment"]
        User["👤 USER (Học viên / Giảng viên)"]
        GitHub["🐙 GitHub (CI/CD Pipeline)"]
    end

    subgraph AWS_Cloud ["AWS Cloud Infrastructure (ap-southeast-1)"]
        IAM["🔐 IAM (Identity & Access Control)"]
        ECR["📦 Amazon ECR (Container Registry)"]
        SSM["⚙️ AWS Systems Manager"]

        subgraph Edge_Storage ["Edge & Storage Services"]
            CloudFront["⚡ Amazon CloudFront (CDN)"]
            S3_FE["🪣 S3 Frontend Bucket"]
            S3_Media["🪣 S3 Media Bucket"]
        end

        subgraph VPC ["AWS VPC (Availability Zone)"]
            subgraph PublicSubnet ["Public Subnet"]
                IGW["🌐 Internet Gateway"]
                EC2["🖥️ Amazon EC2 Instance (Docker Backend)"]
            end
        end

        subgraph Monitoring_Alerts ["Giám sát & Cảnh báo"]
            CloudWatch["📊 AWS CloudWatch (Logs + Alarms)"]
            SNS["🔔 Amazon SNS (LearnSphere-Alerts)"]
        end
    end

    subgraph External ["Dịch vụ bên ngoài (External Services)"]
        MongoDB["🍃 MongoDB Atlas (Cloud DB)"]
        Groq["🚀 Groq API (LLM Inference Engine)"]
        Gmail["✉️ Gmail ADMIN"]
    end

    %% User Flow
    User -->|Truy cập Web| CloudFront
    CloudFront -->|Lấy static assets| S3_FE
    CloudFront -->|Gửi API Request| IGW
    IGW --> EC2
    User <-->|Tải lên / Tải về Media| S3_Media

    %% GitHub CI/CD Flow
    GitHub -->|Xác thực quyền| IAM
    GitHub -->|Push Docker Image| ECR
    GitHub -->|Điều khiển & Deploy EC2| SSM
    GitHub -->|Làm mới Cache| CloudFront
    GitHub -->|Deploy Static Assets| S3_FE

    %% EC2 Core Services
    EC2 <-->|Quản lý file truyền thông| S3_Media
    EC2 <-->|Truy vấn dữ liệu| MongoDB
    EC2 <-->|Xử lý AI Tutor & Quiz Gen| Groq
    EC2 -->|Đẩy Logs & Chỉ số| CloudWatch

    %% System Monitoring & Notification Loop
    CloudWatch -->|Báo động vượt ngưỡng| SNS
    SNS -->|Gửi Mail cảnh báo| Gmail
{{< /mermaid >}}

#### Dịch vụ AWS & Công nghệ sử dụng
- **Amazon EC2 Instance:** Host ứng dụng Backend Express.js (chạy dưới dạng Docker Container) trong Public Subnet thuộc VPC (Region `ap-southeast-1`) kết nối qua Internet Gateway.
- **Amazon CloudFront & S3 Frontend Bucket:** Phân phối và lưu trữ ứng dụng tĩnh Frontend (React + Vite + Tailwind CSS) giúp tăng tốc độ phản hồi cho người dùng toàn cầu.
- **S3 Media Bucket:** Lưu trữ bài giảng video, hình ảnh và tài liệu học tập (PDF/Word) phục vụ tương tác trực tiếp với người dùng và server EC2.
- **Amazon ECR (Elastic Container Registry) & AWS Systems Manager (SSM):** Lưu trữ Docker Images chính thức và quản lý tự động hóa triển khai, thực thi lệnh an toàn lên EC2 Instance.
- **AWS IAM (Identity and Access Management):** Quản lý quyền truy cập và xác thực an toàn cho quy trình CI/CD từ GitHub Actions tới các dịch vụ AWS.
- **AWS CloudWatch (Logs & Alarms) & Amazon SNS (`LearnSphere-Alerts`):** Thu thập Logs hệ thống, theo dõi hiệu năng EC2 và tự động kích hoạt cảnh báo gửi tới **Gmail ADMIN**.
- **GitHub Actions:** Tự động hóa toàn bộ quy trình CI/CD (Build & push Docker image lên ECR, deploy S3 Frontend, invalidate CloudFront cache và điều khiển EC2 qua Systems Manager).
- **Groq API Engine:** Cung cấp Trí tuệ nhân tạo tốc độ cao (Chatbot AI Tutor, tự động phân tích bài học và sinh Quiz).
- **MongoDB Atlas:** Cơ sở dữ liệu Cloud MongoDB lưu trữ dữ liệu người dùng, khóa học, bài học và kết quả làm bài.

#### Thiết kế thành phần
- **Quản lý người dùng & Phân quyền:** JWT Authentication với 3 vai trò (Student, Instructor, Admin) và gửi OTP khôi phục mật khẩu.
- **Quản lý Khóa học & Bài học:** Tạo khóa học (Draft/Published), upload video/tài liệu lên S3 Media, sắp xếp lộ trình bài học.
- **Xử lý Tài liệu & AI Engine:** Trích xuất text từ file PDF/Word/Ảnh scan (Tesseract OCR tiếng Việt), gửi đến Groq API để tóm tắt và sinh câu hỏi tự động.
- **Làm bài & Chấm điểm tự động:** Hệ thống Quiz tương tác (Trắc nghiệm, Đúng/Sai, Điền từ, Tự luận) tự động chấm điểm và lưu lịch sử thi.
- **Quy trình CI/CD, SSM & Cảnh báo SNS:** GitHub Actions tự động kiểm thử, push ECR và deploy qua Systems Manager lên EC2; CloudWatch & SNS giám sát hệ thống 24/7 và gửi cảnh báo tức thì tới Gmail Admin.


---

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai
Dự án gồm 2 phần — xây dựng Hạ tầng AWS / CI/CD Pipeline và phát triển ứng dụng Web Full-stack tích hợp AI — trải qua 4 giai đoạn gói gọn trong 2 tháng thực tập:

1. **Nghiên cứu và thiết kế kiến trúc:** Nghiên cứu yêu cầu hệ thống, thiết kế Database Schema (11 Models), API Design và sơ đồ kiến trúc AWS VPC (Tháng 1 / Tuần 1–2).
2. **Thiết lập Hạ tầng AWS & CI/CD:** Khởi tạo S3 Buckets, Amazon ECR, EC2 Instance, CloudFront và viết workflow GitHub Actions tự động build Docker image (Tháng 1 / Tuần 2–3).
3. **Phát triển Core Services & Tích hợp OpenAI:** Xây dựng Backend API (Auth, Course, Lesson, S3 Presigned URL), kết nối OpenAI API, module OCR/PDF parsing và dựng giao diện React/Vite (Tháng 1 / Tuần 4 - Tháng 2 / Tuần 6).
4. **Kiểm thử, Giám sát CloudWatch & Triển khai:** Cấu hình CloudWatch Logs & Alarms, kiểm thử End-to-End toàn bộ hệ thống trên EC2, tối ưu hiệu năng và đóng gói tài liệu (Tháng 2 / Tuần 7–8).

#### Yêu cầu kỹ thuật
- **Backend & Infrastructure:** Node.js 18+, Express 5, Mongoose 9, Docker, `@aws-sdk/client-s3`, `@aws-sdk/client-cloudwatch-logs`, OpenAI SDK, `tesseract.js` (data `vie`), `mammoth`, `pdf-parse`. Cấu hình `.env` cho S3 Buckets (`ap-southeast-1`), ECR URI và OpenAI API Key.
- **Frontend & CI/CD:** React 18, TypeScript, Vite, Tailwind CSS, KaTeX. Workflow GitHub Actions cấu hình AWS Secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`).

---

### 5. Lộ trình & Mốc triển khai

```text
[Tháng 0 / Trước thực tập] ──► [Tháng 1 / Tuần 1-4] ──► [Tháng 2 / Tuần 5-8] ──► [Sau triển khai]
  Kế hoạch & Thiết kế           AWS Infra & Core API     OpenAI, CI/CD & Test      Vận hành 1 năm
```

- **Trước thực tập (Tháng 0):** 1 tháng lên kế hoạch, chuẩn bị tài liệu yêu cầu và thiết kế sơ bộ kiến trúc AWS.
- **Thực tập (Tháng 1–2):**
  - **Tháng 1:**
    - *Tuần 1–2:* Hoàn thiện thiết kế Database Schema, API Design và sơ đồ VPC EC2/S3/CloudFront.
    - *Tuần 3–4:* Khởi tạo hạ tầng AWS (ECR, EC2, CloudFront), cấu hình GitHub Actions CI/CD và viết các API Backend cơ bản.
  - **Tháng 2:**
    - *Tuần 5–6:* Tích hợp OpenAI API, trích xuất tài liệu PDF/Docx/OCR, tính năng Trợ lý AI và Tạo Quiz tự động trên Frontend React.
    - *Tuần 7–8:* Cấu hình AWS CloudWatch Logs & Alarms, kiểm thử End-to-End toàn hệ thống trên EC2, tối ưu hiệu năng và hoàn thiện báo cáo.
- **Sau triển khai:** Duy trì hệ thống trên AWS, thu thập phản hồi người dùng và mở rộng tính năng trong vòng 1 năm.

---

### 6. Ước tính ngân sách

Có thể xem chi phí dịch vụ điện toán đám mây ước tính trên AWS Pricing Calculator.

#### Chi phí hạ tầng

| Dịch vụ Cloud / AI | Thông số chi tiết | Chi phí / tháng (USD) |
| --- | --- | --- |
| **Amazon EC2 (t2.micro / t3.micro)** | Free Tier hoặc ~0.0116 USD/giờ | 4.50 - 8.50 USD |
| **Amazon CloudFront & S3 Static (`learnsphere-fe-static`)** | Lưu trữ frontend và chuyển dữ liệu qua CDN | 0.50 USD |
| **Amazon S3 Standard (`ai-learning-platform-vhd`)** | 10 GB lưu trữ media/tài liệu | 0.30 USD |
| **Amazon ECR & CloudWatch** | Lưu trữ Docker images và thu thập logs | 0.50 USD |
| **OpenAI API** | ~300.000 input/output tokens cho AI Tutor & Quiz Gen | 2.50 - 5.00 USD |
| **MongoDB Atlas (Shared Cluster)** | Free Tier M0 | 0.00 USD |
| **Tổng cộng hàng tháng** | | **8,30 – 14,80 USD/tháng** |
| **Tổng cộng 12 tháng** | | **99,60 – 177,60 USD/năm** |

#### Phần cứng / Phần mềm
- **Chi phí phần cứng & phần mềm:** 0 USD (Tận dụng thiết bị có sẵn và các công cụ mã nguồn mở).

---

### 7. Đánh giá rủi ro

#### Ma trận rủi ro

| Rủi ro nhận diện | Mức độ ảnh hưởng | Xác suất xảy ra |
| --- | --- | --- |
| Sự cố EC2 Instance ngừng hoạt động (Downtime) | Cao | Thấp |
| Vượt ngân sách OpenAI API Token | Cao | Thấp |
| Lỗi OCR tài liệu scan mờ | Trung bình | Trung bình |
| Thất bại khi chạy Pipeline GitHub Actions CI/CD | Trung bình | Thấp |

#### Chiến lược giảm thiểu
- **Quản lý EC2:** Cấu hình Docker auto-restart policy (`restart: always`), tạo CloudWatch Alarm cảnh báo khi CPU/RAM vượt ngưỡng 85%.
- **Ngân sách OpenAI:** Cấu hình giới hạn Max Tokens, áp dụng Rate Limiting cho API request và lưu bộ nhớ đệm (Cache) các câu trả lời AI phổ biến.
- **Tài liệu OCR:** Tiền xử lý văn bản, hiển thị cảnh báo cho người dùng nếu file upload quá mờ.
- **CI/CD & Security:** Kiểm thử Docker build cục bộ trước khi push, bảo vệ tài khoản AWS với IAM least privilege và lưu trữ chìa khóa bảo mật trong GitHub Secrets.

#### Kế hoạch dự phòng
- Tự động khôi phục container hoặc khởi động lại EC2 via CloudWatch Actions nếu instance bị crash.
- Cung cấp công cụ QuestionBuilder cho phép giảng viên biên soạn/chỉnh sửa câu hỏi thủ công khi tài liệu tải lên không thể trích xuất AI.

---

### 8. Kết quả kỳ vọng

- **Cải tiến kỹ thuật:** Xây dựng thành công hệ thống E-Learning chuẩn Docker/AWS Cloud, triển khai tự động qua CI/CD GitHub Actions, tự động hóa quy trình tạo học liệu bằng OpenAI API và giám sát hệ thống qua CloudWatch.
- **Giá trị dài hạn:** Hạ tầng AWS EC2 + Docker sẵn sàng mở rộng quy mô (Auto Scaling Group / ECS / EKS) cho hàng nghìn học viên, cung cấp nền tảng phục vụ các nghiên cứu và sản phẩm EdTech trong tương lai.