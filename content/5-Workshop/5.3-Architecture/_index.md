---
title: "Architecture Description"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

The following section provides an in-depth analysis of the entire cloud infrastructure architecture for the LearnSphere system on AWS, detailing data flow mechanisms, technical component specifications, network encryption, security permission design, and automated deployment procedures.

![LearnSphere AWS Architecture](/images/LEARNSHPHERE.drawio.png)

---

### 1. Data Flow Mechanics & System Interactions

The LearnSphere infrastructure is designed to seamlessly process 4 primary data flows across the system:

#### Flow 1: Frontend Asset Loading & Single-Page Application (React SPA Routing Flow)
* **Connection Initialization:** Users access CloudFront's HTTPS domain from a browser. CloudFront handles TLS/SSL connections using standard certificates.
* **Static Asset Fetching:** Requests matching default behavior (`/`) are routed to the S3 Frontend Bucket via Origin Access Control (OAC) to retrieve static files (`index.html`, JavaScript, CSS, images). The S3 Bucket verifies OAC signatures and returns content to CloudFront for delivery to the browser.
* **Page Reload / Client-Side Sub-routes (F5 Handling):** When users refresh sub-routes like `/profile` or `/courses/123`, a CloudFront Function attached to the Viewer Request event intercepts the request. If no file extension dot is present in the URI, it internally rewrites the request to `/index.html`. React Router handles client-side rendering without triggering 404 errors from S3.

#### Flow 2: Backend REST API Execution Flow
* **Request Forwarding:** When the React app makes business API calls (login, course listing, quiz submission), requests starting with `/api/*` match the priority CloudFront `/api/*` behavior and are forwarded directly to the EC2 instance.
* **Cache Bypass & Header Preservation:** This behavior has `CachingDisabled` and uses the `AllViewerExceptHostHeader` policy to preserve original HTTP headers from the browser (including JWT authentication in the Authorization header).
* **EC2 Inbound Processing:** Packets pass through the EC2 Security Group (restricted to CloudFront global Prefix List IPs) on port 5000. The Express.js Docker Container processes the request, decodes JWT tokens, and executes business logic.
* **Database Queries:** The Backend uses Mongoose ODM to query the MongoDB Atlas Cluster over a secure SRV connection. JSON responses are returned to the browser via CloudFront.

#### Flow 3: Large Media File Upload & Download (Media Presigned URL Flow)
* **Upload Flow (Video/PDF/Image Uploads):**
  1. Instructors select lecture video files on the React UI. Frontend sends a short request to Backend requesting upload authorization.
  2. Backend validates Instructor permissions and uses AWS SDK (with temporary credentials from EC2 IAM Role) to generate a Presigned PUT URL valid for 5 minutes.
  3. Backend returns the Presigned PUT URL to the Frontend.
  4. Frontend uses this Presigned URL to upload files directly from the browser to the S3 Media Bucket without passing through the EC2 server. Large files use S3 Multipart Upload, exposed via S3 CORS ETag headers.
* **Download Flow (Video Streaming / Document Reading):**
  1. Students click to view a lesson. Frontend requests a video playback URL from Backend.
  2. Backend verifies course enrollment. If valid, Backend generates a Presigned GET URL valid for 15 minutes and returns it to Frontend.
  3. Browser uses the Presigned GET URL to stream video directly from the S3 Media Bucket. S3 Buckets remain 100% private to the public Internet.

#### Flow 4: Automated CI/CD Deployment & Rollback Flow
* **OIDC Authentication & Image Build:** Engineers push code to the `main` branch on GitHub. GitHub Actions triggers the workflow, authenticates via OIDC with AWS STS to receive short-term credentials, builds the application, tags the Backend Docker Image with the Git Commit SHA, and pushes it to Amazon ECR.
* **SSM Deployment Execution:** GitHub Actions executes AWS SSM RunCommand to send an encrypted deployment script to the SSM Agent on EC2.
* **Candidate Container Deployment:** The script on EC2 pulls the new image from ECR and launches a test container named `candidate` on temporary port 5001.
* **Health Check Retry:** The script retries `/health/ready` endpoint checks on port 5001 up to 24 times (every 5 seconds).
* **Success Path:** If the endpoint responds 200 OK and database connection is healthy, the script renames the current main container to `rollback`, switches the new container to main port 5000, and removes the test container. Switchover takes milliseconds.
* **Failure Path:** If 24 checks fail, the script stops and removes the `candidate` container, keeping the existing container on port 5000, and reports a failure to GitHub Actions to abort the pipeline.

---

### 2. Component Technical Specifications & Detailed Design

#### Amazon CloudFront (Distribution Configuration)
* **Distribution Domain:** Automatically assigned domain format `d2onzy56n3iw1w.cloudfront.net`.
* **Origin 1 Configuration (S3 Frontend):**
  * Origin Domain: Points to S3 Bucket `learnsphere-fe-575620421319`.
  * Origin Access: Origin Access Control (OAC) with Sign Requests enabled.
* **Origin 2 Configuration (EC2 Backend):**
  * Origin Domain: EC2 IPv4 Public IP / DNS address.
  * Protocol Policy: HTTP Only (Internal communication between CloudFront and EC2).
  * HTTP Port: `5000`.
* **Default Cache Behavior (`/*`):**
  * Target Origin: S3 Frontend Origin.
  * Viewer Protocol Policy: Redirect HTTP to HTTPS.
  * Allowed HTTP Methods: GET, HEAD.
  * Cache Policy: `CachingOptimized` (Optimized caching for hashed JS/CSS assets).
  * Function Associations: CloudFront Function attached to Viewer Request for SPA routing.
* **API Cache Behavior (`/api/*`):**
  * Target Origin: EC2 Backend Origin.
  * Viewer Protocol Policy: Redirect HTTP to HTTPS.
  * Allowed HTTP Methods: GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE.
  * Cache Policy: `CachingDisabled` (Strictly no API caching).
  * Origin Request Policy: `AllViewerExceptHostHeader`.

#### Amazon S3 (Dual Bucket Configuration)
* **Frontend Bucket (`learnsphere-fe-575620421319`):**
  * Region: `ap-southeast-1`.
  * Block Public Access: ON (Block all public access).
  * S3 Static Website Hosting: Disabled.
  * Bucket Policy: Restricts `s3:GetObject` access exclusively to `cloudfront.amazonaws.com` Service Principal matching CloudFront SourceArn.
* **Media Bucket (`learnsphere-media-575620421319`):**
  * Region: `ap-southeast-1`.
  * Block Public Access: ON (Block all public access).
  * CORS Configuration:
    * AllowedOrigins: `http://localhost:5173`, `https://d2onzy56n3iw1w.cloudfront.net`.
    * AllowedMethods: GET, PUT, HEAD.
    * AllowedHeaders: `*`.
    * ExposeHeaders: `ETag`.
    * MaxAgeSeconds: `3600`.

#### Amazon ECR (Repository Configuration)
* **Repository Name:** `learnsphere-be`.
* **Repository Type:** Private.
* **Image Scan Settings:** Enable Scan on Push (Automatic CVE vulnerability scanning on every image push).
* **Lifecycle Policy Rules:** Keeps the 10 most recent tagged images and automatically purges older images to optimize storage costs.

#### Amazon EC2 & Docker Runtime Environment
* **Server Specifications:**
  * Instance ID: `i-008c48e6c120b2978`.
  * OS: Amazon Linux 2023 64-bit (x86).
  * Instance Type: `t3.small` (2 vCPU, 2.0 GiB Physical Memory).
  * Location: Default VPC, Public Subnet, Auto-assign Public IP Enabled.
* **RAM Swap Memory Configuration:**
  * Created 2.0 GB Swap file at `/swapfile`.
  * Restricted file permissions to `600` (root read/write only).
  * Registered auto-mount entry in `/etc/fstab`.
  * **Benefit:** Provides ~4.0 GB total virtual memory (2GB RAM + 2GB Swap), preventing Node.js multi-process memory crashes during OCR image analysis or PDF parsing.
* **Security Group Rules:**
  * Inbound Rules: Custom TCP, Port `5000`, Source: AWS Managed Prefix List `com.amazonaws.global.cloudfront.origin-facing`.
  * Blocks 100% of external traffic on ports 22 (SSH), 80 (HTTP), and 443 (HTTPS) from `0.0.0.0/0`.
* **Docker Container Security Context:**
  * Base Image: Lightweight Linux Alpine (`node:24-alpine`).
  * Non-root User: System group `nodejs` (GID 1001) and user `nodejs` (UID 1001).
  * Execution: Runs under non-root user `nodejs` to prevent host takeover vulnerabilities.
  * Health Check: Configured 30s `wget` checks to internal port 5000 `/health/ready`.

#### MongoDB Atlas Database
* **Deployment Model:** 3-node Replica Set (Primary - Secondary - Secondary) for high availability.
* **Connection Method:** `mongodb+srv://` protocol for auto-load balancing and primary node discovery.
* **Network Access Security:** Whitelisted EC2 Public IP on MongoDB Atlas Network Access IP Access List.

#### IAM Security Architecture & Zero Static Credentials
* **OIDC Identity Provider:**
  * Provider URL: `https://token.actions.githubusercontent.com`
  * Audience: `sts.amazonaws.com`
* **IAM Role 1 (`LearnSphereGitHubDeployRole`):**
  * Trust Policy: `sts:AssumeRoleWithWebIdentity` constrained to `sts.amazonaws.com` audience and matching LearnSphere `main` branch sub claim.
  * Permissions Policy: ECR authorization, push permissions, S3 Sync, CloudFront Invalidation, and SSM SendCommand targeted strictly to the EC2 Instance ID.
* **IAM Role 2 (`LearnSphereEc2Role`):**
  * Attached Policies: `AmazonSSMManagedInstanceCore`, `AmazonEC2ContainerRegistryReadOnly`, S3 Media Custom Policy, CloudWatch Logs Custom Policy, and Bedrock Custom Policy.

#### CloudWatch Monitoring & SNS Alert System
* **Log Aggregation (CloudWatch Logs):**
  * Log Group Name: `/learnsphere/backend`.
  * Retention Policy: 30 days.
  * Log Driver: `awslogs` driver streaming container logs.
* **CloudWatch Alarm 1 (EC2 High CPU):**
  * Alarm Name: `LearnSphere-EC2-HighCPU`.
  * Metric: `CPUUtilization` (> 80% average over two 5-minute periods).
  * Notification: Sends alert to SNS Topic `LearnSphere-Alerts`.
* **CloudWatch Alarm 2 (EC2 Status Check Failed):**
  * Alarm Name: `LearnSphere-EC2-StatusCheckFailed`.
  * Metric: `StatusCheckFailed` (>= 1 over 1-minute period).
  * Notification: Triggers immediate alert to SNS Topic `LearnSphere-Alerts`.
* **Amazon SNS Topic & Email Subscription:**
  * Topic Name: `LearnSphere-Alerts` (Email subscription confirmed).

---

### 3. Legacy Architecture vs. LearnSphere Modern Cloud Architecture Comparison

| Feature | Legacy Architecture | LearnSphere Modern Cloud Architecture |
| --- | --- | --- |
| **Credential Authentication** | Long-term static Access Keys / Secret Keys stored in `.env` or GitHub Secrets. High leakage risk. | **100% Zero Static Credentials.** Uses OIDC for CI/CD and IAM Instance Profiles (IMDSv2) for EC2. |
| **Server Administration** | Open SSH port 22 to Internet. Vulnerable to Brute-force attacks. | **SSH Port 22 completely closed.** 100% remote administration via AWS Systems Manager (SSM) Session Manager. |
| **Domain & Distribution** | Separate domains or direct IP:5000 access. Prone to CORS and Mixed Content errors. | **Single HTTPS Domain via CloudFront CDN.** S3 FE via OAC and `/api/*` reverse proxy to EC2. Zero CORS errors. |
| **Deployment Workflow** | Manual SSH `git pull` & restart. Causes downtime and crash risks. | **Automated CI/CD via GitHub Actions**, Multi-stage Docker, Candidate Container (Port 5001) testing, and zero-downtime health check switchover. |
| **Media File Management** | Uploads proxied through API server or public S3 bucket access. | **S3 Media Bucket 100% Private.** Direct browser uploads/downloads via short-lived Presigned URLs. |
