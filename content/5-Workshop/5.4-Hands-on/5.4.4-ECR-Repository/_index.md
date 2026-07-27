---
title: "Create Amazon ECR Repository"
date: 2026-07-27
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

# 5.4.4. Create Amazon ECR (Elastic Container Registry) Repository

In this step, practitioners initialize a Private Repository on **Amazon ECR** to manage Backend Docker Images and configure automatic cleanup rules (Lifecycle Policy).

---

### 1. Initialize ECR Private Repository

1. Access **Amazon ECR** service $\rightarrow$ navigate to **Repositories** $\rightarrow$ click **Create repository**.
2. **Visibility settings:** Select **Private**.
3. **Repository name:** Name it `learnsphere-be`.
4. **Image scan settings:** Enable **Scan on push** (automatically scans for CVE security vulnerabilities upon image push).
5. Click **Create repository**.

---

### 2. Configure Lifecycle Policy for Automatic Image Purging

1. Open Repository `learnsphere-be` $\rightarrow$ select **Lifecycle policies** from the left menu $\rightarrow$ click **Create rule**.
2. **Rule priority:** `1`.
3. **Rule description:** `Keep last 10 tagged images`.
4. **Image status:** `Tagged`.
5. **Match criteria:** Select `Image count more than` $\rightarrow$ Count: `10`.
6. Click **Save**.

---

### 3. Verify ECR Login CLI Command

Test logging into your ECR Registry from local terminal:

```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 575620421319.dkr.ecr.ap-southeast-1.amazonaws.com
```

**Expected Result:** Terminal outputs `Login Succeeded`.
