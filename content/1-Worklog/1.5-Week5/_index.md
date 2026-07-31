---
title: "Worklog Week 5"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

## Topic: Multi-AZ VPC Infrastructure, IAM, Container Packaging & ECR Provisioning

### 1. Week 5 Objectives (Jun 29, 2026 – Jul 03, 2026)
* Provision **Amazon VPC Multi-AZ** network architecture (2 Public Subnets, 2 Private Subnets) for High Availability.
* Configure Internet Gateway, 2 NAT Gateways, and an S3 Gateway Endpoint to ensure secure private routing.
* Set up **AWS IAM Role** to grant least-privilege permissions to Backend EC2 instances.
* Package Backend `LearnSphere_BE` using **Multi-stage Docker Builds** on lightweight Linux Alpine.
* Provision **Amazon ECR Private Repository** and push the initial Docker Image to AWS.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jun 29, 2026)** | • Accessed AWS Console $\rightarrow$ Created VPC `LearnSphere-Prod-vpc` with CIDR `10.20.0.0/16`.<br>• Created 2 Public Subnets and 2 Private Subnets spanning across 2 Availability Zones (`1a`, `1b`).<br>• Attached Internet Gateway and configured Public Route Table for Internet access. | • Foundational VPC with 4 subnets.<br>• Public network ready for connectivity. |
| **Tuesday (Jun 30, 2026)** | • Provisioned 2 NAT Gateways in the Public Subnets to provide egress for Private Subnets.<br>• Configured 2 Private Route Tables to route Internet traffic through the respective AZ's NAT Gateway.<br>• Created an S3 Gateway Endpoint allowing EC2 to access S3 bypassing NAT. | • 2 operational NAT Gateways.<br>• Secure private subnet egress routing. |
| **Wednesday (Jul 01, 2026)** | • Created IAM Role `LearnSphere-Backend-Role` with a Trust Policy for `ec2.amazonaws.com`.<br>• Attached `AmazonSSMManagedInstanceCore` and inline policies for ECR/Parameter Store access.<br>• Configured Security Groups for ALB (open port 443) and Backend EC2 (only allow port 5000 from ALB). | • IAM Role configured.<br>• Robust Security Group isolation. |
| **Thursday (Jul 02, 2026)** | • Authored Multi-stage `Dockerfile`: Stage 1 (Build `node:24-alpine`) and Stage 2 (Production runtime).<br>• Enforced security: Created non-root user/group (`nodejs`/`expressuser`) for process execution.<br>• Authored `docker-compose.yml` for local testing and verified container functionality. | • Optimized Multi-stage `Dockerfile`.<br>• Non-root container security enforcement. |
| **Friday (Jul 03, 2026)** | • Accessed **Amazon ECR** $\rightarrow$ Created Private Repository `learnsphere-be` and enabled *Scan on push*.<br>• Authenticated via AWS CLI: `aws ecr get-login-password ... \| docker login ...`.<br>• Built local image and pushed to the ECR repository, followed by Week 5 review with Mentor. | • Functional ECR `learnsphere-be` repo.<br>• Docker Image ready for deployment. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Infrastructure
* **Amazon VPC (Virtual Private Cloud) Multi-AZ:**
  * High Availability network design principles, subnetting, and route table configuration.
  * Cost optimization and availability enhancement by using AZ-specific NAT Gateways.
* **AWS IAM & Security Groups:**
  * Assigning IAM Roles to EC2 instances instead of relying on long-term static access keys.
  * Configuring restrictive Inbound/Outbound rules to tightly control traffic flow.
* **Amazon ECR (Elastic Container Registry):**
  * Secure storage for Docker Images with automated vulnerability scanning capabilities.

#### B. Docker Containerization
* **Multi-stage Docker Builds & Security:**
  * Removing build dependencies from the final stage to significantly reduce image size.
  * Adhering to security best practices by running processes under non-root permissions.

---

### 4. Deliverables
* Fully operational Multi-AZ VPC network (4 Subnets, 2 NAT Gateways, IGW, S3 Endpoint).
* Production-ready IAM Role and Security Groups.
* Optimized `Dockerfile` successfully pushed to Amazon ECR.
