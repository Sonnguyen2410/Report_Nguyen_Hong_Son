---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# [AWS Architecture] Serverless Caching Solution with Amazon ElastiCache: Cost Optimization & Response Acceleration for AI Platforms


Hello everyone in the AWS Study Group,

When building an AI-integrated application (like Groq API or OpenAI), the biggest challenge for technical teams when scaling is not the EC2 infrastructure, but rather the cost of calling external APIs and the latency when waiting for the Model to generate an answer.

A request to the Groq API can take anywhere from 800ms to 3000ms depending on the length of the prompt and generated tokens. If an application has thousands of daily visits with many repetitive questions (like multiple-choice questions, term explanations, study guides), the system will burn through a lot of budget and significantly degrade user experience.

To solve this problem, our team researched an architectural optimization approach by introducing a Serverless Caching layer into the system.

## 1. Current Data Flow Analysis & Budget Burn Points

In the current architecture diagram of the project:
* **Traffic Flow:** User -> CloudFront -> S3 (Frontend) & EC2 Instance (Backend).
* **AI Execution:** EC2 Backend handles the logic and directly calls the Groq API.

**Problems encountered:**
* **Redundant Calls:** If 500 students simultaneously open an assignment and ask the AI to explain a concept, the EC2 will send 500 separate HTTP requests to the Groq API for the exact same content.
* **Latency Bottleneck:** Users always have to wait a few seconds for each interaction, even though the backend system on AWS is very fast.
* **Rate Limit Risks:** AI API providers always impose TPM (Tokens Per Minute) and RPM (Requests Per Minute) limits. Continuous calling easily leads the application to hit 429 Too Many Requests errors.

## 2. Architectural Solution: Integrating Amazon ElastiCache for Redis (Serverless)

The optimal architectural solution is to add Amazon ElastiCache for Redis acting as an In-Memory Cache in front of the AI Service layer.

**Why choose a Serverless configuration?**
* **No Cluster Management:** Automatically scales RAM capacity and throughput according to actual traffic.
* **Cost Optimization:** Only pay for storage (GB-hour) and data access (ECU/read-write unit), suitable for projects growing from small to large scale.
* **Low Latency:** Response times are in the microsecond/millisecond range since the data resides entirely in RAM.

## 3. Data Processing Mechanism (Cache-Aside Strategy)

The application implements the Cache-Aside (Lazy Loading) strategy with a step-by-step detailed flow:

**Technical Processing Flow:**
1. **Standardization & Key Generation:**
   When the EC2 Backend receives a request from a user, the system standardizes the text string (lowercases, removes extra spaces) and generates a unique Hash Key for that prompt.
2. **Cache Status Check (Cache Hit / Miss):**
   * **Cache Hit (Found in Cache):** If a corresponding result is found with the Identifier Key in ElastiCache, the EC2 Backend directly fetches the data from RAM and returns it to the user immediately. Processing time < 10ms.
   * **Cache Miss (Not in Cache):** If not found, the EC2 Backend initiates a connection to call the Groq API for processing.
3. **Storage & Time To Live (TTL):**
   After receiving the answer from the Groq API, the EC2 Backend simultaneously saves this result into ElastiCache with a Time To Live (TTL - E.g.: 24 hours) before responding to the end-user.

## 4. Resulting Benefits Assessment

* **Latency:** Reduces response time for common questions from ~1.5s - 2.5s down to ~5ms - 15ms (nearly 100x speedup).
* **Cost Efficiency:** Saves up to 40% - 60% of token usage when calling the Groq API during peak study periods.
* **High Availability:** If the Groq API service is unstable or hits the Rate Limit, the system can still adequately serve previously cached content without affecting the student experience.

## 5. Next Upgrade Strategy: Semantic Caching

While Exact-match Caching only solves identical questions, the team's next upgrade step is to research Semantic Caching:
* Use Vector Embeddings to measure the similarity between a new question and questions already saved in the cache.
* If two questions have high semantic similarity (e.g., "What is AWS Lambda?" and "Explain the concept of AWS Lambda"), the system will return the same result without needing to call the AI Model again.

![Blog 3](/images/blog3.jpg)

---

**Original Facebook Post:** [AWS Study Group Post](https://www.facebook.com/groups/660548818043427)