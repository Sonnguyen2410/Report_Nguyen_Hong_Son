---
title: "Production Testing & Verification"
date: 2026-07-27
weight: 10
chapter: false
pre: " <b> 5.4.10. </b> "
---

# 5.4.10. Production Testing & Verification

In this step, practitioners access the official LearnSphere application in the Production environment via the CloudFront HTTPS domain and execute End-to-End verification testing.

---

### 1. Access CloudFront HTTPS Domain

1. Open a web browser and navigate to your CloudFront URL: `https://d2onzy56n3iw1w.cloudfront.net`.
2. Verify the **TLS/SSL Padlock** icon indicating a secure HTTPS connection.

---

### 2. Verify Authentication Workflows (Login & Register)

1. Register a new Student account.
2. Confirm API requests to `/api/v1/auth/register` are properly reverse-proxied to EC2 Backend.
3. Log in and verify JWT Token storage in LocalStorage.

---

### 3. Verify Media Upload & Playback via Presigned URLs

1. Log into a Tutor account $\rightarrow$ Create a new course.
2. Upload a lecture video file $\rightarrow$ Verify the browser requests a Presigned PUT URL and uploads directly to the S3 Media Bucket without CORS errors.
3. Log into a Student account $\rightarrow$ Play a video lesson $\rightarrow$ Confirm the browser streams video via a short-lived Presigned GET URL.

---

### 4. Verify Client-Side Sub-route Page Refresh (SPA Routing Check)

1. Navigate directly to a sub-route: `https://d2onzy56n3iw1w.cloudfront.net/courses`.
2. Press **F5** to refresh the page.
3. **Expected Result:** CloudFront Function rewrites internal URI to `/index.html`, and React Router renders the course page without 404 Not Found errors.
