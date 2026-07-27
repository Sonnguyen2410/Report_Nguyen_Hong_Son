---
title: "Resource Clean-up"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### 5.5.1. Resource Clean-up Process Overview

After completing the deployment workshop and verifying the **LearnSphere** system on AWS, tearing down all provisioned cloud resources is a mandatory final step. The objective of this process is to fully release unused resources and strictly prevent unexpected recurring charges on your AWS account.

The clean-up order must strictly follow the **Reverse Dependency Order** to avoid deletion errors caused by resource dependencies:

```text
[1. CloudFront Distribution] ➔ [2. S3 Buckets] ➔ [3. EC2 Instance] ➔ [4. ECR Repository] ➔ [5. CloudWatch & SNS] ➔ [6. IAM Roles & OIDC]
```

---

### 5.5.2. Detailed AWS Resource Clean-up Steps

#### Step 1: Disable and Delete Amazon CloudFront Distribution

CloudFront Distributions must be disabled before they can be permanently deleted:

1. Navigate to **AWS Management Console** -> Search for and select **CloudFront**.
2. From the Distributions list, select the Distribution created for LearnSphere (Distribution ID: `EQRDOBSCG5MC8`).
3. Click **Disable** and confirm. Wait 3 to 5 minutes for the status to change from `Enabled` to `Disabled` (completing global edge location deployment).
4. Once the status displays `Disabled`, select the Distribution again and click **Delete** to permanently remove it.

#### Step 2: Empty and Delete Amazon S3 Buckets

S3 Buckets can only be deleted after emptying all contained objects:

1. Navigate to **Amazon S3**.
2. **Clean up Media Bucket (`learnsphere-media-575620421319`):**
   - Select bucket `learnsphere-media-575620421319`.
   - Click **Empty**, type `permanently delete` to confirm purging all stored videos, thumbnails, and documents.
   - Once emptied, return to Buckets list, select the bucket, and click **Delete**.
3. **Clean up Frontend Bucket (`learnsphere-fe-575620421319`):**
   - Repeat process: Select `learnsphere-fe-575620421319` -> Click **Empty** to purge React static assets -> Click **Delete** to remove the bucket.

#### Step 3: Terminate Amazon EC2 Instance

Terminating the EC2 instance automatically releases its Public IP address and attached EBS storage volumes:

1. Navigate to **Amazon EC2** -> Select **Instances**.
2. Select the LearnSphere Backend server (Instance ID: `i-008c48e6c120b2978`).
3. Click **Instance state** menu -> Select **Terminate instance**.
4. Click **Terminate** to confirm. Status will transition from `Running` -> `Shutting-down` -> `Terminated`.

#### Step 4: Delete Amazon ECR Repository

1. Navigate to **Amazon ECR** -> Select **Private repositories**.
2. Select repository `learnsphere-be`.
3. Click **Delete**, type repository name `learnsphere-be` to confirm deleting the repository and all contained Docker Image tags.

#### Step 5: Delete CloudWatch & Amazon SNS Monitoring Resources

1. **Delete CloudWatch Alarms:**
   - Open **CloudWatch** -> Select **Alarms** -> **All alarms**.
   - Select the 2 alarms: `LearnSphere-EC2-HighCPU` and `LearnSphere-EC2-StatusCheckFailed`.
   - Click **Actions** -> Click **Delete**.
2. **Delete CloudWatch Log Group:**
   - Select **Logs** -> **Log groups**.
   - Find Log Group `/learnsphere/backend`.
   - Click **Actions** -> Click **Delete log group**.
3. **Delete Amazon SNS Topic & Subscriptions:**
   - Navigate to **Amazon SNS** -> Select **Topics**.
   - Select Topic `LearnSphere-Alerts` -> Click **Delete**.
   - Select **Subscriptions** -> Select associated email subscription -> Click **Delete**.

#### Step 6: Delete IAM Roles & OIDC Provider

1. **Delete IAM Roles:**
   - Navigate to **IAM** service -> Select **Roles**.
   - Search for and delete Role `LearnSphereGitHubDeployRole` (GitHub Actions deploy role).
   - Search for and delete Role `LearnSphereEc2Role` (EC2 server role).
2. **Delete Identity Provider:**
   - In IAM menu -> Select **Identity providers**.
   - Select Provider `token.actions.githubusercontent.com` -> Click **Delete**.

---

### 5.5.3. Clean-up Acceptance Checklist

| AWS Resource | Resource Name / Identifier | Post-Clean-up Status |
|---|---|---|
| CloudFront | Distribution ID: `EQRDOBSCG5MC8` | Deleted |
| S3 Media Bucket | `learnsphere-media-575620421319` | Emptied & Deleted |
| S3 FE Bucket | `learnsphere-fe-575620421319` | Emptied & Deleted |
| EC2 Instance | Instance ID: `i-008c48e6c120b2978` | Terminated |
| ECR Repository | `learnsphere-be` | Repository Deleted |
| CloudWatch Alarms | `LearnSphere-EC2-HighCPU` & `LearnSphere-EC2-StatusCheckFailed` | Alarms Deleted |
| CloudWatch Logs | Log Group: `/learnsphere/backend` | Log Group Deleted |
| Amazon SNS | Topic: `LearnSphere-Alerts` | Topic & Subscriptions Deleted |
| IAM Roles | `LearnSphereGitHubDeployRole` & `LearnSphereEc2Role` | Roles Deleted |
| IAM OIDC | `token.actions.githubusercontent.com` | Provider Deleted |