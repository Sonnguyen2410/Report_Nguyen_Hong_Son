---
title: "Worklog Week 3"
date: 2026-07-27
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

## Topic: Quiz Module, AI Assistant (Amazon Bedrock) & S3 Media Presigned Streaming

### 1. Week 3 Objectives (Jun 15, 2026 – Jun 19, 2026)
* Develop Online Quiz Examination module (question bank, automated grading, score history).
* Integrate **Amazon Bedrock / Claude 3** model for AI Tutor Assistant interactions.
* Build secure video streaming & upload via **Amazon S3 Short-lived Presigned URLs** (PUT & GET).
* Package Seed Data script for frontend integration.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jun 15, 2026)** | • Built Quiz Controller & Attempt Routes (`/api/quizzes`): Create quiz exams, time limit rules.<br>• Implemented automated grading algorithm (`QuizAttempt.service.js`) for Multiple Choice, True/False, Fill-in-blank. | • Quiz examination & automated grading.<br>• Student score history tracking. |
| **Tuesday (Jun 16, 2026)** | • Installed `@aws-sdk/client-bedrock-runtime` and researched Anthropic Claude 3 Foundation Model.<br>• Built AI Controller (`/api/ai/chat`): Received queries, invoked Bedrock runtime, returned smart AI responses.<br>• Persisted message history into `AIMessage` Mongoose collection. | • Intelligent AI Tutor response service.<br>• Lesson-bound AI chat history logging. |
| **Wednesday (Jun 17, 2026)** | • Researched private S3 object security without public access bucket policies.<br>• Installed `@aws-sdk/client-s3` & `@aws-sdk/s3-request-presigner` dependencies.<br>• Built APIs generating short-lived Presigned PUT (5-min upload) and Presigned GET (15-min streaming) URLs. | • Operational Presigned PUT & GET APIs.<br>• 100% Private S3 Media Security. |
| **Thursday (Jun 18, 2026)** | • Developed Course Discussion APIs (`CourseDiscussion`) and Notification APIs (`Notification`).<br>• Authored `seedData.js` script populating mock accounts (Student, Tutor, Admin) and 5 sample courses. | • Discussion & Notification APIs.<br>• Automated Database Seeding Script. |
| **Friday (Jun 19, 2026)** | • Executed end-to-end API testing using Postman combined with S3 Presigned URL generation.<br>• Measured Amazon Bedrock response latency and optimized system Prompt Engineering templates.<br>• Attended Week 3 progress review with Mentor. | • 100% Backend API completion.<br>• Successfully passed Week 3 review. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Cloud Services
* **Amazon Bedrock (Generative AI on AWS):**
  * Invoking Anthropic Claude 3 Foundation Model via `@aws-sdk/client-bedrock-runtime`.
* **Amazon S3 Presigned URLs Architecture:**
  * Delegated temporary authorization for direct client-to-S3 uploads, saving EC2 compute resources.

---

### 4. Deliverables
* Online Quiz examination and grading system.
* Amazon Bedrock AI Tutor Assistant integration.
* Secure S3 Presigned URL generation APIs.
* Database seeding script (`seedData.js`).
