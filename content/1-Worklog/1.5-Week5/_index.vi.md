---
title: "Worklog Tuần 5"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

## Chủ đề: Xây dựng Hạ tầng VPC Multi-AZ, IAM, Đóng gói Container & Khởi tạo ECR

### 1. Mục tiêu tuần 5 (29/06/2026 – 03/07/2026)
* Khởi tạo kiến trúc mạng **Amazon VPC Multi-AZ** (2 Public Subnets, 2 Private Subnets) phục vụ High Availability.
* Cấu hình Internet Gateway, 2 NAT Gateways và S3 Gateway Endpoint đảm bảo private routing an toàn.
* Thiết lập hệ thống phân quyền **AWS IAM Role** cấp quyền cần thiết cho Backend EC2.
* Đóng gói mã nguồn Backend `LearnSphere_BE` bằng kỹ thuật **Multi-stage Docker Build** trên nền Linux Alpine.
* Khởi tạo **Amazon ECR Private Repository** và đẩy bản Docker Image đầu tiên lên AWS.

---

### 2. Lịch trình công việc từng ngày (Daily Activity Log)

| Ngày | Công việc thực hiện chi tiết | Đạt được / Sản phẩm |
|---|---|---|
| **Thứ 2 (29/06/2026)** | • Mở AWS Console $\rightarrow$ Khởi tạo VPC `LearnSphere-Prod-vpc` với CIDR `10.20.0.0/16`.<br>• Tạo 2 Public Subnets và 2 Private Subnets trải rộng trên 2 Availability Zones (`1a`, `1b`).<br>• Gắn Internet Gateway và cấu hình Public Route Table để có đường truyền ra Internet. | • Hệ thống mạng VPC nền tảng 4 subnets.<br>• Mạng Public sẵn sàng kết nối. |
| **Thứ 3 (30/06/2026)** | • Khởi tạo 2 NAT Gateways tại 2 Public Subnets để cung cấp kết nối egress cho Private Subnets.<br>• Cấu hình 2 Private Route Tables định tuyến traffic ra Internet qua NAT Gateway tương ứng cùng AZ.<br>• Tạo S3 Gateway Endpoint cho phép EC2 truy cập S3 không cần qua NAT. | • 2 NAT Gateways sẵn sàng.<br>• Private subnet kết nối an toàn. |
| **Thứ 4 (01/07/2026)** | • Tạo IAM Role `LearnSphere-Backend-Role` với Trust Policy cho phép `ec2.amazonaws.com` assume.<br>• Đính kèm chính sách `AmazonSSMManagedInstanceCore` để kết nối SSM và quyền đọc ECR, Parameter Store.<br>• Thiết lập Security Group cho ALB (mở port 443) và Backend EC2 (chỉ nhận traffic port 5000 từ ALB). | • IAM Role cấu hình thành công.<br>• Lớp bảo mật Security Group vững chắc. |
| **Thứ 5 (02/07/2026)** | • Viết `Dockerfile` Multi-stage: Stage 1 (Build `node:24-alpine`) và Stage 2 (Production runtime).<br>• Cấu hình bảo mật container: Tạo nhóm/người dùng non-root (`nodejs`/`expressuser`) thực thi app.<br>• Viết kịch bản `docker-compose.yml` để chạy thử nghiệm môi trường local, xác minh container chạy mượt mà. | • File `Dockerfile` Multi-stage tối ưu dung lượng.<br>• Container chạy dưới quyền non-root an toàn. |
| **Thứ 6 (03/07/2026)** | • Mở dịch vụ **Amazon ECR** $\rightarrow$ Tạo Private Repository `learnsphere-be` và bật *Scan on push*.<br>• Đăng nhập ECR bằng AWS CLI: `aws ecr get-login-password ... \| docker login ...`.<br>• Build image local và đẩy (push) lên ECR repository, sau đó họp Review tuần 5 với Mentor. | • Kho chứa ECR `learnsphere-be` hoạt động.<br>• Docker Image sẵn sàng cho triển khai. |

---

### 3. Nội dung học tập & Tìm hiểu (AWS & Core Tech)

#### A. Kiến thức Đám mây AWS (AWS Cloud Fundamentals)
* **Amazon VPC (Virtual Private Cloud) Multi-AZ:**
  * Nguyên tắc thiết kế mạng High Availability, cách chia subnet và định tuyến route table.
  * Tối ưu chi phí và tăng khả dụng bằng cách đặt NAT Gateway theo từng Availability Zone thay vì dùng chung.
* **AWS IAM & Security Groups:**
  * IAM Role cấp quyền cho EC2 thay vì dùng access key tĩnh dài hạn.
  * Thiết lập Security Group kiểm soát chặt chẽ lưu lượng Inbound/Outbound.
* **Amazon ECR (Elastic Container Registry):**
  * Lưu trữ an toàn Docker Images và tự động quét lỗ hổng bảo mật.

#### B. Công nghệ Docker & Containerization
* **Multi-stage Docker Builds & Security:**
  * Loại bỏ dependencies phát triển sau bước build để thu nhỏ kích thước image.
  * Tuân thủ bảo mật chạy process không dưới quyền root (non-root execution).

---

### 4. Kết quả đạt được (Deliverables)
* Mạng VPC Multi-AZ (4 Subnets, 2 NAT Gateways, IGW, S3 Endpoint) hoàn chỉnh.
* IAM Role và Security Groups sẵn sàng cho môi trường Production.
* File `Dockerfile` tối ưu hóa và đẩy thành công lên Amazon ECR.
