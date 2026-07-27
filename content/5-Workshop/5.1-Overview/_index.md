---
title: "Overview"
date: 2026-07-27
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

# Overview (LearnSphere Project Overview)

### 1. Overview of the LearnSphere Project

**LearnSphere** is a modern online learning platform (E-Learning Platform) supporting teaching and learning workflows for both Tutors and Students. Designed with a simple Monorepo architecture, it simplifies source code management and optimizes testing:

- **Frontend (`LearnSphere_FE`)**: Single Page Application (SPA) user interface developed with React.js, TypeScript, and Vite, delivering smooth performance and rapid response speeds.
- **Backend (`LearnSphere_BE`)**: RESTful API services built on Node.js and Express.js, handling business logic, session management, authorization, and AI integration.
- **Database (MongoDB Atlas)**: Cloud Document-oriented NoSQL database storing user profiles, course structures, learning progress, and Quiz exams.
- **Object Storage (Amazon S3)**: Manages large media assets such as lecture videos, PDF learning documents, and course cover images.

![LearnSphere AWS Production Architecture Diagram](/images/LEARNSHPHERE.drawio.png)

---

### 🌐 Project Links & Resources

| Resource | Link (URL) | Description |
| --- | --- | --- |
| 🌐 **Live Website** | [https://sonnguyen2410.github.io/Report_Nguyen_Hong_Son/](https://sonnguyen2410.github.io/Report_Nguyen_Hong_Son/) | Direct access to live report and running application |
| 🐙 **GitHub Repository** | [https://github.com/Sonnguyen2410/Report_Nguyen_Hong_Son](https://github.com/Sonnguyen2410/Report_Nguyen_Hong_Son) | View project source code, Docker configs, and CI/CD workflows |
| 🎬 **Video Demo** | [Watch Demo Video on YouTube / Google Drive](#) | System walkthrough & feature demonstration video |

---

### 2. Technical Objectives of the Workshop

The primary goal of this workshop is to guide you step-by-step in migrating the LearnSphere application from a local environment to **AWS Cloud infrastructure in the Singapore region (`ap-southeast-1`)** at Production-Grade standards.

Upon completion, you will master core Cloud Native and DevOps practices:

* **Zero Static Credentials Security**: Completely eliminate risks of leaked long-term Access Keys/Secret Keys. Configure GitHub Actions OIDC to fetch short-lived credentials from AWS STS during pipeline runs, combined with an IAM Instance Profile (IMDSv2) for EC2.
* **Network Safety & SSH-less Management**: Configure Security Groups to close inbound SSH (Port 22) and public ports. EC2 management and execution are handled 100% via encrypted AWS Systems Manager (SSM) Session Manager channels.
* **Optimized Content Delivery via CDN**: Deploy Amazon CloudFront as a single HTTPS entrypoint. Distribute static Frontend code from S3 Private via Origin Access Control (OAC) while forwarding `/api/*` requests to EC2, eliminating CORS and Mixed Content issues. Attach CloudFront Functions for SPA Routing to prevent 404 errors on page refreshes.
* **Zero-Downtime CI/CD Automation & Auto-Rollback**: Containerize Backend using Multi-stage Docker Builds on Linux Alpine (non-root). Automate deployment pipelines: test candidate containers on temporary ports, run periodic Health Checks, swap traffic only upon success, and trigger automatic Rollbacks on failure.
* **Centralized Monitoring & Proactive Alerting**: Aggregate all application logs into Amazon CloudWatch Logs. Set up CloudWatch Alarms for CPU usage and server health, integrated with Amazon SNS for immediate email alerts to administrators.

---

### 3. Summary Table of Technical Configuration

| Component | Technology / AWS Service | Role & Configuration Details |
| --- | --- | --- |
| **Networking & CDN** | Amazon CloudFront | HTTPS content distribution, static data security via OAC, automatic SPA routing. |
| **Frontend Storage** | Amazon S3 Frontend | Stores compiled React static assets in fully Private state. |
| **Backend Server** | Amazon EC2 (`t3.small`) | Runs Node.js/Express Docker Container on internal port 5000 with 2GB Swap memory. |
| **Container Registry** | Amazon ECR | Stores Backend Docker Images with automated vulnerability scanning on push. |
| **Media Storage** | Amazon S3 Media | Stores Videos, PDFs, and images. All uploads and downloads enforced via Backend-generated time-bound Presigned URLs. |
| **Database** | MongoDB Atlas | Document database, secure SRV connection string from EC2. |
| **CI/CD Automation** | GitHub Actions + OIDC | Automated compilation, testing, packaging, deployment, and safe rollback via AWS Systems Manager. |
| **Monitoring & Alerting** | CloudWatch Logs & Alarms + SNS | Centralized logging, automated system performance tracking, and email alert notifications. |

---

### 4. Expected Results After Completion

Upon finishing this workshop, LearnSphere will operate seamlessly in a Production environment under a single HTTPS domain provided by CloudFront. All operations—from registration, login, lecture downloads, video streaming, quiz taking to AI Assistant interaction—will run automatically, securely, and with high availability.