---
title: "Create Amazon ECR Repository"
date: 2026-07-27
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

In this step, practitioners initialize a Private **Amazon ECR (Elastic Container Registry)** repository to manage and store Backend Docker Images for the LearnSphere system.

---

### 4.1. Initialize Private ECR Repository

1. Open **AWS Management Console** -> navigate to **Amazon ECR** -> select **Private repositories**.
2. Click **Create repository**.
3. **Visibility settings:** Select **Private**.
4. **Repository name:** Name the repository:

```text
learnsphere-be
```

5. **Image scan settings:** Enable **Scan on push = ON** (Automatically scans Docker images for CVE security vulnerabilities upon each push).
6. Click **Create repository**.

![Docker images of Backend stored on Amazon ECR](/images/5-Workshop/5.4/5.4.4.png)
<p align="center"><i>Figure 5.4.4 — Private ECR Repository storing and vulnerability-scanning Backend Docker images.</i></p>

---

### 4.2. Configure Lifecycle Policy for Automated Image Cleanup

To optimize storage costs on ECR, create an automated rule to purge older unused Docker images:

1. Open Repository `learnsphere-be` -> select **Lifecycle policies** from the left menu -> click **Create rule**.
2. **Rule priority:** `1`.
3. **Description:** `Keep 10 most recent Docker images`.
4. **Image status:** `Tagged`.
5. **Match criteria:** Select `Image count more than` -> Count: `10`.
6. Click **Save**.

---

### 4.3. Image Tagging Convention via Git Commit SHA

Every Docker Image pushed to ECR during the CI/CD pipeline will be tagged matching the exact Git Commit SHA hash:

```text
575620421319.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:<GIT_SHA>
```

> **Benefits:** Tagging by Commit SHA pinpoints the exact version running in Production and enables automated Rollback to previous container releases.
