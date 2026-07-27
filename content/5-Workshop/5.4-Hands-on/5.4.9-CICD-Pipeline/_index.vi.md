---
title: "Cấu hình GitHub Secrets & Pipeline CI/CD"
date: 2026-07-27
weight: 9
chapter: false
pre: " <b> 5.4.9. </b> "
---

Trong bước này, người thực hiện sẽ khai báo các biến bảo mật Repository Secrets trên GitHub và xây dựng tệp quy trình tự động hóa `.github/workflows/deploy.yml` với cơ chế kiểm thử Candidate Container và tự động hoàn tác (Auto-Rollback).

---

### 9.1. Khai báo GitHub Actions Secrets

Mở GitHub Repository -> **Settings** -> **Secrets and variables** -> **Actions** -> chọn **New repository secret**:

| Secret | Giá trị / Nội dung |
|---|---|
| `AWS_GITHUB_ROLE_ARN` | `arn:aws:iam::575620421319:role/LearnSphereGitHubDeployRole` |
| `EC2_INSTANCE_ID` | `i-008c48e6c120b2978` |
| `VITE_API_BASE_URL` | `/api` |
| `S3_FE_BUCKET` | `learnsphere-fe-575620421319` |
| `CLOUDFRONT_FE_DISTRIBUTION_ID` | `EQRDOBSCG5MC8` |

> **Không sử dụng các Secret tĩnh cũ:** Không khai báo `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` hay `EC2_SSH_KEY` vì quy trình triển khai sử dụng 100% xác thực **OIDC** và quản trị qua **AWS Systems Manager (SSM)**.

![Các biến cấu hình được sử dụng bởi pipeline CI/CD](/images/5-Workshop/5.4/5.4.9.1.png)
<p align="center"><i>Hình 5.4.9.1 — Khai báo các Repository Secrets phục vụ quy trình triển khai CI/CD.</i></p>

---

### 9.2. Xây dựng Quy trình CI/CD Workflow (`.github/workflows/deploy.yml`)

Tạo file `.github/workflows/deploy.yml` với nội dung tự động hóa 2 Job: **deploy-backend** và **deploy-frontend**:

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

![Quy trình CI/CD tự động của LearnSphere](/images/5-Workshop/5.4/5.4.9.2.png)
<p align="center"><i>Hình 5.4.9.2 — Sơ đồ luồng xử lý tự động của file quy trình deploy.yml.</i></p>

---

### 9.3. Kích hoạt CI/CD Pipeline & Xử lý Lỗi OIDC

Thực thi lệnh push từ local để kích hoạt tự động pipeline:

```powershell
git add .
git commit -m "feat: deploy LearnSphere to AWS"
git push origin main
```

![Pipeline triển khai Backend và Frontend hoàn tất thành công](/images/5-Workshop/5.4/5.4.9.3.png)
<p align="center"><i>Hình 5.4.9.3 — Pipeline GitHub Actions triển khai hoàn tất 2 job Backend và Frontend.</i></p>

#### Xử lý Lỗi OIDC ở Lần Chạy Đầu:
Nếu gặp thông báo lỗi `Could not assume role with OIDC: Not authorized to perform sts:AssumeRoleWithWebIdentity`, hãy kiểm tra lại cấu hình **Trust Policy** của `LearnSphereGitHubDeployRole` đảm bảo khớp chính xác tên `repo:username/repository:ref:refs/heads/main`. Bấm nút **Re-run jobs** trên GitHub Actions để chạy lại pipeline thành công.
