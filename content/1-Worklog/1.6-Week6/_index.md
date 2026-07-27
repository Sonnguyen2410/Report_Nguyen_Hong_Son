---
title: "Week 6 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---

## Topic: Developing 24/7 AI Tutor Learning Assistant and Automated Quiz Generation Tool

### 1. Week 6 Objectives
* Build a personalized 24/7 AI Tutor Chatbot feature allowing students to ask questions and receive accurate answers based on the context of lesson documents extracted in Week 5.
* Develop an AI-powered Automated Quiz Generator supporting diverse question types (Multiple Choice, True/False, Fill in the Blank, Essay) complete with correct answers and detailed explanations.
* Construct a Question Builder interface on React Frontend enabling Instructors to preview, edit, or append AI-generated questions.
* Ensure standardized JSON data responses from OpenAI API and track AI conversation history in MongoDB Atlas.

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. Context-Aware AI Tutor Architecture
* **Simplified RAG Technique (Simple Retrieval-Augmented Generation):**
  * Understand AI Tutor inner workings: When a student submits a question within a lesson, the Backend retrieves the extracted text string (`ai_indexed_content`) of that lesson from MongoDB Atlas to append into the System Prompt as Context.
  * System Prompt Engineering for behavior control: Constrain AI to answer strictly within the lesson's knowledge scope, respond politely in Vietnamese/English, explain clearly, and decline unrelated queries.
* **Chat History Management:**
  * Conversation storage structure in `AIMessage.model.js` (including `user_id`, `lesson_id`, `role`, `content`, `tokens_used`, `timestamp`).
  * Context Window History technique sending the N most recent dialogue turns so the AI understands follow-up questions from students.

#### B. AI Quiz Generator Engine
* **Enforced Structured JSON Outputs:**
  * Utilize OpenAI API's `response_format: { type: "json_object" }` feature to mandate Model responses in exact expected JSON structures without extra explanatory text.
  * Configure JSON Schema structure for quizzes:
    * `title`: Quiz title.
    * `questions`: Array containing questions.
    * `question_type`: Question type (`multiple_choice`, `true_false`, `fill_in_blank`, `essay`).
    * `question_text`: Question statement.
    * `options`: Choices (for multiple choice questions).
    * `correct_answer`: Correct answer key.
    * `explanation`: Detailed explanation of why the answer is correct.
* **KaTeX Rendering for Math/Science Formulas:**
  * Explore `katex` and `@types/katex` libraries on React Frontend.
  * Prompt instructions forcing OpenAI to return mathematical formulas in standard LaTeX format (e.g., `\( E = mc^2 \)` or `\[ \int_0^\infty x^2 dx \]`) for elegant rendering on Frontend.

---

### 3. Implementation Tasks (Work Tasks)

* **Develop AI Tutor Assistant Module (`ai-assistant.service.js`):**
  * Write API `POST /api/ai/chat`:
    * **STEP 1:** Receive `lesson_id` and query `message` from student.
    * **STEP 2:** Query MongoDB for lesson `ai_indexed_content` and 5 most recent messages from `AIMessage` collection.
    * **STEP 3:** Build System Prompt incorporating lesson context and dispatch request to OpenAI API (`gpt-4o-mini`).
    * **STEP 4:** Save User message and AI response to MongoDB, return result to Frontend.
  * Build `AIAssistantPage.tsx` / Chat Drawer UI on Frontend with typing effect, KaTeX formula rendering, and real-time chat window.

* **Develop Automated Quiz Generation Engine (`quiz-generator.service.js`):**
  * Write API `POST /api/quizzes/generate-ai`:
    * Receive parameters from Instructor: `lesson_id`, `num_questions`, `difficulty` (Easy/Medium/Hard), `question_types`.
    * Feed lesson text and parameters into custom-designed System Prompt for OpenAI API.
    * Receive JSON string from OpenAI, perform JSON Data Validation, and save Quiz into `Quiz.model.js` as Draft.

* **Develop Question Builder Interface (`QuestionBuilderPage.tsx`):**
  * Build quiz design screen for Instructors:
    * "Generate Quiz with AI" button triggering modal to select question count and difficulty.
    * Question list rendered as interactive Cards allowing Instructors to directly edit question text, add/remove options, alter correct answers, or manually append questions.
    * "Publish Quiz" button to officially release quiz for student taking.

---

### 4. Deliverables
* AI Tutor Assistant feature running stably, providing accurate context-aware responses based on lesson materials, rendered smoothly on Frontend Chat UI.
* Quiz Generation Engine powered by OpenAI API generating high-quality quizzes in valid JSON structures, supporting KaTeX LaTeX math formula rendering.
* Completed `QuestionBuilderPage.tsx` helping Instructors save 80% of homework composition time by combining AI capabilities with human customization.
