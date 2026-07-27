---
title: "Create & Configure Amazon S3 Buckets"
date: 2026-07-27
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

The LearnSphere system utilizes two distinct S3 Buckets to enforce security isolation between user-generated media content and compiled Frontend static source code.

---

### 3.1. Create S3 Bucket 1 — Media Storage (`learnsphere-media-575620421319`)

This bucket handles storage for all system media assets:
- Lecture videos.
- Learning documents (PDF, DOCX).
- Course cover thumbnails.
- User profile avatars.

#### Configuration Steps:

1. Navigate to **Amazon S3** service -> click **Create bucket**.
2. **Bucket name:** `learnsphere-media-575620421319` (globally unique).
3. **Region:** `ap-southeast-1` (Singapore).
4. **Block Public Access:** Keep **Block all public access = ON**.
5. **Static Website Hosting:** Keep `Disabled`.
6. Click **Create bucket**.

![Creating S3 Media Bucket learnsphere-media](/images/5-Workshop/5.4/5.4.3.1.1.png)
<p align="center"><i>Figure 5.4.3.1a — Creating Private S3 Media Bucket learnsphere-media.</i></p>

#### CORS Configuration:

Because the Frontend React app running on CloudFront uploads media directly to S3 via Presigned URLs, we must configure CORS rules on the S3 Media Bucket:

1. Open Bucket `learnsphere-media-575620421319` -> navigate to **Permissions** tab.
2. Scroll to **Cross-origin resource sharing (CORS)** -> click **Edit** and paste:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT"],
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://d2onzy56n3iw1w.cloudfront.net"
    ],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 3600
  }
]
```

> **Important Note:** Exposing the `ETag` header (`ExposeHeaders: ["ETag"]`) is mandatory for the browser to read upload checksums during S3 Multipart Uploads of large video files.

![CORS configuration enabling browser uploads/downloads via presigned URLs](/images/5-Workshop/5.4/5.4.3.1.2.png)
<p align="center"><i>Figure 5.4.3.1b — CORS configuration enabling browser uploads/downloads via Presigned URLs.</i></p>

---

### 3.2. Create S3 Bucket 2 — Frontend Static Assets (`learnsphere-fe-575620421319`)

This bucket stores compiled React SPA static assets (`index.html`, JavaScript, CSS, images).

1. Click **Create bucket**.
2. **Bucket name:** `learnsphere-fe-575620421319`.
3. **Region:** `ap-southeast-1` (Singapore).
4. **Block Public Access:** Keep **Block all public access = ON**.
5. **Static Website Hosting:** Keep `Disabled`.
6. Click **Create bucket**.

![Private S3 bucket for storing compiled Frontend build](/images/5-Workshop/5.4/5.4.3.2.png)
<p align="center"><i>Figure 5.4.3.2 — Private S3 Bucket for storing compiled Frontend static build assets.</i></p>

> **Note:** This bucket remains 100% Private. Read permissions will be granted exclusively to CloudFront Origin Access Control (OAC) in **Step 7A**.
