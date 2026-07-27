---
title: "Week 5 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

## Topic: OpenAI API Integration, Building PDF/Word Document Extraction Module, and Vietnamese OCR

### 1. Week 5 Objectives
* Successfully integrate OpenAI API (using official SDK) into Node.js Backend to power Artificial Intelligence features.
* Build a multi-format document processing pipeline: Read and extract plain text from PDF files, Word files (`.docx`), and scanned image documents using Vietnamese-supported Optical Character Recognition (OCR) technology.
* Establish an AI Document Indexing system storing lesson document indexes into MongoDB Atlas database to serve as the foundational context for the AI Tutor in Week 6.
* Ensure security and optimize OpenAI API call costs (Rate Limiting, Timeout Handling & Prompt Structuring).

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. OpenAI API Integration & Prompt Engineering Techniques
* **Overview of OpenAI API SDK:**
  * Learn how to initialize OpenAI Client in Node.js using the `openai` library.
  * Choose optimal Model for E-Learning use case: Use `gpt-4o` / `gpt-4o-mini` for fast processing speed, high Vietnamese language comprehension, and cost effectiveness.
  * Understand Model configuration parameters: `temperature` (creativity level), `max_tokens` (response length limit), `top_p`, and `response_format` (enforcing strict `json_object` format).
* **Security & API Cost Management:**
  * Ensure OpenAI API Key is securely stored in `OPENAI_API_KEY` environment variable on EC2 server and never exposed to the React client.
  * Build **Timeout Handling Mechanism:** Configure max wait time (120-second Timeout) preventing network delays or large files from hanging requests.
  * Set up Text Chunking/Truncation mechanism to avoid exceeding the Model's Context Window limits and minimize Token costs.

#### B. Text Extraction Technologies & OCR
* **Static Text File Extraction (PDF & Word):**
  * Use `pdf-parse` library: Read binary data stream (Buffer) of PDF files and extract full text string.
  * Use `mammoth` library: Read Word (`.docx`) files, convert Word XML structure into clean plain text without junk characters.
* **Optical Character Recognition (OCR) with Tesseract.js (Vietnamese):**
  * OCR Concept: Technique of recognizing handwriting or printed text within digital images into editable text data.
  * Use `tesseract.js` library: Configure recognition with Vietnamese (`vie`) and English (`eng`) language datasets.
  * Multi-thread processing with Tesseract Worker to prevent Event Loop blocking on Node.js server when processing large image files.

---

### 3. Implementation Tasks (Work Tasks)

* **Install Packages & Configure Backend Environment (`LearnSphere_BE`):**
  * Install file processing and AI dependencies:
    ```bash
    npm install openai pdf-parse mammoth tesseract.js @tesseract.js-data/vie
    ```
  * Configure environment variables `OPENAI_API_KEY` and `AI_PROVIDER_TIMEOUT_MS=120000` in `.env`.

* **Build Document Data Extraction Pipeline (`file-parser.service.js`):**
  * Develop file format detection module based on MIME Type or file extension:
    * `application/pdf` format: Call `pdf-parse` module to retrieve `data.text`.
    * `application/vnd.openxmlformats-officedocument.wordprocessingml.document` (`.docx`): Call `mammoth.extractRawText({ buffer })`.
    * Image formats (`image/png`, `image/jpeg`): Create Tesseract Worker with `vie+eng` languages, execute `worker.recognize(buffer)` to extract text from scanned images.
  * Build Text Sanitization function: Remove extra whitespaces, unreadable special characters, and normalize Vietnamese Unicode encoding.

* **Build AI Provider Service (`ai-provider.service.js`) & Indexing API:**
  * Write encapsulated OpenAI API calling module:
    ```javascript
    import OpenAI from "openai";

    const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

    export const invokeOpenAI = async ({ systemPrompt, userPrompt, jsonMode = false }) => {
      const response = await openai.chat.completions.create({
        model: "gpt-4o-mini",
        messages: [
          { role: "system", content: systemPrompt },
          { role: "user", content: userPrompt }
        ],
        response_format: jsonMode ? { type: "json_object" } : undefined,
        temperature: 0.3,
      });
      return response.choices[0].message.content;
    };
    ```
  * Write API `POST /api/lessons/:id/index-ai`: Instructors upload lecture files (PDF/Word/Image), system automatically runs text extraction pipeline and saves text string to `ai_indexed_content` field in `Lesson.model.js` on MongoDB Atlas.

* **Update Backend Dockerfile for Tesseract.js:**
  * Update Backend `Dockerfile` to install required Linux system dependencies for Tesseract OCR (`pango`, `cairo`, `libpng`) ensuring smooth Docker build execution on Amazon ECR without errors.

---

### 4. Deliverables
* `file-parser.service.js` module reads and accurately extracts 100% text content from lecture PDFs, Word `.docx` files, and scanned Vietnamese document images.
* `ai-provider.service.js` module successfully connects to OpenAI API with `gpt-4o-mini` model, handling rapid responses and safe timeout errors.
* Successfully unlocked AI Indexing feature: Instructors simply upload lesson documents, system automatically parses and stores text data into MongoDB Atlas as input context for AI Tutor Assistant and Automated Quiz Generator in Week 6.
