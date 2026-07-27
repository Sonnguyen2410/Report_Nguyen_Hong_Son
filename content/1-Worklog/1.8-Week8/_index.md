---
title: "Worklog Week 8"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

## Topic: CloudWatch/SNS Monitoring, Production Verification & Resource Clean-up

### 1. Week 8 Objectives (Jul 20, 2026 – Jul 24, 2026)
* Create **Amazon SNS Topic** (`LearnSphere-Alerts`) and confirm administrator Email Subscriptions.
* Provision 2 **CloudWatch Alarms** (`LearnSphere-EC2-HighCPU` & `LearnSphere-EC2-StatusCheckFailed`) monitoring EC2 resources.
* Aggregate Docker container application logs into **Amazon CloudWatch Logs** (`/learnsphere/backend`).
* Execute End-to-End production application verification on official domain **`https://www.learnsphere.id.vn/`**.
* Master AWS Resource Clean-up procedures following **Reverse Dependency Order** and produce Clean-up Checklist.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jul 20, 2026)** | • Opened AWS Console $\rightarrow$ **Amazon SNS** $\rightarrow$ Created new Topic `LearnSphere-Alerts`.<br>• Created Email Subscription pointing to administrator email address (`son.nguyenhong2410@hcmut.edu.vn`).<br>• Verified email inbox and clicked **Confirm subscription**. | • Functional SNS Topic `LearnSphere-Alerts`.<br>• Confirmed Email Subscription. |
| **Tuesday (Jul 21, 2026)** | • Opened **CloudWatch Console** $\rightarrow$ Created Alarm 1 `LearnSphere-EC2-HighCPU` (triggers if CPU > 80% for 10 min).<br>• Created Alarm 2 `LearnSphere-EC2-StatusCheckFailed` (triggers if EC2 hardware/network fails for 60s).<br>• Configured Docker AWS Logs Driver pushing container logs to CloudWatch Log Group `/learnsphere/backend`. | • 2 CloudWatch Alarms in OK status.<br>• Centralized Log Group `/learnsphere/backend`. |
| **Wednesday (Jul 22, 2026)** | • Navigated to official domain **`https://www.learnsphere.id.vn/`** for End-to-End product verification.<br>• Tested workflows: Auth Login/Register, Course Creation, Media upload via Presigned PUT URLs, Video streaming via Presigned GET URLs, Quiz Runner, and AI Tutor Chat. | • Product operating live on AWS.<br>• 100% stable feature performance. |
| **Thursday (Jul 23, 2026)** | • Studied AWS Cloud Resource Clean-up methodologies (*Reverse Dependency Order*).<br>• Executed teardown steps: Disable CloudFront, Empty & Delete S3 Buckets, Terminate EC2, Delete ECR Repo, Delete CloudWatch/SNS & IAM Roles.<br>• Compiled Clean-up Checklist Table. | • Standardized Clean-up process.<br>• Verified Clean-up Checklist Table. |
| **Friday (Jul 24, 2026)** | • Summarized production verification results with Mentor.<br>• Prepared technical document assets for final report writing week.<br>• Attended Week 8 progress review with Mentor. | • Completed infrastructure testing & Clean-up.<br>• Passed Week 8 review. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Operations & Monitoring
* **Amazon CloudWatch Logs & Alarms:** Centralized Docker container logging and automated metric threshold alerting.
* **Amazon SNS:** Email notification publisher/subscriber pattern for infrastructure incident alerts.
* **AWS Resource Lifecycle Management:** Resource teardown best practices avoiding unexpected AWS cloud charges.

---

### 4. Deliverables
* Operational CloudWatch & SNS automated monitoring channel.
* Production LearnSphere application running on `https://www.learnsphere.id.vn/`.
* AWS Resource Clean-up procedures and acceptance checklist.
