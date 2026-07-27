---
title: "Configure GitHub Secrets & CI/CD Pipeline"
date: 2026-07-27
weight: 9
chapter: false
pre: " <b> 5.4.9. </b> "
---

In this step, practitioners declare Repository Secrets on GitHub and build the automated deployment workflow `.github/workflows/deploy.yml` with candidate container health checks and automated rollback mechanisms.

---

### 9.1. Declare GitHub Actions Secrets

Open GitHub Repository -> **Settings** -> **Secrets and variables** -> **Actions** -> click **New repository secret**:

| Secret | Value / Content |
|---|---|
| `AWS_GITHUB_ROLE_ARN` | `arn:aws:iam::575620421319:role/LearnSphereGitHubDeployRole` |
| `EC2_INSTANCE_ID` | `i-008c48e6c120b2978` |
| `VITE_API_BASE_URL` | `/api` |
| `S3_FE_BUCKET` | `learnsphere-fe-575620421319` |
| `CLOUDFRONT_FE_DISTRIBUTION_ID` | `EQRDOBSCG5MC8` |

> **No Static Access Keys:** Do NOT declare `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, or `EC2_SSH_KEY` because the deployment workflow relies 100% on **OIDC** authentication and **AWS Systems Manager (SSM)** remote execution.

![Configuration secrets used by the CI/CD pipeline](/images/5-Workshop/5.4/5.4.9.1.png)
<p align="center"><i>Figure 5.4.9.1 — Declaring Repository Secrets used by the CI/CD deployment pipeline.</i></p>

---

### 9.2. Construct CI/CD Workflow (`.github/workflows/deploy.yml`)

Create `.github/workflows/deploy.yml` to automate 2 jobs: **deploy-backend** and **deploy-frontend**:

```yaml
name: Deploy LearnSphere to AWS

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  id-token: write

jobs:
  deploy-backend:
    name: Deploy Backend (Docker -> ECR -> EC2 via SSM)
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v4

      - name: Setup Node.js 24
        uses: actions/setup-node@v4
        with:
          node-version: "24"

      - name: Run Backend Tests
        run: |
          cd LearnSphere_BE
          npm ci
          npm test

      - name: Configure AWS Credentials via OIDC
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_GITHUB_ROLE_ARN }}
          aws-region: ap-southeast-1

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build & Push Backend Docker Image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build -t $ECR_REGISTRY/learnsphere-be:$IMAGE_TAG LearnSphere_BE/
          docker push $ECR_REGISTRY/learnsphere-be:$IMAGE_TAG
          docker tag $ECR_REGISTRY/learnsphere-be:$IMAGE_TAG $ECR_REGISTRY/learnsphere-be:latest
          docker push $ECR_REGISTRY/learnsphere-be:latest

      - name: Deploy Backend to EC2 via AWS SSM RunCommand
        run: |
          aws ssm send-command \
            --instance-ids "${{ secrets.EC2_INSTANCE_ID }}" \
            --document-name "AWS-RunShellScript" \
            --parameters commands='[
              "aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin '${{ steps.login-ecr.outputs.registry }}'",
              "docker pull '${{ steps.login-ecr.outputs.registry }}'/learnsphere-be:'${{ github.sha }}'",
              "docker stop candidate || true && docker rm candidate || true",
              "docker run -d -p 5001:5000 --name candidate --env-file /home/ec2-user/.env '${{ steps.login-ecr.outputs.registry }}'/learnsphere-be:'${{ github.sha }}'",
              "SUCCESS=0",
              "for i in {1..24}; do if curl -s http://localhost:5001/health/ready | grep -q \"ready\"; then SUCCESS=1; break; fi; sleep 5; done",
              "if [ $SUCCESS -eq 1 ]; then echo \"Health check passed! Swapping containers...\"; docker stop learnsphere-be-rollback || true && docker rm learnsphere-be-rollback || true; docker rename learnsphere-be learnsphere-be-rollback || true; docker rename candidate learnsphere-be; docker stop learnsphere-be || true; docker run -d -p 5000:5000 --name learnsphere-be --restart unless-stopped --env-file /home/ec2-user/.env --log-driver awslogs --log-opt awslogs-group=/learnsphere/backend '${{ steps.login-ecr.outputs.registry }}'/learnsphere-be:'${{ github.sha }}'; else echo \"Health check failed! Aborting candidate container...\"; docker stop candidate || true && docker rm candidate || true; exit 1; fi"
            ]'

  deploy-frontend:
    name: Deploy Frontend (Build -> S3 -> CloudFront Invalidation)
    needs: deploy-backend
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v4

      - name: Setup Node.js 24
        uses: actions/setup-node@v4
        with:
          node-version: "24"

      - name: Install & Build Frontend
        env:
          VITE_API_BASE_URL: ${{ secrets.VITE_API_BASE_URL }}
        run: |
          cd LearnSphere_FE
          npm ci
          npm run build

      - name: Configure AWS Credentials via OIDC
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_GITHUB_ROLE_ARN }}
          aws-region: ap-southeast-1

      - name: Deploy Assets to S3 & Create CloudFront Invalidation
        run: |
          aws s3 sync LearnSphere_FE/dist s3://${{ secrets.S3_FE_BUCKET }} --delete
          aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_FE_DISTRIBUTION_ID }} --paths "/*"
```

![Automated CI/CD workflow of LearnSphere](/images/5-Workshop/5.4/5.4.9.2.png)
<p align="center"><i>Figure 5.4.9.2 — Automated execution flow of the deploy.yml workflow.</i></p>

---

### 9.3. Trigger CI/CD Pipeline & Resolve Initial OIDC Errors

Push code to main to trigger the pipeline automatically:

```powershell
git add .
git commit -m "feat: deploy LearnSphere to AWS"
git push origin main
```

![Backend and Frontend deployment pipeline completed successfully](/images/5-Workshop/5.4/5.4.9.3.png)
<p align="center"><i>Figure 5.4.9.3 — GitHub Actions pipeline completed successfully for both Backend and Frontend jobs.</i></p>

#### Troubleshooting Initial OIDC Errors:
If you encounter `Could not assume role with OIDC: Not authorized to perform sts:AssumeRoleWithWebIdentity`, verify that the IAM Trust Policy for `LearnSphereGitHubDeployRole` matches your exact repository string `repo:username/repository:ref:refs/heads/main`. Click **Re-run jobs** on GitHub Actions to complete the deployment.
