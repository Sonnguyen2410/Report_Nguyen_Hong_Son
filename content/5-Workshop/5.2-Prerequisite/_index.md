---
title : "Prerequisites"
date : 2024-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

To ensure that the LearnSphere application deployment workshop on AWS infrastructure proceeds smoothly and achieves the best results, practitioners need to prepare all required accounts, access permissions, local environment tools, and project source code according to the checklist below.

### 1. Cloud Account & Access Permission Requirements

**AWS Cloud Account (AWS Account):**
* Active AWS account, preferably within the Free Tier period to optimize costs.
* **Deployment Region:** All resources in this workshop will be created in the Singapore Region (`ap-southeast-1`).
* **IAM Permissions:** The account requires Administrator access (`AdministratorAccess`) or full administrative permissions on IAM, EC2, S3, ECR, CloudFront, CloudWatch, SNS, and Systems Manager.

**GitHub Source Control Account:**
* Personal GitHub account and a repository named `LearnSphere` initialized to store project source code.
* Access to GitHub Repository Settings to configure Repository Secrets for the CI/CD pipeline.

**MongoDB Atlas Database Account:**
* MongoDB Atlas account with an initialized database Cluster (free M0 Sandbox or M2 Shared tier).
* Standard SRV connection string retrieved for backend environment configuration.

---

### 2. Local Environment Tools Preparation

Practitioners should install and verify the following software tools on their local machine (Windows, macOS, or Linux):

**Node.js Runtime and npm:**
* Install Node.js `v24 LTS` or higher.
* Bundled npm CLI to install dependencies, run backend unit tests, and build static frontend assets.

**Git CLI Version Control:**
* Git installed and configured with personal user information (email, username).
* Successfully authenticated and linked with the project's GitHub Repository.

**Docker Desktop Container Platform:**
* Docker Desktop installed and in Running state.
* Used to build backend Docker Images and test containers locally before cloud deployment.

**AWS CLI (Version 2):**
* AWS CLI Version 2 installed.
* Used to verify IAM identity authentication and test interaction with Amazon S3 from the command line.

**Code Editor and Terminal:**
* Preferred code editor such as Visual Studio Code.
* Terminal window (macOS/Linux) or PowerShell (Windows) to execute management commands.

---

### 3. Source Code & Configuration Templates Preparation

**LearnSphere Monorepo Directory Structure:**
* Local source code must maintain a complete directory structure containing `LearnSphere_BE` (Express.js backend app), `LearnSphere_FE` (React/Vite frontend app), and `.github/workflows` (deployment automation configuration).

**Environment Configuration Template (`.env.example`):**
* Environment variables prepared for Backend including: service port `PORT=5000`, environment `NODE_ENV=production`, connection string `MONGODB_URI`, encryption key `JWT_SECRET`, domain `FRONTEND_URL`, AWS Region, and S3 Media Bucket name.

---

### 4. Readiness Checklist

Before starting the first hands-on step, review the checklist below:

- [x] Successfully logged into AWS Management Console in Singapore Region (`ap-southeast-1`).
- [x] Initialized and accessed MongoDB Atlas Cluster dashboard.
- [x] Docker Desktop and Terminal window running on local machine.
- [x] Local source code tested and compiled without errors.