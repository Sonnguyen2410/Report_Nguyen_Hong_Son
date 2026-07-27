---
title: "Worklog Week 7"
date: 2026-07-31
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

## Topic: CI/CD Pipeline Automation with GitHub Actions & SSM RunCommand Rollback

### 1. Week 7 Objectives
* Declare GitHub Repository Secrets (`AWS_GITHUB_ROLE_ARN`, `EC2_INSTANCE_ID`, `VITE_API_BASE_URL`, `S3_FE_BUCKET`, `CLOUDFRONT_FE_DISTRIBUTION_ID`).
* Build `.github/workflows/deploy.yml` automation workflow containing 2 jobs: `deploy-backend` and `deploy-frontend`.
* Automate EC2 Backend deployments via **AWS Systems Manager (SSM) RunCommand** without Port 22 SSH.
* Implement `candidate` container health checks, Zero-Downtime Swapping, and **Auto-Rollback** to `rollback` containers upon failure.
* Automate React Frontend builds, S3 sync, and CloudFront Cache Invalidation `/*`.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jul 27, 2026)** | • Opened GitHub Repository $\rightarrow$ **Settings** $\rightarrow$ **Secrets and variables** $\rightarrow$ **Actions**.<br>• Declared 5 critical CI/CD deployment secrets.<br>• Enforced Zero Static Credentials security guidelines. | • 5 CI/CD Secrets configured.<br>• Zero Static Credentials security. |
| **Tuesday (Jul 28, 2026)** | • Authored `.github/workflows/deploy.yml` configuring `deploy-backend` job.<br>• Added `aws-actions/configure-aws-credentials@v4` step using OIDC authentication.<br>• Built Docker Image packaging step tagging SHA Git Commit hashes and pushing to ECR. | • `deploy.yml` Backend Job.<br>• Automated Docker build & ECR push. |
| **Wednesday (Jul 29, 2026)** | • Authored Bash deployment script via `aws ssm send-command`.<br>• Configured temporary port 5001 `candidate` container execution with a 24-iteration `/health/ready` retry loop.<br>• Built Zero-Downtime container renaming and Auto-Rollback logic upon health check failures. | • SSM RunCommand Deployment script.<br>• Zero-Downtime & Auto-Rollback logic. |
| **Thursday (Jul 30, 2026)** | • Configured `deploy-frontend` job running upon successful backend deployment.<br>• Built React static assets (`npm run build`), synced `dist` directory to S3 Frontend Bucket (`aws s3 sync --delete`).<br>• Added CloudFront cache invalidation (`aws cloudfront create-invalidation --paths "/*"`). | • Completed `deploy-frontend` Job.<br>• Automated S3 Sync & CDN Invalidation. |
| **Friday (Jul 31, 2026)** | • Triggered end-to-end CI/CD pipeline via `git push origin main`.<br>• Resolved initial OIDC IAM Trust Policy string mismatch (`repo:username/repository:ref:refs/heads/main`).<br>• Verified 100% successful execution for both deployment jobs and attended Week 7 review. | • 100% passing GitHub Actions pipeline.<br>• Resolved OIDC permission issues. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Infrastructure & DevOps
* **AWS Systems Manager (SSM) RunCommand:** Remote SSH-less shell script execution on EC2.
* **GitHub Actions CI/CD:** OIDC short-lived credential authentication and multi-job workflow orchestration.
* **Zero-Downtime & Health Check Rollback:** Testing candidate containers on temporary ports before production traffic cutover.

---

### 4. Deliverables
* Complete `.github/workflows/deploy.yml` pipeline workflow.
* SSM RunCommand script with Health Check and Auto-Rollback capability.
* Automated CloudFront CDN cache invalidation workflow.
