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
LearnSphere là nền tảng học tập trực tuyến (E-Learning) thế hệ mới được thiết kế nhằm nâng cao hiệu quả giảng dạy và học tập trong môi trường giáo dục hiện đại. Nền tảng kết hợp ứng dụng web full-stack (React/Vite & Express/MongoDB) với hạ tầng đám mây AWS thiết kế theo chuẩn High Availability (VPC Multi-AZ, Application Load Balancer, Auto Scaling, EC2, CloudFront, S3, ECR, CloudWatch, SNS), quy trình tự động hóa CI/CD không mật khẩu tĩnh (OIDC) qua GitHub Actions và Trí tuệ Nhân tạo tốc độ cao từ Groq API (Llama 3 Inference Engine). Hệ thống hỗ trợ phân quyền linh hoạt cho 3 nhóm người dùng (Student, Instructor, Admin), tích hợp các tính năng nổi bật như Trợ lý AI hỗ trợ giải đáp thắc mắc 24/7, tự động trích xuất tài liệu (PDF/Word/Ảnh scan OCR) để tạo bài kiểm tra Quiz thông minh, lưu trữ tài nguyên đa phương tiện bảo mật qua S3 Media Bucket, và giám sát chỉ số hệ thống thời gian thực qua AWS CloudWatch kết hợp tự động gửi cảnh báo email đến Admin qua Amazon SNS.

---

### 2. Tuyên bố vấn đề

#### Vấn đề hiện tại
Các hệ thống E-Learning truyền thống thiếu tính cá nhân hóa và khả năng hỗ trợ tức thì cho học viên ngoài giờ lên lớp. Giảng viên mất quá nhiều thời gian thủ công để đọc tài liệu, tóm tắt và biên soạn từng câu hỏi kiểm tra cho học viên. Bên cạnh đó, các tài liệu bài giảng dạng file PDF, file Word (.docx) hoặc tài liệu dạng ảnh scan (OCR) chưa được tự động hóa để chuyển đổi thành dữ liệu bài học. Về mặt vận hành, việc triển khai ứng dụng thiếu tự động hóa, lưu trữ dữ liệu trực tiếp trên máy chủ gây quá tải và hạ tầng đơn lẻ (Single AZ) tiềm ẩn nguy cơ gián đoạn dịch vụ cao (Single Point of Failure).

#### Giải pháp
LearnSphere triển khai kiến trúc hạ tầng AWS sản xuất (ap-southeast-1) theo chuẩn High Availability: Frontend (React/Vite) được build tĩnh lưu trữ trên Amazon S3 (`learnsphere-fe-2`) và phân phối qua Amazon CloudFront CDN. Backend (Express.js) được đóng gói dạng Docker container, quản lý trên Amazon ECR và triển khai tự động lên cụm Amazon EC2 Instances thuộc **Auto Scaling Group** trong các **Private Subnets** (Multi-AZ). Người dùng kết nối với Backend thông qua **Application Load Balancer (ALB)** đặt tại **Public Subnets**, trong khi máy chủ Backend giao tiếp với Internet qua NAT Gateways. Quá trình CI/CD được thực hiện hoàn toàn tự động qua GitHub Actions sử dụng **AWS OIDC** và tính năng **Instance Refresh** của Auto Scaling. Cơ sở dữ liệu sử dụng MongoDB Atlas với IP Access List giới hạn chặt chẽ, các file bài giảng được lưu trên Amazon S3 (`learnsphere-media-2`). Tính năng thông minh tích hợp Groq API kết hợp xử lý văn bản (`pdf-parse`, `mammoth`, `tesseract.js`) giúp vận hành Trợ lý AI và sinh Quiz tự động. Toàn bộ hệ thống được theo dõi chặt chẽ qua AWS CloudWatch (Logs & Alarms), tự động kích hoạt Amazon SNS gửi email cảnh báo khi có sự cố.


#### Lợi ích và hoàn vốn đầu tư (ROI)
- **Tối ưu hóa thời gian:** Tự động hóa đến 80% thời gian tạo bài tập/Quiz cho giảng viên nhờ Groq AI.
- **Tính sẵn sàng cao (High Availability):** Kiến trúc Multi-AZ với Auto Scaling đảm bảo ứng dụng luôn phục vụ, tự động phục hồi (self-healing) khi có lỗi phần cứng máy chủ.
- **Bảo mật tuyệt đối:** Backend ẩn trong Private Subnet, không có Public IP. Quy trình CI/CD sử dụng OIDC loại bỏ nguy cơ lộ lọt AWS Access Key.
- **Tăng tốc triển khai:** Quy trình CI/CD tự động (build, push ECR, instance refresh, CloudFront invalidation) giảm 90% thời gian phát hành tính năng mới.

---

### 3. Kiến trúc giải pháp
Nền tảng áp dụng kiến trúc AWS Cloud Production-ready High Availability trong vùng `ap-southeast-1`. Giao diện React được phân phối qua Amazon CloudFront CDN (với OAC) kết nối S3 Frontend. Backend Express.js vận hành trên các máy chủ EC2 (Auto Scaling Group) ẩn trong Private Subnets, nhận traffic an toàn từ Application Load Balancer. Backend sử dụng NAT Gateway để gọi external APIs như Groq và MongoDB Atlas.

![LearnSphere AWS Architecture](/images/LEARNSHPHERE.png)

{{< mermaid >}}
graph TD
    subgraph Users_Dev ["Người dùng & Deployment"]
        User["👤 USER (Học viên / Giảng viên)"]
        GitHub["🐙 GitHub (CI/CD Pipeline via OIDC)"]
    end

    subgraph AWS_Cloud ["AWS Cloud Infrastructure (ap-southeast-1)"]
        IAM["🔐 IAM (OIDC Trust & Roles)"]
        ECR["📦 Amazon ECR (Container Registry)"]

        subgraph Edge_Storage ["Edge & Storage Services"]
            CloudFront["⚡ Amazon CloudFront (CDN)"]
            S3_FE["🪣 S3 Frontend Bucket (OAC)"]
            S3_Media["🪣 S3 Media Bucket (CORS)"]
        end

        subgraph VPC ["AWS VPC (Multi-AZ)"]
            subgraph PublicSubnets ["Public Subnets (AZ-a & AZ-b)"]
                IGW["🌐 Internet Gateway"]
                ALB["⚖️ Application Load Balancer"]
                NAT["🔄 NAT Gateways"]
            end
            
            subgraph PrivateSubnets ["Private Subnets (AZ-a & AZ-b)"]
                ASG["⚙️ Auto Scaling Group (EC2 Backend)"]
            end
        end

        subgraph Monitoring_Alerts ["Giám sát & Cảnh báo"]
            CloudWatch["📊 AWS CloudWatch (Logs + Alarms)"]
            SNS["🔔 Amazon SNS (Alerts Topic)"]
        end
    end

    subgraph External ["Dịch vụ bên ngoài (External)"]
        MongoDB["🍃 MongoDB Atlas (Cloud DB)"]
        Groq["🚀 Groq API (LLM Inference Engine)"]
        Gmail["✉️ Gmail ADMIN"]
    end

    %% User Flow
    User -->|Truy cập Web HTTPS| CloudFront
    CloudFront -->|Lấy static assets| S3_FE
    CloudFront -->|Gửi API Request| ALB
    ALB -->|Cân bằng tải TCP 5000| ASG
    User <-->|Tải lên / Tải về Media trực tiếp| S3_Media

    %% GitHub CI/CD Flow
    GitHub -->|Xác thực OIDC| IAM
    GitHub -->|Push Docker Image| ECR
    GitHub -->|Kích hoạt Instance Refresh| ASG
    GitHub -->|Làm mới Cache| CloudFront
    GitHub -->|Deploy Static Assets| S3_FE

    %% Backend Flow
    ASG -->|Egress| NAT
    NAT --> IGW
    NAT <-->|Quản lý file truyền thông| S3_Media
    NAT <-->|Truy vấn dữ liệu| MongoDB
    NAT <-->|Xử lý AI Tutor & Quiz Gen| Groq
    ASG -->|Đẩy Logs & Metrics| CloudWatch

    %% System Monitoring & Notification Loop
    CloudWatch -->|Báo động vượt ngưỡng| SNS
    SNS -->|Gửi Mail cảnh báo| Gmail
{{< /mermaid >}}

#### Dịch vụ AWS & Công nghệ sử dụng
- **VPC Multi-AZ & Networking:** VPC bao gồm 2 Public Subnets và 2 Private Subnets trải rộng trên 2 Availability Zones, kết hợp Internet Gateways và NAT Gateways cung cấp đường truyền ra ngoài an toàn.
- **Application Load Balancer (ALB):** Tiếp nhận luồng traffic HTTPS an toàn (cấp chứng chỉ ACM) và định tuyến vào cụm Backend phía sau.
- **Auto Scaling Group & EC2:** Tự động điều chỉnh quy mô máy chủ `t3.small` dựa trên cấu hình Launch Template, đảm bảo tính sẵn sàng (High Availability) và tự động thay thế máy chủ lỗi.
- **Amazon CloudFront & S3 Frontend:** Phân phối ứng dụng React an toàn bằng công nghệ Origin Access Control (OAC), cache tại vùng biên (Edge).
- **S3 Media Bucket:** Lưu trữ khóa học, hình ảnh, tài liệu (có quy tắc CORS phục vụ presigned URL cho client upload).
- **Amazon ECR (Elastic Container Registry):** Quản lý phiên bản các Docker image của Backend.
- **AWS IAM (OIDC):** Xác thực an toàn cho quy trình CI/CD từ GitHub Actions bằng OpenID Connect, loại bỏ Secret Key tĩnh.
- **AWS CloudWatch & Amazon SNS:** Giám sát logs hệ thống (CloudWatch Logs Agent) và cảnh báo sức khỏe của ALB/ASG qua email.
- **Groq API Engine:** Trí tuệ nhân tạo tốc độ xử lý nhanh, hỗ trợ Chatbot và phân tích bài học.
- **MongoDB Atlas:** Cơ sở dữ liệu chia sẻ chung cho toàn bộ EC2, bảo vệ bằng IP Access List (chỉ nhận traffic từ IP của NAT Gateways).

#### Thiết kế thành phần
- **Quản lý người dùng & Phân quyền:** JWT Authentication với 3 vai trò (Student, Instructor, Admin) và gửi OTP.
- **Quản lý Khóa học & Media:** Tạo khóa học, upload trực tiếp video dung lượng lớn từ trình duyệt lên S3.
- **Xử lý Tài liệu & AI Engine:** Trích xuất văn bản từ PDF/Docx/Hình ảnh (Tesseract OCR tiếng Việt), xử lý thông qua Groq LLM để tóm tắt và sinh câu hỏi trắc nghiệm.
- **Quy trình CI/CD Zero-Downtime:** GitHub Actions CI/CD tiến hành build/push ECR, sau đó kích hoạt AWS Auto Scaling `start-instance-refresh` để thay thế dần các máy chủ cũ một cách trơn tru.


---

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai
Dự án được triển khai với kiến trúc thực tế (production) gói gọn trong kỳ thực tập:

1. **Thiết kế Hệ thống & AWS Networking:** Phân tích yêu cầu, thiết kế Database, mô hình hóa VPC Multi-AZ, Public/Private Subnets, Internet/NAT Gateways, Security Groups.
2. **Xây dựng Ứng dụng & Docker:** Phát triển Backend (Express.js, AI, OCR, S3 Integration) đóng gói Docker; phát triển Frontend (React, Vite, Tailwind).
3. **Triển khai AWS HA Infrastructure:** Cấu hình ECR, Launch Template, ALB, Target Groups, Auto Scaling Group; thiết lập CloudFront (OAC) và chứng chỉ ACM (HTTPS).
4. **Tự động hóa CI/CD & Giám sát:** Cấp quyền IAM OIDC cho GitHub Actions để triển khai tự động (Frontend S3, Backend ASG Refresh). Thiết lập CloudWatch Alarms & SNS Topic giám sát tính khả dụng.
5. **Kiểm thử & Bàn giao:** Kiểm thử luồng E2E trên Production, kiểm tra khả năng Self-Healing của Auto Scaling, hoàn thành báo cáo Workshop.

#### Yêu cầu kỹ thuật
- **Backend:** Node.js 18+, Express 5, Mongoose 9, Docker, `@aws-sdk/client-s3`, `@aws-sdk/client-ssm`, Groq SDK, `tesseract.js` (`vie`), `mammoth`, `pdf-parse`.
- **Frontend & CI/CD:** React 18, TypeScript, Vite, Tailwind CSS, KaTeX. GitHub Actions (aws-actions/configure-aws-credentials với Role-to-assume).

---

### 5. Lộ trình triển khai (Worklog 9 Tuần)

Quá trình triển khai chi tiết có thể theo dõi tại mục **[Worklog](../../1-worklog/)**:
- **Tuần 1-4:** Thiết kế Database, phát triển API, ứng dụng AI, xử lý Media S3 và giao diện Frontend React.
- **Tuần 5:** Xây dựng Hạ tầng VPC Multi-AZ, IAM Roles, Đóng gói Container và Amazon ECR.
- **Tuần 6:** Triển khai Backend High Availability với Application Load Balancer & Auto Scaling Group.
- **Tuần 7:** Triển khai Frontend CloudFront, Tên miền tùy chỉnh và Tự động hóa CI/CD GitHub Actions qua OIDC.
- **Tuần 8:** Tích hợp MongoDB Atlas (IP Access List), Giám sát CloudWatch/SNS, Kiểm thử Production E2E và quy trình Dọn dẹp (Clean-up).
- **Tuần 9:** Tổng kết Dự án, soạn thảo báo cáo tài liệu Hugo và bảo vệ trước Hội đồng Mentor.

---

### 6. Phân tích Chi phí (Tối ưu High Availability)

Khi ứng dụng chạy trên kiến trúc High Availability (Multi-AZ), cấu trúc chi phí sẽ tập trung vào sự ổn định thay vì tiết kiệm tối đa như môi trường Dev.

| Dịch vụ Cloud / AI | Thông số thiết kế (Vùng ap-southeast-1) | Chi phí ước tính |
| --- | --- | --- |
| **Amazon EC2 (t3.small)** | 2 instances tối thiểu trong Auto Scaling Group | ~ 30.00 USD/tháng |
| **Application Load Balancer** | Phân bổ traffic cho 2 AZ (kèm LCU) | ~ 22.00 USD/tháng |
| **NAT Gateways (2 AZs)** | Cung cấp kết nối Internet cho Private Subnets | ~ 65.00 USD/tháng |
| **Amazon CloudFront & S3** | Lưu trữ Frontend/Media tĩnh và CDN Egress | ~ 1.00 USD/tháng |
| **CloudWatch, SNS, ECR, Route 53** | Thu thập log, lưu trữ image, phân giải DNS | ~ 2.00 USD/tháng |
| **Groq API** | Dịch vụ suy luận Llama 3 tốc độ cao (Miễn phí Beta) | 0.00 USD/tháng |
| **MongoDB Atlas** | Cụm dữ liệu Cloud DB (M0 Free Tier) | 0.00 USD/tháng |
| **Tổng cộng hàng tháng** | Chi phí điển hình cho cụm Production HA | **~ 120.00 USD/tháng** |

*Ghi chú: Để phục vụ học tập, toàn bộ kiến trúc có thể triển khai lên và dọn dẹp (tear-down) trong vài giờ thực hành (workshop) giúp tối thiểu hóa chi phí.*

---

### 7. Đánh giá rủi ro và Khắc phục

| Rủi ro (Rủi ro kỹ thuật) | Mức độ | Chiến lược Giảm thiểu & Khắc phục bằng Kiến trúc |
| --- | --- | --- |
| Máy chủ EC2 quá tải / lỗi phần cứng | Thấp | (Self-Healing) Auto Scaling Group nhận biết Unhealthy qua Target Group và tự động thay thế instance mới; traffic không bị gián đoạn. |
| Một vùng AZ (Availability Zone) bị sập | Rất Thấp | (Multi-AZ) ALB tự động định tuyến toàn bộ traffic sang các EC2 đang hoạt động bình thường ở AZ còn lại. |
| Rò rỉ AWS Credentials | Thấp | (Security) Áp dụng phương thức OIDC cho CI/CD. Không có Access Key/Secret Key tĩnh nào được lưu trên GitHub. Backend EC2 dùng IAM Role gắn qua Instance Profile. |
| Lỗi khi Deploy phiên bản mới | Trung bình | (Zero Downtime) Sử dụng tính năng Instance Refresh của ASG, triển khai cuốn chiếu (rolling deployment) để đảm bảo không bị gián đoạn ứng dụng. |

---

### 8. Kết quả kỳ vọng

- **Tiêu chuẩn Kỹ sư Đám mây:** Hoàn thiện giải pháp E-Learning từ ý tưởng, mã nguồn (Code) đến triển khai tự động CI/CD lên hạ tầng AWS Production chuẩn High Availability, tuân thủ các quy tắc bảo mật cao nhất (VPC Private, OIDC).
- **Tích hợp Trí tuệ Nhân tạo:** Khẳng định năng lực tích hợp AI tạo sinh (GenAI) vào quy trình học tập để giải quyết các bài toán giáo dục (Trợ lý học tập cá nhân, Tự động biên soạn đề).
- **Sẵn sàng Sản phẩm hóa:** Hệ thống được đóng gói hoàn chỉnh với quy trình giám sát (Monitoring) bài bản, đóng vai trò là kiến trúc mẫu chuẩn cho các dự án EdTech thực tế tiếp theo.