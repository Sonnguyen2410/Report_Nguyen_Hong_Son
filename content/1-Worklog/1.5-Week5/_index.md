---
title: "Worklog Week 5"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

## Topic: Multi-stage Docker Containerization & Amazon EC2 Infrastructure Provisioning

### 1. Week 5 Objectives (Jun 29, 2026 – Jul 03, 2026)
* Package Backend `LearnSphere_BE` using **Multi-stage Docker Builds** on lightweight Linux Alpine.
* Enforce container security: Execute Node.js process under non-root user permissions.
* Provision Amazon ECR Private Repository `learnsphere-be` and push initial image release.
* Provision **Amazon EC2 (`t3.small`)** in Singapore (`ap-southeast-1`), attach `LearnSphereEc2Role`, and configure 2GB Swap.
* Lock down Security Group (disable Port 22 SSH) and establish SSH-less management via **AWS Systems Manager (SSM)**.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jun 29, 2026)** | • Authored Multi-stage `Dockerfile`: Stage 1 (Build dependencies `node:24-alpine`) and Stage 2 (Minimal runtime).<br>• Enforced security: Created non-root user/group (`nodejs`/`expressuser`) for application process execution.<br>• Added `HEALTHCHECK` instruction querying `/health/ready` endpoint. | • Optimized Multi-stage `Dockerfile` (~180MB).<br>• Non-root container security enforcement. |
| **Tuesday (Jun 30, 2026)** | • Authored `docker-compose.yml` for local multi-container development testing.<br>• Executed `docker build -t learnsphere-be:latest .` and verified port 5000 container response.<br>• Verified 200 OK HTTP responses from readiness health check endpoint. | • Local Docker Compose setup.<br>• Verified local container execution. |
| **Wednesday (Jul 01, 2026)** | • Created **Amazon ECR** Private Repository `learnsphere-be` via AWS Console.<br>• Enabled *Scan on push* security vulnerability assessment.<br>• Authenticated Docker CLI with ECR and pushed initial release: `docker push <account-id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest`. | • Functional ECR Repository `learnsphere-be`.<br>• Pushed initial Backend image to ECR. |
| **Thursday (Jul 02, 2026)** | • Provisioned **Amazon EC2** (`t3.small`, Amazon Linux 2023) in Singapore (`ap-southeast-1`).<br>• Created IAM Role `LearnSphereEc2Role` (`AmazonSSMManagedInstanceCore` & `AmazonEC2ContainerRegistryReadOnly`).<br>• Executed User Data script creating **2GB Swap space** to prevent OOM server crashes. | • EC2 instance `i-008c48e6c120b2978` running.<br>• Attached IAM Role & 2GB Swap. |
| **Friday (Jul 03, 2026)** | • Configured Security Group: Closed SSH Port 22 completely, accepting internal port 5000 connections.<br>• Verified SSH-less remote server access via **AWS Systems Manager (SSM) Session Manager**.<br>• Installed Docker Engine on EC2 and attended Week 5 review with Mentor. | • Secure EC2 with Port 22 closed.<br>• Verified SSM Session Manager control. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Infrastructure
* **Amazon ECR & EC2 IAM Profile (IMDSv2):**
  * Image vulnerability scanning on push and dynamic temporary credential retrieval via IMDSv2.
* **AWS Systems Manager (SSM) Session Manager:**
  * Secure SSH-less Linux server administration over encrypted TLS channels without public IP exposed ports.

#### B. Docker Containerization
* **Multi-stage Docker Builds:**
  * Shrinking production container image size from >800MB down to ~180MB.

---

### 4. Deliverables
* Multi-stage non-root `Dockerfile`.
* ECR Private Repository `learnsphere-be` with scanned image.
* EC2 instance `i-008c48e6c120b2978` with Docker, 2GB Swap, and SSM IAM Role.
* SSH-less remote management via AWS SSM Session Manager.
