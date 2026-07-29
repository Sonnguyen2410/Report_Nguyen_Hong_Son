---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# [AWS ARCHITECTURE] SYSTEM ARCHITECTURE ANALYSIS OF AI-INTEGRATED LEARNING PLATFORM (LEARNSPHERE)



Hello everyone in the AWS Study Group community,

Today I would like to share a detailed architecture analysis post of the backend & frontend system for the LearnSphere / AI Learning Platform project that our team recently designed.

Unlike typical theoretical articles, this architecture combines Serverless Static Hosting, Containerized Backend on EC2, automated CI/CD pipelines, and external AI/Database services.

---

### 1. System Architecture Overview

The system is deployed on AWS Cloud in the Singapore Region (`ap-southeast-1`), ensuring low latency for users in the Southeast Asia region. The architecture is divided into 4 main components:

- **Frontend Hosting:** Amazon S3 + Amazon CloudFront (CDN)
- **Backend Services:** Amazon EC2 (inside VPC / Public Subnet) + Amazon ECR
- **CI/CD Pipeline & Monitoring:** GitHub Actions + CloudWatch
- **External Services Integration:** OpenAI API + MongoDB Atlas + S3 Storage

---

### 2. Component Details & Workflow Analysis

#### A. Frontend Layer
- **Storage:** Static frontend (React/Next.js/Vue) is stored in S3 Bucket: `learnsphere-fe-static`.
- **Content Delivery Network (CDN):** Amazon CloudFront acts as the CDN in front of the S3 bucket.
- **Access Flow:** When a USER accesses the website, the request goes through CloudFront to retrieve static data from S3. This optimizes page load speed (caching at Edge Locations) and minimizes direct bandwidth costs from S3.

#### B. Backend Layer
- **VPC & Subnet:** The system provisions a VPC with Availability Zone (`ap-southeast-1`) and a Public Subnet.
- **Compute:** Application backend runs on an Amazon EC2 Instance inside the Public Subnet, connected to the Internet via an Internet Gateway.
- **Container Registry:** Backend Docker Images are centrally stored and managed on Amazon ECR (Docker Registry). When a new version is built, the EC2 Instance pulls the Docker Image from ECR to execute.

#### C. CI/CD & Monitoring
- **GitHub Actions:** Serves as the CI/CD hub:
  - When new code is pushed, GitHub Actions automatically builds & pushes Docker Images to Amazon ECR.
  - Automatically triggers/deploys the application to the EC2 Instance.
  - Updates static assets/resources to S3 buckets.
- **CloudWatch (Logs & Alarms):** EC2 Instance forwards all application logs and system metrics to Amazon CloudWatch for monitoring, alerting, and troubleshooting.

#### D. Data & Third-Party Integration
The EC2 Backend processes application logic and interacts directly with supporting services:
- **Storage Bucket (`ai-learning-platform-vhd`):** Dedicated S3 Bucket used to store media data, lesson files, or AI platform VHDs/files.
- **OpenAI API:** Backend sends requests to OpenAI API for intelligent features (such as AI Tutor, question generation, and learning content analysis).
- **MongoDB Atlas:** Fully Managed NoSQL Database outside AWS, EC2 backend connects directly to query and store user/course data.

---

### 3. Key Strengths & Benefits

- **Decoupled Frontend & Backend Architecture:** Serving frontend via CloudFront/S3 delivers fast load speeds, completely independent of the backend server.
- **Complete CI/CD Workflow:** Using GitHub Actions combined with ECR enables Docker container packaging, ensuring consistent Dev/Staging/Prod environments and 100% automated deployment.
- **Flexible AI & Managed Service Integration:** Combines OpenAI power for smart capabilities and MongoDB Atlas for flexible data management without operating a database cluster on EC2.

---

### 4. Future Development & Optimization Suggestions

Our team is also considering several optimization points for future iterations:
- **Security:** Move EC2 Instance to a Private Subnet and place an Application Load Balancer (ALB) in the Public Subnet to enhance security.
- **Scalability:** Transition single EC2 to Auto Scaling Groups or AWS ECS / EKS services as user volume grows.
- **Database Caching:** Integrate Amazon ElastiCache (Redis) to cache API responses from OpenAI / MongoDB to reduce API call costs and speed up response times.

---

### 📷 System Architecture Diagram

![LearnSphere AWS Architecture Diagram](/images/LEARNSHPHERE.drawio.png)

---

Does anyone in the group have feedback or suggestions for our LearnSphere project architecture model? We look forward to receiving your thoughts and discussing further!

**Original Facebook Post:** [AWS Study Group Post](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222081271890166/)