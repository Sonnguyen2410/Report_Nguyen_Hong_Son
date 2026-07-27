---
title: "Configure Amazon CloudFront CDN"
date: 2026-07-27
weight: 7
chapter: false
pre: " <b> 5.4.7. </b> "
---

In this step, practitioners create an **Amazon CloudFront Distribution** serving as the single HTTPS entrypoint for the LearnSphere system, connecting to S3 Frontend via OAC and forwarding API traffic `/api/*` to the EC2 server.

---

### 7A. Frontend S3 & Origin Access Control (OAC) Setup

#### 7A.1. Create CloudFront Distribution

1. Open **AWS Management Console** -> navigate to **Amazon CloudFront** -> click **Create distribution**.
2. **Origin Domain (S3 FE):** Select `learnsphere-fe-575620421319.s3.ap-southeast-1.amazonaws.com`.
3. **Origin Access:** Select **Origin access control settings (recommended)** -> Create control setting (Sign requests enabled).
4. **Default Cache Behavior (`/*`):**
   - **Default root object:** `index.html`
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`
   - **Allowed HTTP Methods:** `GET, HEAD`
   - **Cache Policy:** `CachingOptimized`
5. Click **Create distribution**.

#### 7A.2. Update S3 Frontend Bucket Policy

After Distribution creation, copy the generated OAC Bucket Policy banner and update it in S3 Bucket `learnsphere-fe-575620421319` (**Permissions** tab -> **Bucket policy**):

```json
{
  "Version": "2008-10-17",
  "Id": "PolicyForCloudFrontPrivateContent",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipal",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::learnsphere-fe-575620421319/*",
      "Condition": {
        "ArnLike": {
          "AWS:SourceArn": "arn:aws:cloudfront::575620421319:distribution/EQRDOBSCG5MC8"
        }
      }
    }
  ]
}
```

![Bucket policy updated after creating CloudFront OAC](/images/5-Workshop/5.4/5.4.7.A.png)
<p align="center"><i>Figure 5.4.7.A — Updating S3 Bucket Policy granting read permissions to CloudFront OAC.</i></p>

---

### 7B. Connect Backend EC2 & Create API Behavior `/api/*`

#### 7B.1. Add EC2 Backend Origin

1. Open CloudFront Distribution `EQRDOBSCG5MC8` -> **Origins** tab -> click **Create origin**.
2. **Origin Domain:** EC2 IPv4 Public DNS (e.g., `ec2-xx-xx-xx-xx.ap-southeast-1.compute.amazonaws.com`).
3. **Protocol Policy:** `HTTP Only`, Port `5000`.

![Dual origins Frontend and Backend on CloudFront distribution](/images/5-Workshop/5.4/5.4.7.B.1.png)
<p align="center"><i>Figure 5.4.7.B.1 — List of dual origins (S3 Frontend and EC2 Backend) on CloudFront Distribution.</i></p>

#### 7B.2. Create Behavior `/api/*`

1. Select **Behaviors** tab -> click **Create behavior**:
   - **Path pattern:** `/api/*`
   - **Target Origin:** Select EC2 Backend Origin.
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`
   - **Allowed HTTP Methods:** `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`
   - **Cache Policy:** `CachingDisabled` (Strictly no API caching)
   - **Origin Request Policy:** `AllViewerExceptHostHeader` (Preserves Authorization JWT headers)

> **Benefit:** Browser calls `/api/*` on the single CloudFront domain, CloudFront reverse-proxies to EC2 port 5000, **completely eliminating CORS and Mixed Content errors**.

![CloudFront routing configuration for Frontend and Backend API](/images/5-Workshop/5.4/5.4.7.B.2.png)
<p align="center"><i>Figure 5.4.7.B.2 — CloudFront Behavior rules separating Frontend (`/*`) and Backend API (`/api/*`).</i></p>

#### 7B.3. Attach CloudFront Function for Client-Side SPA Routing

To prevent `404 Not Found` errors when refreshing sub-routes like `/profile` or `/courses`, attach a CloudFront Function:

```javascript
function handler(event) {
  var request = event.request;
  var uri = request.uri;

  if (uri.endsWith("/") || !uri.split("/").pop().includes(".")) {
    request.uri = "/index.html";
  }

  return request;
}
```

> Associate this function with **Viewer Request** events on the Default Behavior `/*`.

![CloudFront Function supporting client-side SPA routing](/images/5-Workshop/5.4/5.4.7.B.3.png)
<p align="center"><i>Figure 5.4.7.B.3 — CloudFront Function rewriting sub-path requests to /index.html for React Router.</i></p>

#### 7B.4. Record CloudFront Details

```text
Distribution ID: EQRDOBSCG5MC8
Domain Name: d2onzy56n3iw1w.cloudfront.net
```
