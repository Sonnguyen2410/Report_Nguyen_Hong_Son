---
title: "Worklog Week 4"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

## Topic: Frontend React Application, State Management & API Integration

### 1. Week 4 Objectives
* Scaffold `LearnSphere_FE` frontend application using Vite, React.js, TypeScript, and TailwindCSS.
* Configure SPA client routing with React Router v6 and Axios Interceptors for JWT authorization.
* Develop Tutor interfaces (Course management, media upload) and Student interfaces (Learning viewer, Quiz runner, AI chat).
* Connect Frontend components with Backend APIs into a seamless Single Page Application.

---

### 2. Daily Activity Log

| Day | Detailed Tasks Executed | Key Deliverables / Outcomes |
|---|---|---|
| **Monday (Jul 06, 2026)** | • Scaffolded `LearnSphere_FE` project using `vite` TypeScript template.<br>• Installed libraries: `react-router-dom`, `axios`, `@tanstack/react-query`, `lucide-react`, `tailwindcss`.<br>• Configured Axios Client (`api.js`) attaching `Authorization: Bearer <token>` and automatic 401 handling. | • React Vite TypeScript codebase.<br>• Axios JWT Interceptors. |
| **Tuesday (Jul 07, 2026)** | • Built shared UI Components: `Navbar`, `Sidebar`, `Modal`, `Button`, `LoadingSpinner`, `ToastNotification`.<br>• Developed Login & Registration pages (`LoginPage.tsx` & `RegisterPage.tsx`) with strong form validation. | • Standardized UI Component library.<br>• Finalized Auth Login/Register pages. |
| **Wednesday (Jul 08, 2026)** | • Developed Tutor Dashboard (`TutorDashboard.tsx`): Course listing, Create Course modal.<br>• Integrated direct browser-to-S3 upload: Frontend fetched Presigned PUT URLs and executed direct S3 PUTs with progress bars. | • Tutor Course Management UI.<br>• Direct S3 Presigned Upload component. |
| **Thursday (Jul 09, 2026)** | • Built Student Learning Viewer (`LessonViewer.tsx`): HTML5 video player streaming via Presigned GET URLs from S3.<br>• Developed Quiz Runner (`QuizRunner.tsx`) with countdown timer and AI Assistant chat drawer (`AITutorChat.tsx`). | • Lesson Viewer & Video Stream UI.<br>• Quiz Runner & AI Chat Drawer. |
| **Friday (Jul 10, 2026)** | • Integrated React Query managing course data caching and background refetching.<br>• Tested End-to-End user workflows locally.<br>• Attended Week 4 Frontend Demo review with Mentor. | • Responsive SPA Frontend application.<br>• Passed Week 4 review. |

---

### 3. Core Tech & Learning Topics

#### A. AWS Cloud Concepts
* **S3 CORS (Cross-Origin Resource Sharing):**
  * Configuring S3 CORS XML/JSON Rules enabling browser HTTP methods (`GET`, `PUT`, `HEAD`) and headers (`*`, `ETag`).

#### B. React & TypeScript Development
* **Vite & React Query:**
  * High-performance HMR and asynchronous server state management using TanStack Query.
* **Direct Browser Uploads:**
  * Utilizing Axios `onUploadProgress` callbacks for real-time S3 upload progress tracking.

---

### 4. Deliverables
* Complete React Single Page Application in `LearnSphere_FE`.
* Direct-to-S3 Presigned PUT video/PDF upload component with progress bar.
* Secure Presigned GET video player component.
* Interactive Quiz Runner and AI Assistant Chat drawer.
