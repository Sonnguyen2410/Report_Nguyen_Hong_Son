---
title: "Access Permission & Security Setup (AWS IAM & OIDC)"
date: 2026-07-27
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

# 5.4.2. Access Permission & Security Setup (AWS IAM & OIDC)

In this step, practitioners configure **Zero Static Credentials** security by setting up a GitHub OIDC Identity Provider and initializing IAM Roles for EC2 servers and GitHub Actions CI/CD pipelines.

---

### 1. Initialize OpenID Connect (OIDC) Identity Provider for GitHub

1. Access **AWS Management Console** $\rightarrow$ **IAM** service $\rightarrow$ **Identity providers** $\rightarrow$ select **Add provider**.
2. Select Provider Type: **OpenID Connect**.
3. Configure parameters:
   - **Provider URL:** `https://token.actions.githubusercontent.com`
   - **Audience:** `sts.amazonaws.com`
4. Click **Get thumbprint** for AWS to automatically verify GitHub's certificate thumbprint, then click **Add provider**.

---

### 2. Create IAM Role 1 for GitHub Actions (`LearnSphereGitHubDeployRole`)

1. In the IAM console, navigate to **Roles** $\rightarrow$ click **Create role**.
2. Select **Web identity** $\rightarrow$ Select Identity Provider `token.actions.githubusercontent.com` $\rightarrow$ Audience: `sts.amazonaws.com`.
3. Specify GitHub Repository: `Sonnguyen2410/Report_Nguyen_Hong_Son` (or your LearnSphere repo).
4. Under **Permissions**, attach temporary access policies:
   - `AmazonEC2ContainerRegistryPowerUser`
   - `s3:Sync` permission for Frontend Bucket.
   - `cloudfront:CreateInvalidation` permission.
   - `ssm:SendCommand` permission targeted to the EC2 instance ID.
5. Name Role: `LearnSphereGitHubDeployRole` and finish creation.
6. Copy the **Role ARN** (e.g., `arn:aws:iam::575620421319:role/LearnSphereGitHubDeployRole`).

---

### 3. Create IAM Role 2 for EC2 Server (`LearnSphereEc2Role`)

1. Click **Create role** $\rightarrow$ Select **AWS service** $\rightarrow$ Use case: **EC2**.
2. Attach Managed Policies:
   - `AmazonSSMManagedInstanceCore`: Enables SSM Agent to maintain secure control connections with Systems Manager without open SSH port 22.
   - `AmazonEC2ContainerRegistryReadOnly`: Allows EC2 to pull Docker Images from ECR.
3. Attach Custom Policy granting access to S3 Media Bucket (`LearnSphereMediaPolicy`) and CloudWatch Log Group (`/learnsphere/backend`).
4. Name Role: `LearnSphereEc2Role` and save.
