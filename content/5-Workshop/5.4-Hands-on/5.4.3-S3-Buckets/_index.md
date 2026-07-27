---
title: "Create & Configure Amazon S3 Buckets"
date: 2026-07-27
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

# 5.4.3. Create & Configure Amazon S3 (Frontend & Media Buckets)

In this step, practitioners initialize 2 distinct S3 Buckets with strict security settings: **S3 Frontend Bucket** (storing compiled React static assets) and **S3 Media Bucket** (storing video/PDF/image lecture files).

---

### 1. Initialize S3 Bucket 1 (Frontend Static Hosting)

1. Navigate to **Amazon S3** service $\rightarrow$ click **Create bucket**.
2. Bucket Name: `learnsphere-fe-575620421319` (globally unique).
3. **Region:** Select `ap-southeast-1` (Singapore).
4. **Block Public Access:** Keep default **Block all public access = ON**.
5. Click **Create bucket**.

---

### 2. Initialize S3 Bucket 2 (Media Storage)

1. Click **Create bucket**.
2. Bucket Name: `learnsphere-media-575620421319`.
3. **Region:** `ap-southeast-1` (Singapore).
4. **Block Public Access:** Keep default **Block all public access = ON**.
5. Click **Create bucket**.

---

### 3. Configure CORS on S3 Media Bucket

1. Open Bucket `learnsphere-media-575620421319` $\rightarrow$ navigate to **Permissions** tab.
2. Scroll to **Cross-origin resource sharing (CORS)** $\rightarrow$ click **Edit**.
3. Paste JSON CORS configuration allowing direct browser Multipart Uploads:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "HEAD"],
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://d2onzy56n3iw1w.cloudfront.net"
    ],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 3600
  }
]
```

4. Click **Save changes**.
