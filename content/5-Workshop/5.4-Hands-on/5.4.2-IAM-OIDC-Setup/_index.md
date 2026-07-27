---
title: "Access Permission & Security Setup (AWS IAM & OIDC)"
date: 2026-07-27
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

In this step, practitioners configure **Zero Static Credentials** security by creating a GitHub OpenID Connect (OIDC) Provider, initializing an IAM Deploy Role for GitHub Actions, and creating an IAM Role for the EC2 server.

---

### 2.1. Create GitHub OIDC Provider in AWS IAM

The OIDC mechanism enables GitHub Actions to fetch short-lived temporary credentials from AWS Security Token Service (STS) during pipeline execution. This completely **eliminates long-term static `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`** from GitHub Secrets or servers.

1. Log into **AWS Management Console** -> **IAM** service -> select **Identity providers** -> click **Add provider**.
2. Select Provider Type: **OpenID Connect**.
3. Configure parameters:
   - **Provider URL:** `https://token.actions.githubusercontent.com`
   - **Audience:** `sts.amazonaws.com`
4. Click **Get thumbprint** for AWS to automatically verify GitHub's certificate thumbprint.
5. Click **Add provider** to finish.

![Configuring GitHub OIDC Provider in AWS IAM](/images/5-Workshop/5.4/5.4.2.1.png)
<p align="center"><i>Figure 5.4.2.1 — Configuring GitHub OIDC Provider in AWS IAM.</i></p>

---

### 2.2. Create IAM Role for GitHub Actions (`LearnSphereGitHubDeployRole`)

1. In IAM Console, navigate to **Roles** -> click **Create role**.
2. **Selected trusted entity:** Select **Web identity**.
   - **Identity provider:** Select `token.actions.githubusercontent.com`.
   - **Audience:** Select `sts.amazonaws.com`.
3. Name Role: `LearnSphereGitHubDeployRole`.
4. Configure a strict **Trust Policy** allowing only the `main` branch of your LearnSphere repository to execute `sts:AssumeRoleWithWebIdentity`:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::575620421319:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:Sonnguyen2410/Report_Nguyen_Hong_Son:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

![Trust policy restricting GitHub repository allowed to assume role](/images/5-Workshop/5.4/5.4.2.2.1.png)
<p align="center"><i>Figure 5.4.2.2a — Trust policy constraining access strictly to the GitHub main branch.</i></p>

5. Attach minimal permissions (**Least Privilege**):
   - Push Docker Images to ECR repository `learnsphere-be`.
   - Upload Frontend build artifacts to S3 bucket `learnsphere-fe-575620421319`.
   - Create CloudFront invalidations.
   - Send `AWS-RunShellScript` commands to the EC2 server via AWS Systems Manager (SSM).

![Deployment permissions attached to GitHub Actions](/images/5-Workshop/5.4/5.4.2.2.2.png)
<p align="center"><i>Figure 5.4.2.2b — Deployment permission policies attached to GitHub Actions.</i></p>

---

### 2.3. Create IAM Role for EC2 Server (`LearnSphereEc2Role`)

This role is attached directly to the EC2 Instance Profile, allowing the Backend Node.js app and AWS CLI on the server to automatically fetch temporary credentials from IMDSv2 without static Access Keys in `.env`.

1. In IAM Roles Console, click **Create role** -> Trusted entity: **AWS service** -> Use case: **EC2**.
2. Name Role: `LearnSphereEc2Role`.
3. Attach minimal policy permissions:
   - `AmazonSSMManagedInstanceCore`: Enables remote connection via Systems Manager Session Manager.
   - `AmazonEC2ContainerRegistryReadOnly`: Allows pulling Docker Images from ECR.
   - **S3 Media Custom Policy:** Grants `ListBucket`, `PutObject`, `GetObject`, `DeleteObject`, `AbortMultipartUpload` on bucket `learnsphere-media-575620421319`.
   - **CloudWatch Logs Custom Policy:** Grants log stream creation and event logging to Log Group `/learnsphere/backend`.
   - **Amazon Bedrock Policy:** Grants `InvokeModel` and `InvokeModelWithResponseStream`.

![IAM Role providing AWS permissions for Backend on EC2](/images/5-Workshop/5.4/5.4.2.3.png)
<p align="center"><i>Figure 5.4.2.3 — IAM Role providing automated temporary credentials for Node.js Backend on EC2.</i></p>
