---
title: "Worklog Week 7"
date: 2026-07-27
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

## Topic: Frontend CloudFront, Domain & GitHub Actions CI/CD Automation

### 1. Week 7 Objectives (Jul 13, 2026 – Jul 17, 2026)
* Provision **Amazon S3 Frontend Bucket** and **Amazon CloudFront CDN** with Origin Access Control (OAC).
* Register a custom domain and provision SSL/TLS certificates via **AWS Certificate Manager (ACM)**.
* Establish secure **GitHub Actions OIDC Identity Provider** authentication on AWS IAM.
* Build an automated CI/CD pipeline for the Backend featuring **ASG Instance Refresh & Auto-Rollback**.
* Build an automated CI/CD pipeline for the Frontend to sync with S3 and Invalidate CloudFront Cache.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jul 13, 2026)** | • Created S3 Bucket `learnsphere-frontend` (Block Public Access enabled).<br>• Configured CloudFront Distribution, enabling Origin Access Control (OAC) to grant CloudFront read access to S3.<br>• Added a Routing Behavior directing `/api/*` to the Backend ALB, resolving CORS issues natively. | • Secure S3 & CloudFront integration.<br>• Seamless SPA & API routing. |
| **Tuesday (Jul 14, 2026)** | • Requested an SSL certificate for `*.learnspherev2.id.vn` via AWS ACM in the `us-east-1` region.<br>• Validated domain ownership via Route 53 DNS records.<br>• Attached the ACM certificate and custom domain `www.learnspherev2.id.vn` to the CloudFront Distribution. | • HTTPS-secured production website.<br>• Custom domain fully operational. |
| **Wednesday (Jul 15, 2026)** | • Configured **GitHub OIDC Identity Provider** on IAM and created `LearnSphereGitHubDeployRole`.<br>• Attached policies for ECR Push, SSM Parameter Store (for tag rollback), and Auto Scaling updates.<br>• Ensured zero Static Access Keys are stored in the GitHub Repository. | • Zero Static Credentials security.<br>• Repository-scoped IAM permissions. |
| **Thursday (Jul 16, 2026)** | • Authored Backend deployment GitHub Actions Workflow (`deploy-backend.yml`).<br>• Scripted steps to build Docker image, push to ECR, and trigger `aws autoscaling start-instance-refresh`.<br>• Implemented Auto-Rollback logic: If refresh fails, restore the previous Image Tag from SSM Parameter Store. | • Fully automated Backend CI/CD.<br>• Resilient Rollback mechanism. |
| **Friday (Jul 17, 2026)** | • Authored Frontend Workflow (`deploy-frontend.yml`): compile React, upload to S3 (`aws s3 sync`).<br>• Added CloudFront cache clearing step via `aws cloudfront create-invalidation`.<br>• Triggered the full CI/CD pipeline via `git push` and attended Week 7 review. | • Completed Frontend CI/CD.<br>• Reliable end-to-end pipelines. |

---

### 3. Core Tech & Learning Topics

#### A. Content Delivery & DNS (CDN & DNS)
* **Amazon CloudFront & OAC:**
  * Securing S3 origin by forcing all traffic through CloudFront using Origin Access Control.
  * Intelligent multi-origin routing (S3 for static assets, ALB for dynamic APIs).
* **AWS Certificate Manager (ACM):**
  * Managing the lifecycle of free SSL/TLS certificates, requiring deployment in `us-east-1` for CloudFront integration.

#### B. DevOps & CI/CD Automation
* **GitHub Actions OIDC (OpenID Connect):**
  * Advanced security mechanism allowing GitHub Actions to assume temporary AWS credentials via Trust Policies instead of hardcoded keys.
* **Auto Scaling Instance Refresh:**
  * AWS native rolling update mechanism, seamlessly replacing old EC2 instances with new ones without causing downtime.

---

### 4. Deliverables
* Fully functional website accessible via secure custom domain `https://www.learnspherev2.id.vn/`.
* Secure OIDC integration between GitHub and AWS IAM.
* Automated and resilient CI/CD Pipelines for both Backend and Frontend with Rollback support.
