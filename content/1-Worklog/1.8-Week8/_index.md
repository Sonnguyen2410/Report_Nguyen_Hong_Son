---
title: "Worklog Week 8"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

## Topic: Database/AI Integration, CloudWatch/SNS Monitoring, Production Testing & Clean-up

### 1. Week 8 Objectives (Jul 20, 2026 – Jul 24, 2026)
* Securely integrate **MongoDB Atlas** by enforcing an IP Access List matching the NAT Gateways.
* Configure **Amazon CloudWatch** to aggregate logs and create **Alarms** for resource monitoring (CPU, Health Checks).
* Provision an **Amazon SNS Topic** to dispatch automated email alerts during system degradation.
* Perform comprehensive End-to-End (E2E) testing of all features in the live Production environment.
* Study and practice the exact Resource Clean-up sequence to avoid unwanted AWS billing charges.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jul 20, 2026)** | • Retrieved the 2 Elastic IPs attached to the NAT Gateways.<br>• Accessed MongoDB Atlas and whitelisted these IPs in the Network Access configuration.<br>• Applied CORS rules to the S3 Media Bucket to allow frontend presigned uploads. | • MongoDB secured from outside access.<br>• Media uploads working flawlessly. |
| **Tuesday (Jul 21, 2026)** | • Accessed AWS Console $\rightarrow$ **Amazon SNS** $\rightarrow$ Created new Topic `LearnSphere-Alerts`.<br>• Created an Email Subscription for the admin and confirmed the subscription.<br>• Configured the Docker daemon to ship logs centrally to **CloudWatch Logs** (`/learnsphere/backend`). | • SNS Topic ready to broadcast.<br>• Centralized logging for easier debugging. |
| **Wednesday (Jul 22, 2026)** | • Provisioned 2 **CloudWatch Alarms** to monitor EC2 metrics (TargetTrackingScaling or manual CPU thresholds).<br>• Set up an Alarm that triggers SNS alerts if the Target Group registers Unhealthy hosts. | • Proactive monitoring architecture.<br>• Verified Email alert delivery. |
| **Thursday (Jul 23, 2026)** | • Accessed the live domain `https://www.learnspherev2.id.vn/` for comprehensive E2E testing.<br>• Tested core flows: Registration/Login, Course Creation, Video/PDF Uploads, Quizzes, and AI Tutor interactions. | • Application running perfectly.<br>• 100% E2E Testing Pass Rate. |
| **Friday (Jul 24, 2026)** | • Drafted a Clean-up Checklist Table documenting the exact deletion sequence.<br>• Executed teardown steps: Disable CloudFront, Empty/Delete S3 Buckets, Delete ALB, ASG, NAT Gateways, and VPC.<br>• Held Week 8 technical progress review with Mentor. | • Mastered resource teardown process.<br>• Prevented phantom billing charges. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Infrastructure & Operations
* **Amazon CloudWatch Logs & Alarms:**
  * Centralized log aggregation providing real-time visibility into application health.
  * Triggering automated Alarms based on critical infrastructure metrics (`CPUUtilization`, `UnHealthyHostCount`).
* **Amazon SNS (Simple Notification Service):**
  * Flexible Pub/Sub messaging service utilized to dispatch emergency notifications.
* **Resource Clean-up Best Practices:**
  * Understanding cross-service dependencies (VPC, NAT, ALB, ASG, Security Groups).
  * Enforcing Reverse Dependency Order teardown to successfully terminate interconnected resources.

---

### 4. Deliverables
* Successful and secure integration of MongoDB Atlas and S3 CORS.
* Stable CloudWatch & SNS monitoring and alerting channels.
* LearnSphere platform passed rigorous E2E Production testing.
* Accurate Clean-up Checklist ensuring cost-effective AWS resource management.
