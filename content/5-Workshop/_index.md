---
title: "Workshop"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Main Technical Project: Production-Grade LearnSphere System Deployment and Operation on AWS Infrastructure

#### Overview

**LearnSphere** is a modern online learning platform (E-Learning Platform) designed following a Monorepo architecture (React/Vite SPA Frontend and Node.js/Express REST API Backend), integrated with **MongoDB Atlas** database and advanced AI features.

In this workshop, we will deploy the complete LearnSphere application to **AWS cloud infrastructure (Singapore Region - ap-southeast-1)** adhering to top industry technical standards in production operations:

* **Zero Static Credentials**: Automate authentication between GitHub Actions and AWS STS via **OpenID Connect (OIDC)**; assign **IAM Instance Profile (IMDSv2)** to EC2 to completely eliminate static Access Keys from source code or environment variables.
* **Network Security & Remote Management**: Completely block inbound SSH (Port 22) access from the Internet; control and execute scripts on EC2 100% securely via **AWS Systems Manager (SSM) Session Manager**.
* **CDN Distribution Optimization (Single Domain)**: Distribute static Frontend assets from S3 Private via **CloudFront Origin Access Control (OAC)** and proxy `/api/*` requests to EC2 port 5000, eliminating CORS and Mixed Content issues.
* **CI/CD Pipeline & Auto-Rollback**: Fully automate Multi-stage Docker packaging, ECR push, Candidate Container testing (port 5001) with an automatic Zero-Downtime Rollback mechanism.
* **Proactive Monitoring**: Automatically collect application logs into **CloudWatch Logs** and dispatch immediate notifications to administrator email via **CloudWatch Alarms** and **Amazon SNS**.

---

#### Table of Contents

1. [5.1. Overview](5.1-Overview/)
2. [5.2. Prerequisites](5.2-Prerequisite/)
3. [5.3. Architecture Description](5.3-Architecture/)
4. [5.4. Hands-on Steps](5.4-Hands-on/)
5. [5.5. Clean-up](5.5-Cleanup/)