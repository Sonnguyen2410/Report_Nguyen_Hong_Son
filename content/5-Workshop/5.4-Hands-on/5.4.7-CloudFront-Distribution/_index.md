---
title: "Configure Amazon CloudFront CDN"
date: 2026-07-27
weight: 7
chapter: false
pre: " <b> 5.4.7. </b> "
---

# 5.4.7. Configure Amazon CloudFront CDN (Origins, Behaviors & SPA Function)

In this step, practitioners create an **Amazon CloudFront Distribution** serving as the single HTTPS entrypoint for the LearnSphere application, routing static assets to S3 and forwarding API traffic to the EC2 server.

---

### 1. Create CloudFront Distribution

1. Access **Amazon CloudFront** service $\rightarrow$ click **Create distribution**.
2. **Origin Domain 1 (S3 Frontend):** Select `learnsphere-fe-575620421319.s3.ap-southeast-1.amazonaws.com`.
3. **Origin Access:** Select **Origin access control settings (recommended)** $\rightarrow$ click **Create control setting** (Sign requests enabled).
4. **Default Cache Behavior (`/*`):**
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`.
   - **Allowed HTTP Methods:** `GET, HEAD`.
   - **Cache Policy:** `CachingOptimized`.
5. Click **Create distribution**.

---

### 2. Update S3 Frontend Bucket Policy

1. Copy the OAC Bucket Policy banner provided by CloudFront.
2. Open S3 Bucket `learnsphere-fe-575620421319` $\rightarrow$ **Permissions** $\rightarrow$ **Bucket policy** $\rightarrow$ click **Edit** and paste:

```json
{
  "Version": "2012-10-17",
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
          "AWS:SourceArn": "arn:aws:cloudfront::575620421319:distribution/*"
        }
      }
    }
  ]
}
```

---

### 3. Add Origin 2 (EC2 Backend) & API Behavior (`/api/*`)

1. In CloudFront Distribution $\rightarrow$ **Origins** tab $\rightarrow$ click **Create origin**.
2. **Origin Domain:** EC2 IPv4 Public DNS (e.g., `ec2-xx-xx-xx-xx.ap-southeast-1.compute.amazonaws.com`).
3. **Protocol Policy:** `HTTP Only`, Port `5000`.
4. Navigate to **Behaviors** tab $\rightarrow$ click **Create behavior**:
   - **Path pattern:** `/api/*`
   - **Target Origin:** Select EC2 Backend Origin.
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`.
   - **Allowed HTTP Methods:** `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`.
   - **Cache Policy:** `CachingDisabled`.
   - **Origin Request Policy:** `AllViewerExceptHostHeader`.

---

### 4. Attach CloudFront Function for Client-Side SPA Routing

1. Select **Functions** from the left CloudFront menu $\rightarrow$ click **Create function**.
2. **Name:** `LearnSphereSPARouting`.
3. Paste the JavaScript rewrite function:

```javascript
function handler(event) {
    var request = event.request;
    var uri = request.uri;
    
    if (!uri.includes('.')) {
        request.uri = '/index.html';
    }
    
    return request;
}
```

4. Save and click **Publish function**.
5. Associate this Function with the Default Behavior (`/*`) under **Viewer Request** events.
