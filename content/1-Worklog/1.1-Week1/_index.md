---
title: "Week 1 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---
## Topic: Getting Started with AWS VPC, IAM, and Designing Database Schema for LearnSphere

### 1. Week 1 Objectives
* Master foundational knowledge of AWS VPC cloud networking, AWS IAM security mechanisms, and connecting to MongoDB Atlas.
* Complete data structure design (Database Schema) consisting of 11 Mongoose Models for the LearnSphere system.
* Standardize API design documentation (`API_DESIGN.md`) and initialize project directory structure (Monorepo setup).

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. AWS Cloud Fundamentals
* **AWS VPC (Virtual Private Cloud):**
  * Concept of isolated virtual network on AWS Cloud and subnet division.
  * Distinguish between **Public Subnet** (allows Internet access via Internet Gateway) and **Private Subnet** (for Database/Internal Services).
  * Learn how to configure **Internet Gateway (IGW)** and **Route Table** for traffic routing.
* **Security Groups (Firewall):**
  * Learn how to create and configure access rules (Inbound/Outbound Rules) for ports: 22 (SSH), 80/443 (HTTP/HTTPS), 5000 (Backend API).
* **AWS IAM (Identity and Access Management):**
  * Concepts of IAM User, IAM Group, IAM Role, and IAM Policy.
  * Principle of Least Privilege (**Principle of Least Privilege**).
  * Learn how to securely create Access Key / Secret Access Key for applications communicating with AWS services (such as S3).

#### B. Database & API Design
* **MongoDB Atlas & Mongoose ODMs:**
  * Learn NoSQL database model, how to initialize a free M0 Cluster on MongoDB Atlas.
  * Learn how to configure Network Access (IP Whitelist) for secure connection from Node.js server to Atlas.
  * How to define Schema, Indexes, and Relationships between collections in Mongoose.
* **RESTful API Standards:**
  * Rules for naming Endpoints, HTTP Verbs (GET, POST, PUT, PATCH, DELETE).
  * Standardize JSON Response structure (Success, Error Handling, HTTP Status Codes).

---

### 3. Implementation Tasks (Work Tasks)

* **Project Environment Initialization & Configuration:**
  * Create root directory `LearnSphere` containing 2 independent applications: `LearnSphere_BE` (Backend Node.js/Express) and `LearnSphere_FE` (Frontend React/Vite/Tailwind).
  * Initialize template `.env` file to manage environment variables (`PORT`, `MONGODB_URI`, `JWT_SECRET`, `AWS_REGION`, etc.).

* **Database Schema Development (11 Models):**
  Define Schemas using Mongoose:
  * `User.model.js`: Account management, role authorization (student, instructor, admin), bcrypt password encryption.
  * `Course.model.js` & `Lesson.model.js`: Course information management, lesson roadmap, video URLs & attached documents.
  * `Enrollment.model.js` & `LessonProgress.model.js`: Track student enrollment progress and completion percentage.
  * `Quiz.model.js` & `QuizAttempt.model.js`: Configure question bank (Multiple Choice, True/False, Fill in the Blank, Essay) and store quiz results.
  * `AIMessage.model.js`: Store conversation history between students and the AI Tutor Assistant.
  * `CourseDiscussion.model.js` & `Notification.model.js`: Lesson discussion features and notification system.
  * `RequestMetric.model.js`: Store HTTP request metrics for the Admin Monitoring page.

* **Write `API_DESIGN.md` Document:**
  * Detail and package the list of all API Endpoints for functional groups: Auth, Course, Lesson, Quiz, AI, Progress, Metrics.

---

### 4. Deliverables
* Master theoretical knowledge of VPC structure, Subnets, Security Groups, and IAM on AWS.
* Completed 11 Mongoose Model files in `LearnSphere_BE/src/models/` directory.
* Successfully configured connection to MongoDB Atlas Cluster.
* Completed `API_DESIGN.md` documentation serving subsequent development weeks.
