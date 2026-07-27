---
title: "Week 2 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

## Topic: Packaging Docker Container for Backend and Pushing Image to Amazon ECR

### 1. Week 2 Objectives
* Master foundational knowledge of Containerization technology with Docker and how to package Node.js/Express applications.
* Gain proficiency in AWS Amazon ECR (Elastic Container Registry) to manage Docker Image repositories.
* Successfully package the `LearnSphere_BE` application into a Docker Image, test container execution in Localhost environment, and successfully push the Image to Amazon ECR.

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. Application Packaging with Docker (Containerization)
* **Basic Docker Concepts:**
  * Differences between Virtual Machines (VM) and Docker Containers (lightweight, faster startup, shared OS Kernel).
  * Distinguish between **Docker Image** (Blueprint/Template) and **Docker Container** (Running instance).
* **Dockerfile Optimization for Node.js:**
  * Learn to choose lightweight Base Images (e.g., `node:18-alpine` or `node:18-slim`).
  * Use `.dockerignore` to exclude `node_modules`, `.env`, `.git`, and junk files to reduce Image size.
  * Leverage **Docker Layer Caching** (place `COPY package*.json` before `RUN npm install` then `COPY . .`) to accelerate re-builds.
  * Set up secure execution environment (Non-root user) and container launch command (`CMD ["npm", "start"]`).

#### B. Amazon ECR (Elastic Container Registry) Service
* **Amazon ECR Concepts:**
  * What is Amazon ECR? The role of ECR in AWS infrastructure architecture (Private and secure Docker Image registry).
  * ECR authorization mechanism with AWS IAM (permissions `ecr:GetAuthorizationToken`, `ecr:BatchCheckLayerAvailability`, `ecr:PutImage`, etc.).
* **CLI Operations Connecting to Amazon ECR:**
  * Use AWS CLI to retrieve authentication token and log Docker into ECR:
    ```bash
    aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com
    ```
  * How to tag Images matching the ECR URI structure:
    ```bash
    docker tag learnsphere-be:latest <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest
    ```
  * How to push Images to ECR (`docker push`) and pull Images (`docker pull`).

---

### 3. Implementation Tasks (Work Tasks)

* **Build Docker Configuration for Backend (`LearnSphere_BE`):**
  * Write `Dockerfile` inside `LearnSphere_BE` directory:
    * Initialize Node.js environment.
    * Install required packages (including OCR/Tesseract dependencies if applicable).
    * Expose port 5000.
  * Write `.dockerignore` file to optimize the build process.

* **Localhost Containerization Testing:**
  * Run local image build command: `docker build -t learnsphere-be:v1.0 .`
  * Run test container: `docker run -d -p 5000:5000 --env-file .env learnsphere-be:v1.0`
  * Use Postman / Browser to verify basic routes (`http://localhost:5000/api/health`) and connectivity to MongoDB Atlas.

* **Initialize Amazon ECR Repository & Push Image:**
  * Access AWS Management Console -> Amazon ECR Service.
  * Create a new Private Repository named `learnsphere-be` in Region `ap-southeast-1`.
  * Execute a sequence of AWS CLI commands on local machine to authenticate, tag, and push Docker Image `learnsphere-be:v1.0` (or tag `latest`) to Amazon ECR.

---

### 4. Deliverables
* Standardized `Dockerfile` and `.dockerignore` for `LearnSphere_BE`.
* Backend container running successfully in Localhost environment with stable MongoDB Atlas connection.
* Repository `learnsphere-be` successfully created on Amazon ECR (`ap-southeast-1`).
* First project Docker Image successfully pushed to Amazon ECR and ready to be pulled to EC2 server in Week 3.
