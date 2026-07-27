---
title: "Configure GitHub Secrets & CI/CD Pipeline"
date: 2026-07-27
weight: 9
chapter: false
pre: " <b> 5.4.9. </b> "
---

# 5.4.9. Configure GitHub Secrets & CI/CD Pipeline (Zero-Downtime & Auto-Rollback)

In this step, practitioners configure Repository Secrets on GitHub and build the automated deployment workflow file `.github/workflows/deploy.yml` with candidate container testing and automated rollback capabilities.

---

### 1. Configure GitHub Repository Secrets

1. Open GitHub Repository $\rightarrow$ **Settings** $\rightarrow$ **Secrets and variables** $\rightarrow$ **Actions**.
2. Click **New repository secret** to declare parameters:
   - `AWS_ROLE_TO_ASSUME`: `arn:aws:iam::575620421319:role/LearnSphereGitHubDeployRole`
   - `AWS_REGION`: `ap-southeast-1`
   - `ECR_REPOSITORY`: `learnsphere-be`
   - `EC2_INSTANCE_ID`: `i-008c48e6c120b2978`
   - `S3_FRONTEND_BUCKET`: `learnsphere-fe-575620421319`
   - `CLOUDFRONT_DISTRIBUTION_ID`: `E3xxxxxxxxxxxx`

---

### 2. Craft CI/CD Workflow File (`.github/workflows/deploy.yml`)

Create `.github/workflows/deploy.yml` to automate the deployment pipeline:

```yaml
name: LearnSphere Production CI/CD Pipeline

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Configure AWS Credentials via OIDC
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build & Push Backend Docker Image to ECR
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build -t $ECR_REGISTRY/${{ secrets.ECR_REPOSITORY }}:$IMAGE_TAG LearnSphere_BE/
          docker push $ECR_REGISTRY/${{ secrets.ECR_REPOSITORY }}:$IMAGE_TAG
          docker tag $ECR_REGISTRY/${{ secrets.ECR_REPOSITORY }}:$IMAGE_TAG $ECR_REGISTRY/${{ secrets.ECR_REPOSITORY }}:latest
          docker push $ECR_REGISTRY/${{ secrets.ECR_REPOSITORY }}:latest

      - name: Deploy Frontend Static Assets to S3
        run: |
          cd LearnSphere_FE
          npm ci
          npm run build
          aws s3 sync dist/ s3://${{ secrets.S3_FRONTEND_BUCKET }} --delete
          aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} --paths "/*"

      - name: Deploy Backend to EC2 via AWS SSM RunCommand
        run: |
          aws ssm send-command \
            --instance-ids "${{ secrets.EC2_INSTANCE_ID }}" \
            --document-name "AWS-RunShellScript" \
            --parameters commands='[
              "aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin '${{ steps.login-ecr.outputs.registry }}'",
              "docker pull '${{ steps.login-ecr.outputs.registry }}'/'${{ secrets.ECR_REPOSITORY }}':'${{ github.sha }}'",
              "docker stop candidate || true && docker rm candidate || true",
              "docker run -d -p 5001:5000 --name candidate --env-file /home/ec2-user/.env '${{ steps.login-ecr.outputs.registry }}'/'${{ secrets.ECR_REPOSITORY }}':'${{ github.sha }}'",
              "echo \"Performing HealthCheck...\"",
              "SUCCESS=0",
              "for i in {1..24}; do if curl -s http://localhost:5001/health/ready | grep -q \"OK\"; then SUCCESS=1; break; fi; sleep 5; done",
              "if [ $SUCCESS -eq 1 ]; then echo \"HealthCheck Passed! Swapping containers...\"; docker stop rollback || true && docker rm rollback || true; docker rename main rollback || true; docker rename candidate main; docker stop main || true; docker run -d -p 5000:5000 --name main --env-file /home/ec2-user/.env '${{ steps.login-ecr.outputs.registry }}'/'${{ secrets.ECR_REPOSITORY }}':'${{ github.sha }}'; else echo \"HealthCheck Failed! Aborting candidate container...\"; docker stop candidate || true && docker rm candidate || true; exit 1; fi"
            ]'
```
