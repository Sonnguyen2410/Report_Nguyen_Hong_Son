---
title: "Week 8 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---

## Topic: Configuring System Monitoring with AWS CloudWatch Logs/Alarms and Developing Admin System Monitoring Dashboard

### 1. Week 8 Objectives
* Master configuring and managing AWS CloudWatch monitoring systems (Logs & Alarms) to track operation logs and Amazon EC2 server performance.
* Construct an Admin System Monitoring dashboard (`SystemMonitoringPage.tsx`) displaying real-time HTTP Request Metrics, memory usage, total user count, and traffic volume.
* Perform comprehensive End-to-End (E2E) Testing of the complete application workflow: registration, course creation, S3 file upload, AI quiz generation, exam taking, and auto-grading.
* Conduct Security Audits, optimize AWS operational budgets, complete project documentation, and package the official internship final report.

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. Cloud Infrastructure Monitoring with AWS CloudWatch
* **AWS CloudWatch Logs:**
  * Learn how to ship application logs from Docker Containers on EC2 server to CloudWatch Log Groups.
  * Use `@aws-sdk/client-cloudwatch-logs` library or Docker Log Driver (`awslogs`) for automated log synchronization.
  * Search and filter logs using Log Insights to rapidly detect system errors (e.g., 500 status codes, DB connection failures, OpenAI API timeouts).
* **AWS CloudWatch Metrics & Alarms (Incident Alerting):**
  * Monitor EC2 infrastructure metrics: CPU Utilization, Network In/Out, Disk Read/Write.
  * Set up CloudWatch Alarms: Configure alert threshold when EC2 CPU exceeds 85% continuously for 5 minutes.
  * Integrate Amazon SNS (Simple Notification Service) to automatically dispatch instant email incident alerts to Administrators.

#### B. Security Audit & Cost Optimization
* **Infrastructure Security Audit:**
  * Review AWS Security Groups: Ensure only necessary ports are exposed (22, 80, 443, 5000) and lock down public DB management ports.
  * Ensure S3 Bucket Privacy: Audit Block Public Access configuration on S3 and verify 100% of sensitive files utilize short-lived Presigned URLs.
  * Audit GitHub Secrets: Confirm no API Keys (OpenAI, AWS Access Keys, MongoDB URI) are hardcoded in source code.
* **Operational Cost Optimization (AWS Cost Explorer):**
  * Analyze actual cost breakdowns on AWS Billing Dashboard.
  * Ensure EC2, S3, and CloudFront resources remain within AWS Free Tier limits or optimal budget estimates ($8.30 – $14.80/month).

---

### 3. Implementation Tasks (Work Tasks)

* **Develop Backend System Metrics Logging Module (`stats.service.js`):**
  * Write Express Middleware `metrics.middleware.js`: Automatically measure Response Time (ms), Status Codes (2xx, 4xx, 5xx), and Endpoint URLs for every HTTP Request, storing data into `RequestMetric.model.js` on MongoDB Atlas.
  * Write Admin-only APIs:
    * `GET /api/stats/overview`: Overview statistics for total Students, Instructors, Courses, Lessons, and Quizzes created.
    * `GET /api/stats/system-metrics`: Return hourly request traffic charts, average response latency, and system error rates.

* **Build Admin System Monitoring Dashboard (`SystemMonitoringPage.tsx`):**
  * Construct professional Admin Dashboard UI:
    * **Overview Metric Cards:** Total Users, Active Courses, Total Quiz Attempts, Total S3 Storage Usage.
    * **HTTP Traffic Charts (Line Chart / Bar Chart):** Display successful requests (200 OK) vs failed requests (4xx/5xx).
    * **System Logs Table:** Display recent system events and CloudWatch error logs.
  * **User Management Page (`AdminUsersPage.tsx`):** Allow Admins to view user accounts, lock/unlock users, or alter role authorizations.

* **End-to-End (E2E) Testing & Final Report Packaging:**
  * Execute full-flow E2E testing scenarios on live CloudFront/EC2 environment:
    * **SCENARIO 1:** Instructor registers account → Creates new course → Uploads video & PDF materials to AWS S3 via Presigned URL → Triggers AI document extraction → Uses Question Builder to generate quizzes via OpenAI API.
    * **SCENARIO 2:** Student logs in → Searches course → Views video lesson → Asks AI Tutor questions → Takes online Quiz → Receives auto-graded score and updates course progress to 100%.
  * Update `README.md` with comprehensive instructions for running local project and executing AWS CI/CD pipeline.
  * Package clean codebase and complete official 8-week Internship Report.

---

### 4. Deliverables
* AWS CloudWatch Logs & Alarms fully configured, automatically sending email alerts when EC2 server experiences high load.
* `SystemMonitoringPage.tsx` and `AdminUsersPage.tsx` running smoothly, enabling Admins to easily monitor system health and manage users.
* LearnSphere application achieves 100% successful E2E test execution on actual AWS infrastructure (S3, CloudFront, EC2, ECR, GitHub Actions, MongoDB Atlas, OpenAI API).
* Complete `README.md` guide, presentation slides, and official 8-week Internship Report ready for project defense and acceptance.
