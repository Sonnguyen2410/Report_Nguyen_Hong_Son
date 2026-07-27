---
title: "Overview"
date: 2026-07-27
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. LearnSphere Project Overview

**LearnSphere** is a modern online learning platform (E-Learning Platform) supporting complete teaching and learning workflows for both Tutors and Students. The system is designed following a clean Monorepo architecture for synchronized code management and optimized testing workflows:

- **Frontend (`LearnSphere_FE`)**: Single Page Application (SPA) user interface developed with React.js, TypeScript, and Vite, delivering smooth performance and instant responsiveness.
- **Backend (`LearnSphere_BE`)**: RESTful API service backend built on Node.js and Express.js, handling business logic, session authentication, permission access control, and Artificial Intelligence (AI) model integrations.
- **Database (MongoDB Atlas)**: Document-oriented NoSQL database system managing user accounts, course structures, learning progress, and quiz examinations.
- **Object Storage (Amazon S3)**: Manages large media files including lecture streaming videos, PDF learning documents, and course cover images.

![LearnSphere AWS Production Architecture Diagram](/images/LEARNSHPHERE.drawio.png)

---

### 🌐 Project Links & Resources

| Resource | Link (URL) | Description |
| --- | --- | --- |
| 🌐 **Production Product Website** | [https://www.learnsphere.id.vn/](https://www.learnsphere.id.vn/) | Official LearnSphere web application operating live on AWS infrastructure |
| 🐙 **GitHub Repository** | [https://github.com/HoiaeKHMT/LearnSphere](https://github.com/HoiaeKHMT/LearnSphere) | LearnSphere source code repository (Express.js Backend & React Frontend Monorepo) |
| 🎬 **Demo Video** | [Watch Demo Video on Google Drive](https://drive.google.com/file/d/1J6heEzrB1jZO3C5Z3tuz1LBwdkRozMh4/view) | Comprehensive video showcasing platform features and system architecture |

---

### 2. Technical Workshop Objectives

The core objective of this workshop is to guide practitioners step-by-step through deploying the LearnSphere application from a local environment onto **AWS Cloud infrastructure in the Singapore region (`ap-southeast-1`)** at Production-Grade standards.

Upon completion, practitioners will master key Cloud-Native and DevOps standards:

* **Zero Static Credentials Security**: Completely eliminates long-term Access Key / Secret Key leak risks. Configures GitHub Actions OIDC to fetch short-lived temporary credentials from AWS STS during pipeline execution, combined with IAM Instance Profile (IMDSv2) attached to EC2 for automated AWS service access.
* **Network Security & SSH-less Server Administration**: Configures Security Groups closing all SSH (Port 22) and public Internet inbound ports. EC2 server management and execution are performed 100% via encrypted AWS Systems Manager (SSM) Session Manager channels.
* **Optimized CDN Content Delivery**: Deploys Amazon CloudFront as the single HTTPS entrypoint for the entire system. Distributes Frontend static assets from S3 Private via Origin Access Control (OAC), while reverse-proxying API queries (`/api/*`) to the EC2 backend, completely resolving CORS and Mixed Content issues. Attaches CloudFront Functions handling client-side SPA routing to prevent 404 errors on page reloads.
* **Zero-Downtime CI/CD Automation & Auto-Rollback**: Containerizes Backend using Multi-stage Docker Builds on lightweight Linux Alpine running under non-root permissions. Automates deployment pipelines: candidate container testing on temporary ports, health check validation, zero-downtime container swapping upon success, and automated Rollback to previous container releases on failure.
* **Centralized Monitoring & Proactive Alerting**: Aggregates all application logs into centralized Amazon CloudWatch Logs. Configures CloudWatch Alarms tracking EC2 CPU utilization and hardware health, integrated with Amazon SNS to send instant alert emails to administrators upon system anomalies.

---

### 3. Technical Configuration Summary

| Component | Technology / AWS Service | Role & Detailed Configuration |
| --- | --- | --- |
| **Network & CDN** | Amazon CloudFront | Optimizes HTTPS content distribution, secures static S3 assets via OAC, handles SPA routing. |
| **Frontend Storage** | Amazon S3 Frontend | Stores compiled React static assets in 100% Private state. |
| **Backend Server** | Amazon EC2 (`t3.small`) | Runs Node.js/Express Docker container on internal port 5000 with 2GB Swap memory to prevent OOM. |
| **Container Registry** | Amazon ECR | Stores Backend Docker Images with automated CVE vulnerability scanning on push. |
| **Media Storage** | Amazon S3 Media | Stores lecture videos, PDFs, thumbnails. All uploads/downloads enforce short-lived Presigned URLs. |
| **Database** | MongoDB Atlas | Stores Document database entities, securely connected from EC2 via encrypted SRV string. |
| **CI/CD Automation** | GitHub Actions + OIDC | Automated build, test, package, deploy, and rollback execution via AWS Systems Manager. |
| **Monitoring & Alerting** | CloudWatch Logs & Alarms + SNS | Centralized log management, automated system health monitoring, and email alerting. |

---

### 4. Outcomes Achieved

Upon completing the workshop, the LearnSphere platform will operate fully in Production under the official domain **[https://www.learnsphere.id.vn/](https://www.learnsphere.id.vn/)** (served via CloudFront HTTPS Distribution). All workflows—user registration, authentication, course management, video streaming, quiz exams, and AI Assistant interactions—operate automatically, securely, and with high availability.