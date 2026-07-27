---
title: "Architecture Description"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Overall System Architecture Description

#### Architecture Overview

The system architecture is designed to ensure secure hybrid connectivity between AWS Cloud infrastructure and an On-premises Datacenter environment, enabling workloads to access services like **Amazon S3** privately without traversing the Public Internet.

![Architecture Diagram](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

---

#### Component Breakdown

1. **AWS Cloud Environment (VPC Cloud)**:
   - **VPC Cloud (`10.0.0.0/16`)**: Hosts test EC2 instances and the Gateway VPC Endpoint for Amazon S3.
   - **Gateway VPC Endpoint**: Routes private traffic from EC2 instances to Amazon S3 via Route Tables without using Internet Gateway or NAT Gateway.
   - **AWS Transit Gateway (TGW)**: Acts as a central routing hub connecting VPC Cloud with On-premises network via a VPN tunnel.

2. **Simulated On-Premises Environment (VPC On-Prem)**:
   - **VPC On-Prem (`192.168.0.0/16`)**: Simulates an enterprise datacenter.
   - **StrongSwan VPN EC2**: Establishes an IPSec Site-to-Site VPN tunnel with AWS Transit Gateway.
   - **Interface VPC Endpoint (AWS PrivateLink)**: Provides private IP endpoints inside VPC representing Amazon S3, allowing on-premises workloads to query S3 via DNS over VPN.

3. **DNS Resolution Flow**:
   - **Amazon Route 53 Inbound Endpoint & Resolver Rules**: Allows on-premises hosts to resolve S3 DNS queries (`s3.us-east-1.amazonaws.com`) to private IP addresses of the Interface Endpoint.

---

#### Traffic Flow

- **VPC Cloud Access**: `EC2 Instance` $\rightarrow$ `Route Table` $\rightarrow$ `Gateway VPC Endpoint` $\rightarrow$ `Amazon S3 Bucket`.
- **On-Premises Access**: `On-Prem Client` $\rightarrow$ `IPSec VPN Tunnel` $\rightarrow$ `AWS Transit Gateway` $\rightarrow$ `Interface VPC Endpoint` $\rightarrow$ `Amazon S3 Bucket`.
