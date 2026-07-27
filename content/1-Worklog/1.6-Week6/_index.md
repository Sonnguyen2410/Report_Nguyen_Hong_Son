---
title: "Worklog Week 6"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

## Topic: IAM OIDC Configuration, Amazon S3 Buckets & CloudFront CDN Setup

### 1. Week 6 Objectives (Jul 06, 2026 – Jul 10, 2026)
* Establish **GitHub Actions OIDC Identity Provider** on AWS IAM, eliminating static access key leak risks.
* Provision IAM Role `LearnSphereGitHubDeployRole` with repository-scoped Trust Policy (`repo:username/repository:ref:refs/heads/main`).
* Provision **Amazon S3 Frontend Bucket** (`learnsphere-fe-575620421319`) and **S3 Media Bucket** (`learnsphere-media-575620421319`) with 100% Block Public Access.
* Deploy **Amazon CloudFront CDN Distribution** (`EQRDOBSCG5MC8`) securing S3 Frontend via **Origin Access Control (OAC)** and reverse-proxying API `/api/*` requests to EC2.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jul 06, 2026)** | • Opened AWS IAM Console $\rightarrow$ **Identity providers** $\rightarrow$ Added OIDC Provider `token.actions.githubusercontent.com` (Audience `sts.amazonaws.com`).<br>• Fetched GitHub OIDC security thumbprint.<br>• Provisioned IAM Role `LearnSphereGitHubDeployRole` with repository-bound Trust Policy. | • Configured OIDC Identity Provider.<br>• Secure IAM Role for GitHub Actions. |
| **Tuesday (Jul 07, 2026)** | • Provisioned S3 Frontend Bucket `learnsphere-fe-575620421319` in Singapore (`ap-southeast-1`).<br>• Provisioned S3 Media Bucket `learnsphere-media-575620421319` with Server-Side Encryption (SSE-S3).<br>• Configured S3 CORS JSON rules supporting direct browser uploads. | • Provisioned 2 S3 Buckets.<br>• 100% Block Public Access & CORS. |
| **Wednesday (Jul 08, 2026)** | • Opened **Amazon CloudFront** $\rightarrow$ Created new Distribution `EQRDOBSCG5MC8`.<br>• Configured Origin 1 pointing to S3 Frontend Bucket with new **Origin Access Control (OAC)**.<br>• Applied S3 Bucket Policy granting exclusive read access (`s3:GetObject`) to CloudFront OAC. | • CloudFront Distribution created.<br>• OAC configuration for Private S3. |
| **Thursday (Jul 09, 2026)** | • Configured Origin 2 pointing to EC2 Backend DNS/IP (Port 5000).<br>• Added priority Cache Behavior for `/api/*` path with `CachingDisabled` and header forwarding.<br>• Authored **CloudFront Function** attached to Viewer Request rewrite URI sub-paths to `/index.html` for SPA client-side routing. | • Finalized CloudFront CDN routing.<br>• Resolved CORS & SPA 404 reloads. |
| **Friday (Jul 10, 2026)** | • Tested static frontend asset distribution via CloudFront HTTPS domain `d2onzy56n3iw1w.cloudfront.net`.<br>• Verified API `/api/health` queries forwarded seamlessly to EC2.<br>• Attended Week 6 review with Mentor. | • Smooth CDN distribution workflow.<br>• Passed Week 6 review. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Cloud Security & CDN Architecture
* **AWS IAM OIDC Identity Provider:** Zero Static Credentials security pattern using short-lived AWS STS tokens.
* **Amazon CloudFront & Origin Access Control (OAC):** Securing private S3 origins via modern OAC signatures.
* **CloudFront Edge Functions:** Edge-based URI manipulation handling React SPA client-side routing.

---

### 4. Deliverables
* IAM OIDC Provider & Role `LearnSphereGitHubDeployRole`.
* 2 Private S3 Buckets.
* CloudFront Distribution `EQRDOBSCG5MC8` with OAC, API Forwarding, and SPA Function.
