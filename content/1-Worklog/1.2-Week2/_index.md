---
title: "Worklog Week 2"
date: 2026-07-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

## Topic: Backend RESTful API Development & JWT Authentication for LearnSphere

### 1. Week 2 Objectives (Jun 08, 2026 – Jun 12, 2026)
* Build modular Node.js Express Backend server architecture.
* Implement JWT Authentication and Role-Based Access Control (RBAC).
* Develop RESTful APIs for Auth, Course, Lesson, and Progress management.
* Write automated Unit Tests using Jest and Supertest.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jun 08, 2026)** | • Configured `LearnSphere_BE/src/` modular structure (controllers, routes, middlewares, services).<br>• Installed core dependencies: `express`, `mongoose`, `jsonwebtoken`, `bcryptjs`, `cors`, `dotenv`, `helmet`.<br>• Built centralized global error handler middleware (`error.middleware.js`). | • Completed Backend project scaffolding.<br>• Global error handler middleware. |
| **Tuesday (Jun 09, 2026)** | • Built Auth Controller & Routes (`/api/auth`): Register, Login, Get Current User (`/me`), Refresh Token.<br>• Integrated `bcryptjs` 10 salt rounds password hashing and short-lived JWT Access Token generation. | • Operational Authentication APIs.<br>• Secure password hashing mechanism. |
| **Wednesday (Jun 10, 2026)** | • Implemented RBAC access control middleware (`auth.middleware.js`): `verifyToken`, `isInstructor`, `isAdmin`.<br>• Developed Course Management APIs (`/api/courses`): Create, Read, Update, and Delete courses. | • Robust RBAC authorization.<br>• Finalized Course CRUD APIs. |
| **Thursday (Jun 11, 2026)** | • Developed Lesson Management APIs (`/api/lessons`): Add lessons, reorder lesson display positions.<br>• Built Lesson Progress APIs (`/api/progress`) and Enrollment APIs (`/api/enrollments`). | • Lesson & Progress APIs.<br>• Percentage learning progress tracking. |
| **Friday (Jun 12, 2026)** | • Created automated integration test suite using Jest & Supertest for Auth and Course APIs.<br>• Packaged Postman Collection (`LearnSphere_BE_APIs.json`) for manual verification.<br>• Attended Week 2 progress review with Mentor. | • 100% passing Jest test suite.<br>• Ready-to-use Postman Collection. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Security & Credential Management
* **AWS IAM Roles & Security Best Practices:**
  * Static Long-term Access Keys vs. Dynamic Short-term IAM Role Credentials.
  * Environmental variable management using `.env` files.

#### B. Node.js / Express Backend Development
* **JWT & RBAC Authorization:**
  * Token signing, payload validation, digital signature verification, and role-based permissions.
* **Automated Testing:**
  * **Jest** & **Supertest** integration testing for Express endpoints.

---

### 4. Deliverables
* Complete Node.js Express backend source code for Auth, Course, Lesson, Progress modules.
* Secure JWT Authentication and RBAC middleware.
* Postman Collection for API testing.
* Automated Jest test suite achieving >85% Code Coverage.
