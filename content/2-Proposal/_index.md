---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# LearnSphere - Smart AI-Powered E-Learning Platform
## Intelligent Online Learning Platform Integrated with AI

### 1. Executive Summary
LearnSphere is a next-generation online learning platform (E-Learning) designed to enhance teaching and learning efficiency in modern educational environments. The platform combines a full-stack web application (React/Vite & Express/MongoDB) with AWS cloud infrastructure (EC2, CloudFront, S3, ECR, AWS Systems Manager, CloudWatch, SNS), CI/CD automation via GitHub Actions, and high-speed Artificial Intelligence powered by the Groq API (LLM Inference Engine). The system supports flexible role-based access control for 3 user groups (Student, Instructor, Admin), integrating key features such as a 24/7 AI Learning Assistant, automated document extraction (PDF/Word/OCR scanned images) to generate smart quizzes, secure multimedia asset storage via S3 Media Bucket, and real-time system metrics monitoring via AWS CloudWatch combined with automated email alerts to Admin via Amazon SNS.

---

### 2. Problem Statement

#### Current Issues
Traditional E-Learning systems lack personalization and instant support capabilities for students outside of class hours. Instructors spend excessive manual time reading materials, summarizing, and drafting quiz questions for students. Furthermore, lecture documents in PDF format, Word files (.docx), or scanned image documents (OCR) are not automated for lesson data conversion. Operationally, application deployment lacks automation (no CI/CD), and storing large video files directly on servers causes system overload and complicates log management.

#### Solution
LearnSphere implements an optimized AWS production infrastructure architecture (`ap-southeast-1`): Frontend (React/Vite) is statically built, stored on Amazon S3 (`S3 Frontend`), and distributed via Amazon CloudFront CDN. Backend (Express.js) is containerized with Docker, managed on Amazon ECR, and automatically deployed to an Amazon EC2 Instance inside a VPC Public Subnet (via Internet Gateway) using GitHub Actions CI/CD combined with AWS Systems Manager (SSM) and IAM. The database utilizes MongoDB Atlas, while multimedia files and lesson documents are stored on Amazon S3 (`S3 Media`). Smart features integrated with the Groq API LLM Inference combined with text processing libraries (`pdf-parse`, `mammoth`, `tesseract.js` OCR) automate lesson summarization, power the 24/7 AI Tutor, and generate diverse Quiz question banks. The system is closely monitored via AWS CloudWatch (Logs & Alarms), automatically triggering Amazon SNS (`LearnSphere-Alerts`) to dispatch immediate notifications to Admin Gmail upon incident detection.

#### Benefits & Return on Investment (ROI)
- **Time Optimization:** Automates up to 80% of quiz/assignment creation time for instructors.
- **24/7 Learning Support:** Provides a personalized AI learning assistant 24/7 for students.
- **Operating Cost Optimization:** Docker containerization combined with CloudFront and EC2 `t2.micro`/`t3.micro` optimizes operating budgets, with estimated infrastructure costs around $8.30 – $14.80 USD/month (~$99.60 – $177.60 USD for 12 months).
- **Accelerated Deployment:** CI/CD pipeline reduces product deployment time by 90%.
- **Fast Payback:** Clear payback and effectiveness achieved within 1–3 months by saving hundreds of manual labor hours and improving educational quality.

---

### 3. Solution Architecture
The platform adopts an AWS Cloud Production-ready architecture in region `ap-southeast-1` combined with Docker containerization and CI/CD automation via GitHub Actions. The React frontend interface is distributed via Amazon CloudFront CDN backed by S3 Frontend. The Express.js backend operates on an Amazon EC2 Instance inside a VPC Public Subnet via Internet Gateway, interacting directly with MongoDB Atlas, S3 Media Bucket, Groq API (LLM Inference), and CloudWatch / SNS monitoring systems.

![LearnSphere AWS Architecture](/images/LEARNSHPHERE.drawio.png)

{{< mermaid >}}
graph TD
    subgraph Users_Dev ["Users & Deployment"]
        User["👤 USER (Student / Instructor)"]
        GitHub["🐙 GitHub (CI/CD Pipeline)"]
    end

    subgraph AWS_Cloud ["AWS Cloud Infrastructure (ap-southeast-1)"]
        IAM["🔐 IAM (Identity & Access Control)"]
        ECR["📦 Amazon ECR (Container Registry)"]
        SSM["⚙️ AWS Systems Manager"]

        subgraph Edge_Storage ["Edge & Storage Services"]
            CloudFront["⚡ Amazon CloudFront (CDN)"]
            S3_FE["🪣 S3 Frontend Bucket"]
            S3_Media["🪣 S3 Media Bucket"]
        end

        subgraph VPC ["AWS VPC (Availability Zone)"]
            subgraph PublicSubnet ["Public Subnet"]
                IGW["🌐 Internet Gateway"]
                EC2["🖥️ Amazon EC2 Instance (Docker Backend)"]
            end
        end

        subgraph Monitoring_Alerts ["Monitoring & Alerts"]
            CloudWatch["📊 AWS CloudWatch (Logs + Alarms)"]
            SNS["🔔 Amazon SNS (LearnSphere-Alerts)"]
        end
    end

    subgraph External ["External Services"]
        MongoDB["🍃 MongoDB Atlas (Cloud DB)"]
        Groq["🚀 Groq API (LLM Inference Engine)"]
        Gmail["✉️ Gmail ADMIN"]
    end

    %% User Flow
    User -->|Web Browsing| CloudFront
    CloudFront -->|Fetch Static Assets| S3_FE
    CloudFront -->|Send API Request| IGW
    IGW --> EC2
    User <-->|Upload / Download Media| S3_Media

    %% GitHub CI/CD Flow
    GitHub -->|Auth & IAM Permissions| IAM
    GitHub -->|Push Docker Image| ECR
    GitHub -->|Control & Deploy EC2| SSM
    GitHub -->|Invalidate Cache| CloudFront
    GitHub -->|Deploy Static Assets| S3_FE

    %% EC2 Core Services
    EC2 <-->|Manage Media Files| S3_Media
    EC2 <-->|Database Query| MongoDB
    EC2 <-->|AI Tutor & Quiz Gen| Groq
    EC2 -->|Push System Logs| CloudWatch

    %% System Monitoring & Notification Loop
    CloudWatch -->|Trigger Alarm| SNS
    SNS -->|Send Alert Email| Gmail
{{< /mermaid >}}

#### AWS Services & Technologies Used
- **Amazon EC2 Instance:** Hosts Backend Express.js application (running inside Docker Container) in Public Subnet of VPC (Region `ap-southeast-1`) connected via Internet Gateway.
- **Amazon CloudFront & S3 Frontend Bucket:** Distributes and stores static Frontend application (React + Vite + Tailwind CSS), accelerating response speed globally.
- **S3 Media Bucket:** Securely stores lecture videos, images, and learning materials (PDF/Word), serving direct user downloads/uploads and EC2 media management.
- **Amazon ECR (Elastic Container Registry) & AWS Systems Manager (SSM):** Official Docker Image repository and remote automated deployment agent for safe execution on EC2 Instance.
- **AWS IAM (Identity and Access Management):** Manages access control and secure authentication for GitHub Actions CI/CD workflows to AWS resources.
- **AWS CloudWatch (Logs & Alarms) & Amazon SNS (`LearnSphere-Alerts`):** Collects system logs, monitors EC2 metrics, and automatically triggers email notifications to **Gmail ADMIN**.
- **GitHub Actions:** Automates end-to-end CI/CD workflow (Build & push Docker image to ECR, deploy S3 Frontend, invalidate CloudFront cache, and command EC2 via Systems Manager).
- **Groq API Engine:** Powers high-speed AI features (Chatbot AI Tutor, lesson analysis, automated Quiz generation).
- **MongoDB Atlas:** Managed Cloud MongoDB Database storing users, courses, lessons, and quiz attempt history.

#### Component Design
- **User Management & Authorization:** JWT Authentication with 3 roles (Student, Instructor, Admin) and password recovery OTP.
- **Course & Lesson Management:** Course creation (Draft/Published), video/document upload to S3 Media, lesson ordering.
- **Document Processing & AI Engine:** Text extraction from PDF/Word/OCR scanned images (Vietnamese Tesseract OCR), sent to Groq API for auto-summarization and quiz generation.
- **Quiz Execution & Automated Grading:** Interactive Quiz system (Multiple choice, True/False, Fill-in-the-blank, Essay) with auto-grading and history tracking.
- **CI/CD Pipeline, SSM & SNS Monitoring:** GitHub Actions automatically tests, pushes to ECR, and deploys via Systems Manager to EC2; CloudWatch & SNS monitor system 24/7 and send immediate alerts to Gmail Admin.


---

### 4. Technical Implementation

#### Implementation Phases
The project consists of 2 parts — building AWS Infrastructure / CI/CD Pipeline and developing the AI-integrated Full-stack Web application — executed across 4 phases within 2 internship months:

1. **Research & Architecture Design:** System requirements analysis, Database Schema Design (11 Models), API Design, and AWS VPC architecture diagram (Month 1 / Weeks 1–2).
2. **AWS Infrastructure & CI/CD Setup:** Initialize S3 Buckets, Amazon ECR, EC2 Instance, CloudFront, and write GitHub Actions workflow to automatically build Docker image (Month 1 / Weeks 2–3).
3. **Core Services Development & OpenAI Integration:** Build Backend API (Auth, Course, Lesson, S3 Presigned URL), connect OpenAI API, OCR/PDF parsing module, and build React/Vite UI (Month 1 / Week 4 - Month 2 / Week 6).
4. **Testing, CloudWatch Monitoring & Deployment:** Configure CloudWatch Logs & Alarms, End-to-End system testing on EC2, performance optimization, and documentation packaging (Month 2 / Weeks 7–8).

#### Technical Requirements
- **Backend & Infrastructure:** Node.js 18+, Express 5, Mongoose 9, Docker, `@aws-sdk/client-s3`, `@aws-sdk/client-cloudwatch-logs`, OpenAI SDK, `tesseract.js` (`vie`), `mammoth`, `pdf-parse`. `.env` configuration for S3 Buckets (`ap-southeast-1`), ECR URI, and OpenAI API Key.
- **Frontend & CI/CD:** React 18, TypeScript, Vite, Tailwind CSS, KaTeX. GitHub Actions workflow configured with AWS Secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`).

---

### 5. Roadmap & Key Milestones

```text
[Month 0 / Pre-Internship] ──► [Month 1 / Weeks 1-4] ──► [Month 2 / Weeks 5-8] ──► [Post-Deployment]
  Planning & Design             AWS Infra & Core API     OpenAI, CI/CD & Test      1-Year Operation
```

- **Pre-Internship (Month 0):** 1 month planning, requirements documentation, and preliminary AWS architecture design.
- **Internship (Months 1–2):**
  - **Month 1:**
    - *Weeks 1–2:* Complete Database Schema design, API Design, and EC2/S3/CloudFront VPC diagram.
    - *Weeks 3–4:* Initialize AWS infrastructure (ECR, EC2, CloudFront), configure GitHub Actions CI/CD, and write basic Backend APIs.
  - **Month 2:**
    - *Weeks 5–6:* Integrate OpenAI API, PDF/Docx/OCR document extraction, AI Assistant, and Auto Quiz Generation on React Frontend.
    - *Weeks 7–8:* Configure AWS CloudWatch Logs & Alarms, End-to-End system testing on EC2, performance optimization, and finalize report.
- **Post-Deployment:** Maintain system on AWS, collect user feedback, and expand features over 1 year.

---

### 6. Budget Estimation

Estimated cloud computing service costs available on AWS Pricing Calculator.

#### Infrastructure Costs

| Cloud / AI Service | Specification Details | Cost / Month (USD) |
| --- | --- | --- |
| **Amazon EC2 (t2.micro / t3.micro)** | Free Tier or ~0.0116 USD/hour | 4.50 - 8.50 USD |
| **Amazon CloudFront & S3 Static (`learnsphere-fe-static`)** | Frontend hosting and CDN data transfer | 0.50 USD |
| **Amazon S3 Standard (`ai-learning-platform-vhd`)** | 10 GB media/document storage | 0.30 USD |
| **Amazon ECR & CloudWatch** | Docker image storage and log collection | 0.50 USD |
| **OpenAI API** | ~300,000 input/output tokens for AI Tutor & Quiz Gen | 2.50 - 5.00 USD |
| **MongoDB Atlas (Shared Cluster)** | Free Tier M0 | 0.00 USD |
| **Total Monthly Cost** | | **8.30 – 14.80 USD/month** |
| **Total Annual Cost (12 Months)** | | **99.60 – 177.60 USD/year** |

#### Hardware / Software Costs
- **Hardware & Software Cost:** $0 USD (Utilizing existing hardware and open-source tools).

---

### 7. Risk Assessment

#### Risk Matrix

| Identified Risk | Impact Level | Likelihood |
| --- | --- | --- |
| EC2 Instance Downtime | High | Low |
| OpenAI API Token Budget Overrun | High | Low |
| OCR extraction errors on blurry scan documents | Medium | Medium |
| GitHub Actions CI/CD Pipeline Failure | Medium | Low |

#### Mitigation Strategies
- **EC2 Management:** Configure Docker auto-restart policy (`restart: always`), create CloudWatch Alarm when CPU/RAM exceeds 85%.
- **OpenAI Budget:** Configure Max Tokens limit, apply Rate Limiting on API requests, and cache common AI responses.
- **OCR Documents:** Preprocess text, display warning to users if uploaded file is too blurry.
- **CI/CD & Security:** Test Docker build locally before pushing, enforce AWS IAM least privilege, and store keys in GitHub Secrets.

#### Contingency Plans
- Automatically recover container or restart EC2 via CloudWatch Actions if instance crashes.
- Provide `QuestionBuilder` tool for instructors to manually craft/edit questions when uploaded files cannot be extracted by AI.

---

### 8. Expected Outcomes

- **Technical Improvements:** Successfully built a Docker/AWS Cloud standard E-Learning system, automatically deployed via GitHub Actions CI/CD, automated learning material workflows using OpenAI API, and monitored via CloudWatch.
- **Long-term Value:** AWS EC2 + Docker infrastructure ready to scale (Auto Scaling Group / ECS / EKS) for thousands of students, serving as a foundation for future EdTech research and products.