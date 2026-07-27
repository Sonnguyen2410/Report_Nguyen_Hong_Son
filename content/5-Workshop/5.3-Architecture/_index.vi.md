---
title: "Mô tả kiến trúc"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Mô tả kiến trúc tổng thể của hệ thống

#### Tổng quan mô hình kiến trúc

Kiến trúc kỹ thuật của hệ thống được thiết kế nhằm đảm bảo khả năng kết nối Hybrid (Hybrid Connectivity) an toàn giữa hạ tầng Đám mây (AWS Cloud) và Môi trường Truyền thống (On-premises Datacenter), cho phép các tài nguyên truy cập các dịch vụ như **Amazon S3** thông qua kênh truyền riêng tư mà không cần đi qua Internet công cộng.

![Sơ đồ kiến trúc](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

---

#### Chi tiết các thành phần hệ thống

1. **Môi trường Cloud (AWS Cloud VPC)**:
   - **VPC Cloud (`10.0.0.0/16`)**: Chứa tài nguyên EC2 Instance thử nghiệm và Gateway VPC Endpoint cho Amazon S3.
   - **Gateway VPC Endpoint**: Định tuyến lưu lượng riêng tư từ EC2 trong VPC tới Amazon S3 thông qua Bảng định tuyến (Route Table) mà không cần Internet Gateway hay NAT Gateway.
   - **AWS Transit Gateway (TGW)**: Đóng vai trò làm bộ tập trung định tuyến (Central Router), kết nối VPC Cloud với mạng On-premises thông qua VPN Tunnel.

2. **Môi trường On-Premises mô phỏng (VPC On-Prem)**:
   - **VPC On-Prem (`192.168.0.0/16`)**: Mô phỏng Trung tâm dữ liệu (Datacenter) của doanh nghiệp.
   - **StrongSwan VPN EC2**: Khởi tạo đường hầm IPSec VPN (Site-to-Site VPN) kết nối trực tiếp với AWS Transit Gateway.
   - **Interface VPC Endpoint (AWS PrivateLink)**: Cung cấp điểm cuối địa chỉ IP riêng trong VPC đại diện cho Amazon S3, cho phép các máy tính tại On-premises phân giải qua DNS và truy cập S3 thông qua kết nối VPN.

3. **Cơ chế Phân giải Tên miền (DNS Resolution Flow)**:
   - **Amazon Route 53 Inbound Endpoint & Resolver Rules**: Hỗ trợ máy chủ On-premises phân giải các địa chỉ DNS của S3 (`s3.us-east-1.amazonaws.com`) về địa chỉ IP nội bộ của Interface Endpoint.

---

#### Luồng truy cập dữ liệu (Traffic Flow)

- **Truy cập từ VPC Cloud**: `EC2 Instance` $\rightarrow$ `Route Table` $\rightarrow$ `Gateway VPC Endpoint` $\rightarrow$ `Amazon S3 Bucket`.
- **Truy cập từ On-Premises**: `On-Prem Client` $\rightarrow$ `IPSec VPN Tunnel` $\rightarrow$ `AWS Transit Gateway` $\rightarrow$ `Interface VPC Endpoint` $\rightarrow$ `Amazon S3 Bucket`.
