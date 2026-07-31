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
LearnSphere is a next-generation online learning platform (E-Learning) designed to enhance teaching and learning efficiency in modern educational environments. The platform combines a full-stack web application (React/Vite & Express/MongoDB) with a High Availability AWS cloud architecture (Multi-AZ VPC, Application Load Balancer, Auto Scaling, EC2, CloudFront, S3, ECR, CloudWatch, SNS), passwordless CI/CD automation (OIDC) via GitHub Actions, and high-speed Artificial Intelligence powered by the Groq API (Llama 3 Inference Engine). The system supports flexible role-based access control for 3 user groups (Student, Instructor, Admin), integrating key features such as a 24/7 AI Learning Assistant, automated document extraction (PDF/Word/OCR scanned images) to generate smart quizzes, secure multimedia asset storage via S3 Media Bucket, and real-time system metrics monitoring via AWS CloudWatch combined with automated email alerts to Admin via Amazon SNS.

---

### 2. Problem Statement

#### Current Issues
Traditional E-Learning systems lack personalization and instant support capabilities for students outside of class hours. Instructors spend excessive manual time reading materials, summarizing, and drafting quiz questions for students. Furthermore, lecture documents in PDF format, Word files (.docx), or scanned image documents (OCR) are not automated for lesson data conversion. Operationally, application deployment lacks automation, storing large video files directly on servers causes system overload, and deploying on a single server (Single AZ) poses a high risk of service disruption (Single Point of Failure).

#### Solution
LearnSphere implements an optimized AWS production infrastructure architecture (`ap-southeast-1`) adhering to High Availability standards: Frontend (React/Vite) is statically built, stored on Amazon S3 (`learnsphere-fe-2`), and distributed via Amazon CloudFront CDN. Backend (Express.js) is containerized with Docker, managed on Amazon ECR, and automatically deployed to a cluster of Amazon EC2 Instances within an **Auto Scaling Group** located in **Private Subnets** (Multi-AZ). Users connect to the Backend through an **Application Load Balancer (ALB)** situated in the **Public Subnets**, while the Backend servers communicate with the Internet via NAT Gateways. The CI/CD process is fully automated via GitHub Actions utilizing **AWS OIDC** and the **Instance Refresh** feature of Auto Scaling. The database utilizes MongoDB Atlas with strict IP Access List controls, and media files are stored on Amazon S3 (`learnsphere-media-2`). Smart features integrated with the Groq API combined with text processing libraries (`pdf-parse`, `mammoth`, `tesseract.js`) power the 24/7 AI Tutor and automate Quiz generation. The entire system is closely monitored via AWS CloudWatch (Logs & Alarms), automatically triggering Amazon SNS to dispatch immediate alert emails upon incident detection.


#### Benefits & Return on Investment (ROI)
- **Time Optimization:** Automates up to 80% of quiz/assignment creation time for instructors using Groq AI.
- **High Availability:** Multi-AZ architecture with Auto Scaling ensures the application is always serving traffic, automatically recovering (self-healing) from server hardware failures.
- **Absolute Security:** Backend is hidden in Private Subnets with no Public IPs. CI/CD uses OIDC, eliminating the risk of leaked AWS Access Keys.
- **Accelerated Deployment:** Automated CI/CD pipeline (build, push ECR, instance refresh, CloudFront invalidation) reduces new feature release time by 90%.

---

### 3. Solution Architecture
The platform adopts an AWS Cloud Production-ready High Availability architecture in region `ap-southeast-1`. The React frontend interface is distributed via Amazon CloudFront CDN (with OAC) backed by S3 Frontend. The Express.js backend operates on EC2 instances (Auto Scaling Group) hidden in Private Subnets, receiving safe traffic from the Application Load Balancer. The backend utilizes NAT Gateways to call external APIs such as Groq and MongoDB Atlas.

![LearnSphere AWS Architecture](/images/LEARNSHPHERE.png)

{{< mermaid >}}
graph TD
    subgraph Users_Dev ["Users & Deployment"]
        User["👤 USER (Student / Instructor)"]
        GitHub["🐙 GitHub (CI/CD Pipeline via OIDC)"]
    end

    subgraph AWS_Cloud ["AWS Cloud Infrastructure (ap-southeast-1)"]
        IAM["🔐 IAM (OIDC Trust & Roles)"]
        ECR["📦 Amazon ECR (Container Registry)"]

        subgraph Edge_Storage ["Edge & Storage Services"]
            CloudFront["⚡ Amazon CloudFront (CDN)"]
            S3_FE["🪣 S3 Frontend Bucket (OAC)"]
            S3_Media["🪣 S3 Media Bucket (CORS)"]
        end

        subgraph VPC ["AWS VPC (Multi-AZ)"]
            subgraph PublicSubnets ["Public Subnets (AZ-a & AZ-b)"]
                IGW["🌐 Internet Gateway"]
                ALB["⚖️ Application Load Balancer"]
                NAT["🔄 NAT Gateways"]
            end
            
            subgraph PrivateSubnets ["Private Subnets (AZ-a & AZ-b)"]
                ASG["⚙️ Auto Scaling Group (EC2 Backend)"]
            end
        end

        subgraph Monitoring_Alerts ["Monitoring & Alerts"]
            CloudWatch["📊 AWS CloudWatch (Logs + Alarms)"]
            SNS["🔔 Amazon SNS (Alerts Topic)"]
        end
    end

    subgraph External ["External Services"]
        MongoDB["🍃 MongoDB Atlas (Cloud DB)"]
        Groq["🚀 Groq API (LLM Inference Engine)"]
        Gmail["✉️ Gmail ADMIN"]
    end

    %% User Flow
    User -->|Web Browsing HTTPS| CloudFront
    CloudFront -->|Fetch Static Assets| S3_FE
    CloudFront -->|Send API Request| ALB
    ALB -->|TCP 5000 Load Balancing| ASG
    User <-->|Upload / Download Media Directly| S3_Media

    %% GitHub CI/CD Flow
    GitHub -->|OIDC Authentication| IAM
    GitHub -->|Push Docker Image| ECR
    GitHub -->|Trigger Instance Refresh| ASG
    GitHub -->|Invalidate Cache| CloudFront
    GitHub -->|Deploy Static Assets| S3_FE

    %% Backend Flow
    ASG -->|Egress| NAT
    NAT --> IGW
    NAT <-->|Manage Media Files| S3_Media
    NAT <-->|Database Query| MongoDB
    NAT <-->|AI Tutor & Quiz Gen| Groq
    ASG -->|Push Logs & Metrics| CloudWatch

    %% System Monitoring & Notification Loop
    CloudWatch -->|Trigger Alarm| SNS
    SNS -->|Send Alert Email| Gmail
{{< /mermaid >}}

#### AWS Services & Technologies Used
- **VPC Multi-AZ & Networking:** VPC consists of 2 Public Subnets and 2 Private Subnets spanning 2 Availability Zones, combining Internet Gateways and NAT Gateways to provide secure outbound connectivity.
- **Application Load Balancer (ALB):** Receives secure HTTPS traffic (ACM certificate) and routes it to the backend cluster.
- **Auto Scaling Group & EC2:** Automatically adjusts the scale of `t3.small` servers based on a Launch Template, ensuring High Availability and automatically replacing faulty servers.
- **Amazon CloudFront & S3 Frontend:** Securely distributes the React application using Origin Access Control (OAC), caching at the Edge.
- **S3 Media Bucket:** Stores courses, images, and documents (with CORS rules enabling client-side uploads via presigned URLs).
- **Amazon ECR (Elastic Container Registry):** Manages versioning for Backend Docker images.
- **AWS IAM (OIDC):** Secure authentication for the CI/CD pipeline from GitHub Actions using OpenID Connect, eliminating static Secret Keys.
- **AWS CloudWatch & Amazon SNS:** Monitors system logs (CloudWatch Logs Agent) and alerts on ALB/ASG health status via email.
- **Groq API Engine:** High-speed Artificial Intelligence supporting Chatbots and lesson analysis.
- **MongoDB Atlas:** Shared Cloud Database for the entire EC2 cluster, protected by an IP Access List (accepting traffic only from NAT Gateway IPs).

#### Component Design
- **User Management & Authorization:** JWT Authentication with 3 roles (Student, Instructor, Admin) and OTP delivery.
- **Course & Media Management:** Course creation, direct large video uploads from the browser to S3.
- **Document Processing & AI Engine:** Text extraction from PDF/Docx/Images (Vietnamese Tesseract OCR), processed through the Groq LLM to summarize and generate multiple-choice questions.
- **Zero-Downtime CI/CD Pipeline:** GitHub Actions CI/CD performs build/push to ECR, then triggers AWS Auto Scaling `start-instance-refresh` to smoothly roll out new server replacements.


---

### 4. Technical Implementation

#### Implementation Phases
The project is deployed using a practical production architecture encapsulated within the internship period:

1. **System Design & AWS Networking:** Requirements analysis, Database design, modeling VPC Multi-AZ, Public/Private Subnets, Internet/NAT Gateways, and Security Groups.
2. **Application & Docker Development:** Develop Backend (Express.js, AI, OCR, S3 Integration) packaged in Docker; develop Frontend (React, Vite, Tailwind).
3. **AWS HA Infrastructure Deployment:** Configure ECR, Launch Template, ALB, Target Groups, Auto Scaling Group; setup CloudFront (OAC) and ACM certificates (HTTPS).
4. **CI/CD Automation & Monitoring:** Grant IAM OIDC permissions for GitHub Actions for automated deployment (Frontend S3, Backend ASG Refresh). Configure CloudWatch Alarms & SNS Topic for availability monitoring.
5. **Testing & Handover:** Execute E2E workflows on Production, verify Auto Scaling Self-Healing capabilities, and finalize the Workshop report.

#### Technical Requirements
- **Backend:** Node.js 18+, Express 5, Mongoose 9, Docker, `@aws-sdk/client-s3`, `@aws-sdk/client-ssm`, Groq SDK, `tesseract.js` (`vie`), `mammoth`, `pdf-parse`.
- **Frontend & CI/CD:** React 18, TypeScript, Vite, Tailwind CSS, KaTeX. GitHub Actions (aws-actions/configure-aws-credentials with Role-to-assume).

---

### 5. Deployment Roadmap (9-Week Worklog)

The detailed deployment progress can be tracked in the **[Worklog](../../1-worklog/)** section:
- **Weeks 1-4:** Database Design, API development, AI applications, S3 Media handling, and React Frontend interface.
- **Week 5:** Build Multi-AZ VPC Infrastructure, IAM Roles, Container packaging, and Amazon ECR.
- **Week 6:** Deploy High Availability Backend with Application Load Balancer & Auto Scaling Group.
- **Week 7:** Deploy CloudFront Frontend, Custom Domain, and GitHub Actions CI/CD Automation via OIDC.
- **Week 8:** Integrate MongoDB Atlas (IP Access List), CloudWatch/SNS Monitoring, Production E2E Testing, and Clean-up procedures.
- **Week 9:** Project Summary, drafting Hugo documentation report, and final defense before the Mentor Board.

---

### 6. Cost Analysis (High Availability Optimization)

When the application runs on a High Availability (Multi-AZ) architecture, the cost structure focuses on stability rather than maximum savings as in a Dev environment.

| Cloud / AI Service | Design Specifications (ap-southeast-1 Region) | Estimated Cost |
| --- | --- | --- |
| **Amazon EC2 (t3.small)** | Minimum 2 instances in Auto Scaling Group | ~ $30.00 / month |
| **Application Load Balancer** | Traffic distribution across 2 AZs (incl. LCUs) | ~ $22.00 / month |
| **NAT Gateways (2 AZs)** | Provides Internet connectivity for Private Subnets | ~ $65.00 / month |
| **Amazon CloudFront & S3** | Static Frontend/Media hosting and CDN Egress | ~ $1.00 / month |
| **CloudWatch, SNS, ECR, Route 53** | Log collection, image storage, DNS resolution | ~ $2.00 / month |
| **Groq API** | High-speed Llama 3 inference service (Beta Free) | $0.00 / month |
| **MongoDB Atlas** | Cloud DB Cluster (M0 Free Tier) | $0.00 / month |
| **Total Monthly Cost** | Typical cost for a Production HA cluster | **~ $120.00 / month** |

*Note: For learning purposes, the entire architecture can be spun up and torn down within a few workshop hours to minimize costs.*

---

### 7. Risk Assessment and Mitigation

| Risk (Technical Risk) | Level | Mitigation & Architecture Remediation Strategy |
| --- | --- | --- |
| EC2 Server Overload / Hardware Failure | Low | (Self-Healing) Auto Scaling Group detects Unhealthy status via Target Group and automatically replaces the instance; traffic remains uninterrupted. |
| Single Availability Zone (AZ) Outage | Very Low | (Multi-AZ) ALB automatically routes all traffic to the healthy EC2 instances in the remaining AZ. |
| AWS Credentials Leakage | Low | (Security) Enforce OIDC for CI/CD. No static Access Key/Secret Key is stored on GitHub. Backend EC2 uses IAM Role attached via Instance Profile. |
| Deployment Error on New Release | Medium | (Zero Downtime) Utilize the ASG Instance Refresh feature, performing a rolling deployment to ensure no application downtime. |

---

### 8. Expected Outcomes

- **Cloud Engineer Standards:** Successfully complete the E-Learning solution from concept and source code to automated CI/CD deployment on a High Availability AWS Production infrastructure, complying with the highest security rules (Private VPC, OIDC).
- **Artificial Intelligence Integration:** Validate capabilities of integrating Generative AI (GenAI) into learning workflows to solve educational challenges (Personalized Learning Assistant, Automated Quiz Generation).
- **Productization Readiness:** The system is fully packaged with systematic Monitoring procedures, serving as a robust reference architecture for future real-world EdTech projects.