---
title: "Worklog Week 6"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

## Topic: Backend High Availability with Application Load Balancer & Auto Scaling Group

### 1. Week 6 Objectives (Jul 06, 2026 – Jul 10, 2026)
* Utilize **AWS Systems Manager (SSM) Parameter Store** to securely manage sensitive environment variables.
* Create an **EC2 Launch Template** containing bootstrap configurations (User Data) to install Docker and pull the ECR image.
* Configure an **Application Load Balancer (ALB)** to distribute HTTP/HTTPS traffic across multiple Backend servers.
* Deploy an **Auto Scaling Group (ASG)** to automatically scale Backend instances across 2 Availability Zones.
* Perform High Availability (HA) Validation by simulating a server failure.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jul 06, 2026)** | • Stored sensitive configs (`MONGO_URI`, `GROQ_API_KEY`, `JWT_SECRET`) in **Systems Manager Parameter Store** as SecureStrings.<br>• Granted `ssm:GetParameters` permission to IAM Role `LearnSphere-Backend-Role` for EC2 reads. | • Secure secrets management.<br>• IAM Policy updated. |
| **Tuesday (Jul 07, 2026)** | • Created **Launch Template** `LearnSphere-Backend-Template` using Amazon Linux 2023 AMI, instance type `t3.small`.<br>• Authored a User Data bootstrap script to install Docker, authenticate with ECR, fetch SSM secrets, and run the `learnsphere-be:latest` container on port 5000. | • Reusable Launch Template.<br>• Fully automated EC2 bootstrap script. |
| **Wednesday (Jul 08, 2026)** | • Created **Target Group** `LearnSphere-Backend-TG` on port 5000, HTTP protocol.<br>• Configured Health Checks calling the `/health/ready` API endpoint to monitor container health.<br>• Provisioned a public **Application Load Balancer** in 2 Public Subnets, forwarding traffic to the Target Group. | • Load Balancing configured.<br>• Application-level Health Checks. |
| **Thursday (Jul 09, 2026)** | • Provisioned an **Auto Scaling Group** based on the newly created Launch Template.<br>• Configured the ASG to span across 2 Private Subnets (Availability Zones `1a`, `1b`).<br>• Set capacity parameters: `Desired = 2`, `Minimum = 2`, `Maximum = 4`.<br>• Attached the ASG to the ALB Target Group. | • ASG maintaining 2 instances.<br>• High Availability (HA) architecture finalized. |
| **Friday (Jul 10, 2026)** | • Accessed the ALB DNS Name to test overall Backend functionality.<br>• Performed HA Validation: Intentionally Terminated 1 InService EC2 instance to observe ASG's fault-detection and automated self-healing capabilities.<br>• Attended Week 6 review with Mentor. | • ALB distributing traffic successfully.<br>• ASG self-healing verified. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Infrastructure
* **Application Load Balancer (ALB) & Target Group:**
  * ALB operates at Layer 7, capable of intelligent HTTP/HTTPS routing.
  * Target Group Health Checks determine which instances are healthy enough to receive traffic.
* **Auto Scaling Group (ASG) & Launch Template:**
  * ASG provides Fault Tolerance by automatically replacing degraded or terminated instances.
  * Launch Templates allow version-controlled, reusable infrastructure definitions.
* **AWS Systems Manager Parameter Store:**
  * Encrypting environment variables via KMS and injecting them dynamically at runtime.

---

### 4. Deliverables
* Centrally managed and encrypted secrets via AWS SSM.
* Functional ALB Public Endpoint distributing Backend traffic.
* Resilient Auto Scaling Group with self-healing capabilities.
