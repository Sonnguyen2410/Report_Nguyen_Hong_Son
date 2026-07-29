---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
# [AWS Troubleshooting] Completely Resolving CORS and Routing Errors When Placing CloudFront in Front of an EC2 Backend


Hello everyone in the AWS Study Group,

When building a web application with a separated architecture: Frontend hosted on S3 + CloudFront and Backend REST API running on an EC2 Instance, one of the most common "nightmares" that devs encounter is the CORS (Cross-Origin Resource Sharing) Error and the loss of Query Parameters/Headers when requests go through CloudFront.

Many people often choose the "quick and dirty" solution of enabling `Access-Control-Allow-Origin: *` on the backend. However, this creates a serious security vulnerability and may not solve the root problem if the CloudFront Behavior configuration is wrong.

This article shares how our team analyzed the root cause and standard configuration on AWS to completely solve this problem.

## 1. The Root Cause of the Error

When the Client (browser) calls the API, the data flow is as follows:
* **Client Origin:** [https://app.learnsphere.com](https://app.learnsphere.com) (Served from CloudFront + S3)
* **Backend Origin:** [https://api.learnsphere.com](https://api.learnsphere.com) or the IP of the EC2 Instance.

The browser will automatically send a Preflight Request (HTTP OPTIONS) to ask the backend server if the frontend domain is allowed to access the resource.

**3 common configuration "traps" on AWS:**
* **CloudFront swallows the OPTIONS Request:** By default, CloudFront Behavior only allows basic HTTP Methods (GET, HEAD). OPTIONS requests are blocked right at the Edge Location and never reach EC2.
* **CloudFront does not Forward the Origin Header:** CloudFront defaults to not forwarding the `Origin`, `Access-Control-Request-Method` headers from the Client to the EC2 Backend. As a result, the Backend doesn't know where the client comes from to return the corresponding CORS header.
* **Mistakenly Caching CORS Response:** CloudFront caches the API response. If the first request from Client A lacks a CORS header and is cached, all subsequent Client Bs will also encounter CORS errors due to receiving this faulty cached version.

## 2. Standard Configuration Solution on AWS CloudFront

To completely resolve this without lowering security standards, the CloudFront Behavior configuration specifically for the `/api/*` path (pointing to the EC2 Origin) needs to have the following parameters set correctly:

**Step 1: Allow full HTTP Methods**
In the Allowed HTTP Methods section, switch from `GET, HEAD` to `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`.
This ensures CloudFront forwards all Preflight requests to EC2 for processing.

**Step 2: Enable Forward Headers Configuration (Origin & Authorization)**
In the Cache Key and Origin Requests section, select the Custom / Cache Policy configuration:
Add the mandatory headers to Forward including: `Origin`, `Access-Control-Request-Method`, `Access-Control-Request-Headers`, and `Authorization` (if using Bearer Token).
This helps the EC2 Backend correctly identify the source domain and respond with the correct `Access-Control-Allow-Origin` string.

**Step 3: Use Response Headers Policy (Recommended)**
Instead of complex CORS handling in every piece of code in the application on EC2, AWS provides the Response Headers Policy feature right at CloudFront:
* You create a CORS Response Headers Policy in the CloudFront Console.
* Clearly define:
  * `Allow-Origin`: Only fill in the official domain of the Frontend.
  * `Allow-Credentials`: `true` (if using Cookie/Session).
  * `Allow-Headers` & `Allow-Methods`: Corresponding to system requirements.
* Attach this Policy to the CloudFront Behavior. CloudFront will automatically attach standard CORS headers to every response returned to the Client.

## 3. Optimal Security Group Configuration for EC2

After CloudFront has correctly handled the traffic flow, the next step is security for the EC2 Backend:
* **Do not open Public 0.0.0.0/0 indiscriminately:** Only open port HTTP/HTTPS for the representative IP ranges of CloudFront (use AWS Managed Prefix List for CloudFront) or only receive traffic via an Application Load Balancer (ALB).
* **Ensure Health Check:** Configure the `/health` or `/ping` path on EC2 for CloudFront/ALB to check the container's operational status.

## 4. Resulting Benefits

* **Completely resolves 100% of CORS errors:** The system runs smoothly on all browsers without having to disable strict security modes.
* **Optimizes Caching capabilities:** Only caches necessary data, avoiding the situation of caching the wrong API response header.
* **Enhances Security:** Eliminates the use of wildcard `*` for CORS Origin, protecting the API from unauthorized access from strange domains.

![Blog 2](/images/blog2.png)

---

🔗 **Original Facebook Post:** [AWS Study Group Post](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2227706841327609/#)