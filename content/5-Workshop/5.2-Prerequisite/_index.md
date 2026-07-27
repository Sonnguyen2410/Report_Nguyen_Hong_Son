---
title : "Detailed Prerequisites"
date : 2024-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

To ensure that the infrastructure deployment for the LearnSphere application on AWS cloud proceeds smoothly without interruptions or technical issues, practitioners should carefully review all accounts, permissions, local tools, and source code structure according to the detailed requirements below.

### 1. Cloud Accounts & Access Permission Details

**AWS Cloud Account (AWS Account & IAM Permissions):**
* **Account Status:** The AWS account must be active without outstanding bills or service restrictions. Accounts within the 12-month AWS Free Tier are preferred to optimize testing costs.
* **Deployment Region:** Singapore (`ap-southeast-1`) is strongly recommended for all services to reduce latency to Vietnam and ensure data synchronization across EC2, S3, ECR, and CloudWatch.
* **Access Account:** It is recommended to use an IAM User account with Administrator privileges (`AdministratorAccess`) or dedicated permission policies listed below, rather than using the AWS Root Account directly:
  * **IAM:** Permissions to create OIDC Providers, IAM Roles, and attach Instance Profiles.
  * **EC2:** Permissions to launch Instances, Security Groups, Elastic IPs, and integrate SSM.
  * **S3:** Permissions to create Buckets, configure CORS, Bucket Policies, and OAC.
  * **ECR:** Permissions to create Repositories, push/pull Docker Images, and configure Lifecycle Policies.
  * **CloudFront:** Permissions to create Distributions, OAC, CloudFront Functions, and Create Invalidations.
  * **CloudWatch & SNS:** Permissions to create Log Groups, Alarms, SNS Topics, and Email Subscriptions.
  * **Systems Manager (SSM):** Permissions to execute remote commands via RunCommand.

**GitHub Source Control Account (Repository & CI/CD Access):**
* Personal or organization Repository named `LearnSphere` created on GitHub.
* Default source code branch unified as `main`.
* Access to Repository **Settings** -> **Secrets and variables** -> **Actions** to configure security parameters for automated deployment pipelines.
* **OIDC Authentication Mechanism:** GitHub Actions automatically generates a web identity token to `token.actions.githubusercontent.com` with audience `sts.amazonaws.com` for AWS authentication.

**Cloud MongoDB Atlas Database Account:**
* Project and database Cluster created on MongoDB Atlas (free M0 Sandbox or M2/M5 Shared cluster).
* **Cluster Region:** Choose a cluster location close to `ap-southeast-1` (AWS Singapore cluster preferred) to maximize EC2-to-database query performance.
* **Database Access User:** Dedicated Production Database User created with read/write privileges (`readWriteAnyDatabase`) on the `learnsphere` database, using a strong password.
* **Connection String:** Standard SRV connection string copied and ready for environment configuration.

---

### 2. Local Environment Tools & Setup Details

Practitioners should prepare a local computer (Windows 10/11, macOS, or Linux) with the following CLI tools installed:

**Node.js Runtime & npm Package Manager:**
* **Recommended Version:** Node.js `v24.x LTS` or higher, with npm `v10.x` or higher.
* **Purpose:** Test Express.js application locally, install backend/frontend dependencies, run automated Unit Tests, and build static React SPA bundles.

**Git CLI Version Control Tool:**
* Latest Git installed and configured with personal identity (`user.name` and `user.email`).
* **Ignore File (`.gitignore`):** Strictly configured at root, Backend, and Frontend folders to prevent pushing sensitive files (`.env`), library folders (`node_modules`), temporary files, or build artifacts (`dist`) to GitHub.

**Docker Desktop Container Platform:**
* Latest Docker Desktop (Windows/macOS) or Docker Engine (Linux).
* **Resource Allocation:** Allocate at least 4GB RAM and 2 CPU Cores to Docker Daemon to ensure fast multi-stage Docker builds for Backend.
* **Status:** Docker Daemon must be in Running state. Used for local image builds and Dockerfile validation before pushing code to the pipeline.

**AWS CLI (Version 2):**
* Standard AWS CLI v2 installed for the respective operating system.
* Pre-configured via `aws configure` with default region `ap-southeast-1` and output format `json`.
* Used to verify IAM permissions, test S3 connectivity, and execute quick infrastructure operations from local CLI.

**Code Editor and Terminal Tools:**
* Visual Studio Code (VS Code) with recommended extensions: Docker, GitLens, YAML, Prettier, and Environment Sensitive Settings.
* Dedicated terminal window such as PowerShell (Windows) or Bash/Zsh Terminal (macOS/Linux).

---

### 3. Source Code Structure & Environment Template Details

**LearnSphere Monorepo Project Tree:**
* **`LearnSphere_BE` Folder:** Backend Node.js/Express source code, main entry point `src/server.js`, route handlers `routes`, controllers `controllers`, Mongoose `models`, health check endpoint `/health/ready`, and container `Dockerfile`.
* **`LearnSphere_FE` Folder:** Frontend React/Vite source code, TypeScript configuration, Dashboard admin panel, video lesson viewer, Quiz examination UI, `vite.config.ts`, and frontend environment variables.
* **`.github/workflows` Folder:** Deployment automation file `deploy.yml` orchestrating the entire CI/CD pipeline from ECR image push to EC2 deployment via SSM.

**Environment Variables Template (`.env.example`):**
* Mandatory Backend parameters:
  * Service Port (`PORT`): `5000`.
  * Execution Environment (`NODE_ENV`): `production`.
  * Proxy Authorization (`TRUST_PROXY`): `true` (enables CloudFront header detection).
  * MongoDB Connection String (`MONGODB_URI`): SRV connection string to MongoDB Atlas.
  * JWT Secret Key (`JWT_SECRET`): Random string of at least 64 characters.
  * Frontend Domain (`FRONTEND_URL`): Official HTTPS address provided by CloudFront.
  * AWS Region (`AWS_REGION`): `ap-southeast-1`.
  * S3 Media Bucket Name (`AWS_S3_BUCKET`): Unique bucket name for media storage.
  * AI Service Key (`GROQ_API_KEY`): API Key for AI Tutor features.

---

### 4. Pre-flight Readiness Checklist

Before proceeding to **Section 5.3 (Architecture Description)** and **Section 5.4 (Hands-on Steps)**, verify 100% compliance with the checklist below:

- **AWS Account:** Logged into AWS Management Console in Singapore Region (`ap-southeast-1`) using appropriate IAM credentials.
- **Database:** Successfully tested connection to MongoDB Atlas Cluster via MongoDB Compass or local test script.
- **Local Codebase:** Ran backend tests successfully and compiled frontend `dist` directory without TypeScript errors.
- **Local Docker:** Built backend Docker Image locally and verified 200 OK response on `/health/ready` endpoint.
- **GitHub Repository:** Code pushed to `main` branch of GitHub Repository and ready for OIDC & Repository Secrets setup.
- **Local Machine:** Terminal, web browser, and Docker Desktop open and ready for work.