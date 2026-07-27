---
title: "Week 7 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---

## Topic: Building Online Quiz Interface, Automated Grading, and Lesson Progress Tracking

### 1. Week 7 Objectives
* Build a comprehensive online Quiz Runner UI for students featuring a real-time countdown timer, temporary answer state persistence, and auto-submission upon timeout.
* Develop a Backend Auto-Grading Engine accurately processing diverse question formats (Multiple Choice, True/False, Fill in the Blank, AI-assisted Essay grading).
* Establish a Learning Progress Tracking system recording lesson completion percentages, storing attempt histories (`QuizAttempt`), and updating course completion status.
* Complete student management pages: Progress overview dashboard (`DashboardPage.tsx`) and detailed quiz result review pages.

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. Quiz State Management & Time Synchronization on React Frontend
* **Complex Quiz State Management Techniques:**
  * Manage student answer map `userAnswers: Record<string, any>` to ensure seamless navigation between questions without losing selected data.
  * Persist temporary State to `sessionStorage` or `localStorage` to safeguard against accidental page refreshes (F5).
* **Time Synchronization & Auto-Submission (Quiz Timer):**
  * Utilize React `useEffect` combined with `setInterval` to construct real-time countdown timers (e.g., 15 minutes, 45 minutes).
  * Auto-submission event handling: When `timeLeft === 0`, disable all input controls, display "Time Up" notification, and trigger server submission function.

#### B. Grading Algorithms & Learning Progress Tracking
* **Multi-Format Auto-Grading Engine (Auto-Grading Logic):**
  * **Multiple Choice (`multiple_choice`) & True/False (`true_false`):** Perform exact string/ID comparison between student choices and `correct_answer`.
  * **Fill in the Blank (`fill_in_blank`):** Strip whitespace and convert to lowercase (`trim().toLowerCase()`) for string matching.
  * **Essay (`essay`):** Integrate OpenAI API for automated grading by evaluating student answers against key concepts/sample answers, returning scores (0–10) with detailed feedback.
* **Learning Progress Data Structure (MongoDB Models):**
  * `QuizAttempt.model.js`: Store `user_id`, `quiz_id`, `score`, `passed` (Pass/Fail based on passing score), `answers` (detailed response breakdown), and `duration_seconds` (completion time).
  * `LessonProgress.model.js`: Mark lessons as `completed: true` upon video completion or passing required quizzes.
  * Update overall course completion percentage in `Enrollment.model.js` (`progress_percentage = (completed_lessons / total_lessons) * 100`).

---

### 3. Implementation Tasks (Work Tasks)

* **Develop Backend Grading & Result Logging API (`quiz-execution.service.js`):**
  * Write API `POST /api/quizzes/:id/submit`:
    * **STEP 1:** Receive student `answers` array and `start_time`.
    * **STEP 2:** Query quiz details from MongoDB Atlas (including `correct_answer` keys).
    * **STEP 3:** Calculate overall score: directly add points for Multiple Choice/True-False/Fill-in-blank; (If essays exist) invoke `ai-provider.service.js` for OpenAI scoring and feedback generation.
    * **STEP 4:** Create new record in `QuizAttempt.model.js`.
    * **STEP 5:** If score $\ge$ `passing_score` (e.g., 70%), automatically call `enrollment.service.js` to update lesson status to completed and recalculate overall course progress percentage.
  * Write API `GET /api/quizzes/:id/attempts` allowing students to review past quiz attempts along with detailed answers and explanations.

* **Build Interactive Quiz Taking Interface (`QuizPage.tsx`):**
  * Design professional quiz-taking interface:
    * **Header Bar:** Displays Quiz title, Submit button, and Countdown Timer Badge.
    * **Left Sidebar:** Question Grid allowing quick navigation to individual questions, color-coding answered vs unanswered items.
    * **Main Canvas:** Renders question content (supporting KaTeX math formulas), Radio button / Checkbox options, or text input fields.
    * **Quiz Result Modal (`QuizResultModal`):** Instantly displays total score, PASS / FAIL status, completion time, and per-question detailed correctness breakdown with explanations.

* **Develop Learning Progress Dashboard (`DashboardPage.tsx`):**
  * Build Student Dashboard screen:
    * List enrolled courses with visual Progress Bars (%).
    * Summary statistics: completed lessons count, passed quizzes count, and average score.
    * Recent notification feed (`Notification.model.js`) for new assignments or discussion replies.

---

### 4. Deliverables
* `QuizPage.tsx` interface operates smoothly with accurate countdown timers, auto-locking submission on timeout, and F5-proof state persistence.
* Backend Auto-Grading Engine processes 100% accurate automated grading across all question types, logging full attempt trails in `QuizAttempt`.
* Lesson progress tracking system (`LessonProgress` & `Enrollment`) updates instantly upon successful quiz submission.
* `DashboardPage.tsx` enables students to track personal learning roadmaps and review detailed past quiz performance.
