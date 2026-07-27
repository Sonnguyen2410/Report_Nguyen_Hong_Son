---
title: "Hands-on Steps"
date: 2026-07-27
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Overview

In this section, you will directly execute 11 sequential, detailed hands-on steps to migrate the complete LearnSphere system from your local development environment to AWS Cloud infrastructure in the Singapore region (`ap-southeast-1`).

You will master hands-on workflows across two primary interaction environments:

- **AWS Management Console & SSM Session Manager:** Provision and connect storage services (S3), container registries (ECR), CDN networks (CloudFront), monitoring services (CloudWatch, SNS), and control EC2 servers securely via encrypted Systems Manager channels without opening SSH port 22.
- **Terminal CLI & GitHub Actions Pipeline:** Execute source code testing commands, package Multi-stage Docker Images, declare secure environment variables, and automate the entire Continuous Integration & Continuous Deployment (CI/CD) pipeline with automated rollback capabilities.

---

### Content

1. [Step 1: Local Source Code & Multi-stage Dockerfile Preparation](5.4.1-Local-Build-Dockerfile/)
2. [Step 2: Access Permission & Security Setup (AWS IAM & OIDC)](5.4.2-IAM-OIDC-Setup/)
3. [Step 3: Create & Configure Amazon S3 (Media & Frontend Buckets)](5.4.3-S3-Buckets/)
4. [Step 4: Create Amazon ECR Repository](5.4.4-ECR-Repository/)
5. [Step 5: Launch & Configure EC2 Server (Swap RAM & SSM Engine)](5.4.5-EC2-Initialization/)
6. [Step 6: Configure MongoDB Atlas Database Connection](5.4.6-MongoDB-Atlas/)
7. [Step 7: Configure Amazon CloudFront (CDN, OAC & SPA Routing)](5.4.7-CloudFront-Distribution/)
8. [Step 8: Configure Backend Environment & Permissions on EC2](5.4.8-EC2-Environment/)
9. [Step 9: Configure GitHub Secrets & CI/CD Pipeline](5.4.9-CICD-Pipeline/)
10. [Step 10: Production Verification & Product Testing](5.4.10-Verification-Testing/)
11. [Step 11: Setup System Monitoring & Alerting (CloudWatch Alarms & Amazon SNS)](5.4.11-Monitoring-Alerts/)
