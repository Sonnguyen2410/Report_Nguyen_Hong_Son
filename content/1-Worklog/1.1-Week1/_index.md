---
title: "Worklog Week 1"
date: 2026-06-19
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

## Topic: Onboarding, AWS VPC/IAM Fundamentals & Database Schema Design for LearnSphere

### 1. Week 1 Objectives
* Attend FCAJ Program Onboarding session and receive AWS Sandbox practice environment resources.
* Master foundational cloud networking concepts on AWS VPC, IAM security, and MongoDB Atlas connectivity.
* Complete database schema design including 11 Mongoose Models for the LearnSphere platform.
* Standardize technical API specification (`API_DESIGN.md`) and initialize project Monorepo structure.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jun 15, 2026)** | • Joined internship kickoff Onboarding session with FCAJ Admins and Mentors.<br>• Received AWS Sandbox credentials and configured AWS CLI v2 locally.<br>• Discussed LearnSphere E-Learning project requirements and objectives with Mentor. | • Successfully set up AWS environment.<br>• Aligned on LearnSphere project roadmap. |
| **Tuesday (Jun 16, 2026)** | • Researched AWS Virtual Private Cloud (VPC) networking fundamentals.<br>• Analyzed architectural differences between Public and Private Subnets.<br>• Studied Internet Gateway (IGW) and Route Table routing mechanisms.<br>• Configured Security Group ingress rules for ports 22, 80, 443, and 5000. | • Understood VPC network architecture.<br>• Created Security Group port specification table. |
| **Wednesday (Jun 17, 2026)** | • Studied AWS IAM (Users, Groups, Roles, Policies) and Principle of Least Privilege.<br>• Provisioned free MongoDB Atlas M0 Cluster and configured IP Access List for local connections. | • Grasped IAM access control concepts.<br>• Verified MongoDB Atlas connectivity. |
| **Thursday (Jun 18, 2026)** | • Initialized `LearnSphere` Monorepo containing `LearnSphere_BE` and `LearnSphere_FE`.<br>• Designed 11 Mongoose Models (User, Course, Lesson, Enrollment, LessonProgress, Quiz, QuizAttempt, AIMessage, CourseDiscussion, Notification, RequestMetric). | • Configured Monorepo workspace.<br>• Implemented 11 Mongoose schema models. |
| **Friday (Jun 19, 2026)** | • Authored comprehensive API design specification (`API_DESIGN.md`).<br>• Tested MongoDB Atlas SRV connection string integration in Node.js Express.<br>• Attended Week 1 progress review meeting with Mentor. | • Completed `API_DESIGN.md` document.<br>• Successfully passed Week 1 review. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Cloud Fundamentals
* **AWS VPC (Virtual Private Cloud):**
  * Isolated virtual network concepts and subnet allocation strategies.
  * Public Subnets (Internet access via IGW) vs. Private Subnets (Database/Internal Services).
  * Route Table configuration and Internet Gateway attachment.
* **Security Groups:**
  * Setting up Stateful firewall rules for ports 22 (SSH), 80/443 (HTTP/HTTPS), 5000 (Backend API).
* **AWS IAM:**
  * IAM Roles, Policies, and Principle of Least Privilege enforcement.

#### B. Database & API Architecture
* **MongoDB Atlas & Mongoose ODMs:**
  * NoSQL schema design, Index optimization, and Mongoose collection relationships.
* **RESTful API Standards:**
  * HTTP Verbs, Endpoint naming conventions, JSON error responses, and Status Codes.

---

### 4. Deliverables
* Clear understanding of AWS VPC, Subnets, Security Groups, and IAM roles.
* Completed 11 Mongoose Schema Models in `LearnSphere_BE/src/models/`.
* Verified MongoDB Atlas Cluster connection.
* Finalized `API_DESIGN.md` specification document.
