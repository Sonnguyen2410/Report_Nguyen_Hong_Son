---
title: "Week 3 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

## Topic: Launching Amazon EC2 Server, Configuring S3 Bucket, and Deploying Presigned URL API

### 1. Week 3 Objectives
* Master the process of creating, configuring security, and managing Amazon EC2 virtual servers within the VPC model.
* Gain deep understanding of Amazon S3 object storage concepts and temporary access authorization using Presigned URLs.
* Deploy Docker Container pulled from Amazon ECR to the EC2 server and run the live Backend application.
* Build core API suite for Authentication, Courses, Lessons, and Presigned URL handling module integrated with AWS SDK v3.

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. Amazon EC2 (Elastic Compute Cloud) Virtual Server & Network Infrastructure
* **EC2 Instance Launch & Configuration:**
  * **AMI Selection:** Choose Ubuntu Server 22.04 LTS 64-bit (x86).
  * **Instance Type Selection:** `t2.micro` or `t3.micro` (ensuring AWS Free Tier eligibility).
  * **Elastic IP (EIP) Concept:** Fixed static public IP address. Understand why stopping/starting an instance without EIP changes the Public IP and interrupts application connectivity.
* **Security Group Configuration (EC2 Stateful Firewall):**
  * **Inbound Rule 1:** Type SSH, Port 22, Source My IP (Restricts SSH access solely to the admin's personal computer).
  * **Inbound Rule 2:** Type Custom TCP, Port 5000, Source 0.0.0.0/0 (Allows clients to call Backend RESTful APIs).
  * **Inbound Rule 3:** Type HTTP/HTTPS, Port 80/443, Source 0.0.0.0/0 (Prepares for domain/web traffic).
* **Server Connection & Authorization:**
  * Manage Key Pair (`.pem` file) security and set file permissions on Linux (`chmod 400 key.pem`).
  * **Assign IAM Role to EC2 (EC2 Instance Profile):** Grant `AmazonEC2ContainerRegistryReadOnly` policy so EC2 automatically gets permanent rights to fetch auth tokens and pull images from Amazon ECR without hardcoding AWS Access Keys on the server.
  * Install runtime environment on EC2: Docker Engine, Docker Compose, and AWS CLI v2.

#### B. Amazon S3 Service & Presigned URL Mechanism
* **Amazon S3 Bucket Configuration:**
  * Create Bucket named `ai-learning-platform-vhd` in Region `ap-southeast-1`.
  * Retain **Block All Public Access = ON** setting (Ensures all objects in the bucket remain completely hidden from the Internet).
  * Set up **CORS Configuration (Cross-Origin Resource Sharing)** on S3 Console to allow Frontend (e.g., `http://localhost:5173` or CloudFront URL) to send direct HTTP PUT requests with files to S3:
    ```json
    [
      {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "PUT", "POST", "HEAD"],
        "AllowedOrigins": ["*"],
        "ExposeHeaders": ["ETag"]
      }
    ]
    ```
* **Deep Dive into AWS S3 Presigned URLs:**
  * **Problem Statement:** Keeping the bucket Private prevents regular users from downloading files/viewing lesson videos. Making it Public allows anyone to steal data or explode S3 bandwidth costs.
  * **Presigned URL Solution:** Backend uses Secret Credentials to sign a short-lived link.
  * **Presigned Upload URL Flow:**
    1. Frontend sends a request with file name and type (`mimeType`) to Backend `/api/files/upload-url`.
    2. Backend uses `@aws-sdk/s3-request-presigner` to generate a link formatted like `https://ai-learning-platform-vhd.s3.ap-southeast-1.amazonaws.com/uploads/...&X-Amz-Signature=...`.
    3. Frontend executes `fetch(presignedUrl, { method: 'PUT', body: file })` to push the file directly to AWS S3.
  * **Presigned Download URL Flow:**
    1. When a student opens a lesson, Backend generates a GET Presigned URL link with a 900-second (15-minute) TTL.
    2. Videos / Documents render smoothly in the browser and automatically expire after 15 minutes to prevent unauthorized sharing.

---

### 3. Implementation Tasks (Work Tasks)

* **Deploy EC2 Server & Run Backend Docker Container:**
  * Launch EC2 Instance, allocate Elastic IP, and configure Security Group per design.
  * Connect via SSH: `ssh -i "learnsphere-key.pem" ubuntu@<Elastic_IP>`
  * Install Docker on EC2:
    ```bash
    sudo apt-get update
    sudo apt-get install -y docker.io
    sudo systemctl start docker
    sudo systemctl enable docker
    sudo usermod -aG docker ubuntu
    ```
  * Execute ECR login and pull Docker Container:
    ```bash
    aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com
    docker pull <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest
    docker run -d -p 5000:5000 --name backend-api --env-file .env <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:latest
    ```

* **Build `file.service.js` Module in Backend:**
  * Install AWS SDK v3 libraries: `npm install @aws-sdk/client-s3 @aws-sdk/s3-request-presigner`
  * Initialize S3 Client configured for Region `ap-southeast-1`:
    ```javascript
    import { S3Client, PutObjectCommand, GetObjectCommand } from "@aws-sdk/client-s3";
    import { getSignedUrl } from "@aws-sdk/s3-request-presigner";

    const s3Client = new S3Client({
      region: process.env.AWS_REGION,
      credentials: {
        accessKeyId: process.env.AWS_ACCESS_KEY_ID,
        secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
      },
    });
    ```
  * Write function to create Upload URL with 300s expiration (`S3_UPLOAD_URL_EXPIRES_IN=300`).
  * Write function to create Download URL with 900s expiration (`S3_DOWNLOAD_URL_EXPIRES_IN=900`).

* **Develop & Package Core RESTful APIs:**
  * **Authentication Controller (`auth.service.js` & `auth.controller.js`):**
    * Implement API `POST /api/auth/register`: Check duplicate email, hash password with `bcryptjs` (salt 10 rounds), save new User to MongoDB Atlas, return JWT Token.
    * Implement API `POST /api/auth/login`: Compare hashed password, issue JWT Token containing `userId` and `role` with 7-day expiration.
    * Implement API `GET /api/auth/me`: `auth.middleware.js` decodes `Authorization: Bearer <token>` Header to return current user info.
  * **Course Controller (`course.service.js`):**
    * Implement API `GET /api/courses`: Search and filter courses by category and status (Published).
    * Implement API `POST /api/courses`: Instructor/Admin API to create new course information.
  * **Lesson Controller (`lesson.service.js`):**
    * Implement API `POST /api/lessons`: Add new lesson to course, save `s3_key` of document/video.
    * Implement API `GET /api/lessons/:id`: Return lesson details along with secure video Presigned URL.

---

### 4. Deliverables
* EC2 Ubuntu server running stably on AWS with fixed Elastic IP, Docker Backend Container listening on port 5000.
* S3 Bucket `ai-learning-platform-vhd` set up completely Private with standard CORS configuration.
* `file.service.js` module programmed with AWS SDK v3 generating accurate, secure Presigned URLs.
* Core API suite (Auth, Course, Lesson) successfully tested via Postman on live environment `http://<Elastic_IP>:5000/api` and ready for Frontend integration.
