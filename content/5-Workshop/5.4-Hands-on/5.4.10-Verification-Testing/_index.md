---
title: "Production Testing & Verification"
date: 2026-07-27
weight: 10
chapter: false
pre: " <b> 5.4.10. </b> "
---

After CI/CD pipeline execution, practitioners verify Backend containers, inspect centralized log streams, test database connectivity, and perform End-to-End user testing on the official Production HTTPS domain.

---

### 10.1. Inspect Backend Container Status on EC2

Connect to EC2 via Session Manager and check container status:

```bash
sudo docker ps --filter name=learnsphere-be
```

Inspect detailed container health details:

```bash
sudo docker inspect \
  --format 'status={{.State.Status}} health={{.State.Health.Status}} restarts={{.RestartCount}} image={{.Config.Image}}' \
  learnsphere-be
```

**Expected Output:**
```text
status=running
health=healthy
restarts=0
```

---

### 10.2. Verify Database Connection via Health Check Endpoint

Query the application readiness endpoint to confirm MongoDB Atlas connectivity:

```bash
curl -fsS http://127.0.0.1:5000/health/ready
```

**Expected Output:**
```json
{
  "status": "ready",
  "database": "connected"
}
```

![Backend container running healthy and connected to MongoDB](/images/5-Workshop/5.4/5.4.10.2.png)
<p align="center"><i>Figure 5.4.10.2 — Backend container operating healthily and verified connection to MongoDB Atlas.</i></p>

---

### 10.3. Inspect Centralized CloudWatch Logs

Open **Amazon CloudWatch** -> **Log groups** -> select `/learnsphere/backend`.

Confirm:
- Node.js server initialized successfully on port 5000.
- MongoDB Atlas connection is healthy.
- Zero crash/restart loops.
- Receives inbound HTTP request logs forwarded from CloudFront.

![Backend logs aggregated in Amazon CloudWatch](/images/5-Workshop/5.4/5.4.10.3.png)
<p align="center"><i>Figure 5.4.10.3 — Centralized system log streams aggregated in CloudWatch Log Groups.</i></p>

---

### 10.4. Production Application Testing & User Experience

Navigate directly to the official Production URL:

```text
https://www.learnsphere.id.vn/
(Or CloudFront HTTPS Distribution URL: https://d2onzy56n3iw1w.cloudfront.net)
```

#### Execute End-to-End Feature Verification Tests:
1. **Registration & Authentication:** Create a new user account and verify JWT Token generation.
2. **Course Management:** Log in as a Tutor -> Create a new course.
3. **Presigned URL Media Upload:** Upload lecture videos, thumbnails, and PDFs. Confirm browser fetches Presigned PUT URLs and uploads directly to S3 Media Bucket without CORS issues.
4. **Learning & Video Streaming:** Log in as a Student -> Open a lesson -> Confirm video streams seamlessly via short-lived Presigned GET URLs.
5. **Quiz Exams & AI Assistant:** Complete quiz examinations and test AI Assistant interactions.

![LearnSphere application operating on AWS Production environment](/images/5-Workshop/5.4/5.4.10.4.png)
<p align="center"><i>Figure 5.4.10.4 — LearnSphere web application operating live on AWS Production infrastructure.</i></p>
