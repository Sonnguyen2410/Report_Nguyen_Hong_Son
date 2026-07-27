---
title: "Week 4 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---

## Topic: Hosting Static Frontend on Amazon S3, Configuring CloudFront CDN, and Setting up CI/CD with GitHub Actions

### 1. Week 4 Objectives
* Master packaging and hosting static Web Frontend applications (Static Single Page Application - SPA) on Amazon S3.
* Gain deep understanding of Amazon CloudFront Content Delivery Network (CDN) operation principles, HTTPS configuration, and React/Vite application distribution mechanisms.
* Build automated Continuous Integration and Continuous Deployment (CI/CD) pipelines using GitHub Actions for both Frontend and Backend.
* Complete main user interfaces using React 18, TypeScript, and Tailwind CSS directly connected to RESTful APIs on EC2.

---

### 2. Learning Content & Research (AWS & Core Tech)

#### A. Amazon S3 Static Website Hosting & Amazon CloudFront CDN
* **Static Frontend Hosting on Amazon S3:**
  * Understand Vite build mechanism: Compiling React/TypeScript source code into static assets including `index.html`, JavaScript (`.js`), CSS (`.css`), and assets stored in `dist/` directory.
  * Create new S3 Bucket named `learnsphere-fe-static` in Region `ap-southeast-1`.
  * Configure **Static Website Hosting** on S3: Designate `index.html` as both Index Document and Error Document (ensuring Client-side React Router routing functions correctly).
* **Amazon CloudFront CDN (Content Delivery Network):**
  * Edge Locations concept and how CloudFront accelerates page load speeds by caching content at nearest global stations to users.
  * **Create CloudFront Distribution:**
    * **Origin Domain:** Point to S3 Bucket `learnsphere-fe-static`.
    * **Viewer Protocol Policy:** Configure *Redirect HTTP to HTTPS* ensuring all web connections are securely encrypted.
  * **Configure Custom Error Responses in CloudFront:** Handle 403 Forbidden and 404 Not Found errors by returning `/index.html` with HTTP Status Code 200 OK (Mandatory for Single Page Applications like React/Vite to avoid white screen errors on sub-URL reloads).
  * **CloudFront Cache Invalidation:** How to issue cache invalidation commands (`/*`) whenever a new Frontend build is pushed to S3.

#### B. CI/CD Automation with GitHub Actions
* **GitHub Actions Concepts:**
  * Core components: Workflows, Events (`push`, `pull_request`), Jobs, Steps, Actions, and Runners (`ubuntu-latest`).
  * Declare and secure secret variables in GitHub Secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `EC2_HOST`, `EC2_SSH_KEY`).
* **Build Dual-path CI/CD Pipeline Deployment:**
  * **Path 1 (Deploy Frontend):**
    Checkout code → Setup Node.js → Install dependencies → Run `npm run build` → Sync `dist/` directory to S3 Bucket `learnsphere-fe-static` via `aws s3 sync` → Issue CloudFront Invalidation.
  * **Path 2 (Deploy Backend Docker):**
    Checkout code → Set up Docker Buildx → Log in to Amazon ECR → Build Docker Image and Tag → Push Image to ECR → SSH into EC2 server via SSH Action → Pull new Image (`docker pull`) and restart Container (`docker run`).

---

### 3. Implementation Tasks (Work Tasks)

* **Initialize & Configure AWS Infrastructure for Frontend (S3 + CloudFront):**
  * Create S3 Bucket `learnsphere-fe-static` on AWS Console, enable Static Website Hosting, and configure Bucket Policy allowing CloudFront OID/OAC (Origin Access Control) read access.
  * Initialize CloudFront Distribution pointing to S3 `learnsphere-fe-static`, enable HTTPS SSL encryption, and configure Custom Error Page routing 404 errors to `/index.html`.

* **Develop React Frontend Interface (`LearnSphere_FE`):**
  * Build Routing system using React Router DOM:
    * **Home Page (`HomePage.tsx`):** Introduction banner, featured course list.
    * **Auth Pages (`LoginPage.tsx`, `SignupPage.tsx`):** User authentication forms, JWT Token storage integration in LocalStorage/State.
    * **Course Catalog Page (`CourseCatalogPage.tsx`):** Search and filter courses by category.
    * **Course & Lesson Detail Pages (`CourseDetailPage.tsx`, `LessonDetailPage.tsx`):** Display lesson roadmaps, integrate video player, and provide resource download buttons via S3 Presigned URLs.
  * Configure Frontend environment variable `VITE_API_BASE_URL` connecting to Backend EC2 API.

* **Write GitHub Actions CI/CD Workflow (`.github/workflows/deploy.yml`):**
  Initialize `.github/workflows/deploy.yml` configuration file at root of project directory:
  ```yaml
  name: Deploy LearnSphere Full-Stack App
  on:
    push:
      branches: [ main ]
  jobs:
    deploy-frontend:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Setup Node.js
          uses: actions/setup-node@v3
          with:
            node-version: 18
        - name: Install & Build FE
          run: |
            cd LearnSphere_FE
            npm install
            npm run build
        - name: Deploy FE to S3
          uses: jakejarvis/s3-sync-action@master
          with:
            args: --delete
          env:
            AWS_S3_BUCKET: 'learnsphere-fe-static'
            AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
            AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            AWS_REGION: 'ap-southeast-1'
            SOURCE_DIR: 'LearnSphere_FE/dist'
        - name: Invalidate CloudFront
          run: |
            aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} --paths "/*"
    deploy-backend:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Configure AWS Credentials
          uses: aws-actions/configure-aws-credentials@v2
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: ap-southeast-1
        - name: Log in to Amazon ECR
          id: login-ecr
          uses: aws-actions/amazon-ecr-login@v1
        - name: Build, Tag & Push Docker Image to ECR
          run: |
            cd LearnSphere_BE
            docker build -t learnsphere-be:latest .
            docker tag learnsphere-be:latest ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
            docker push ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
        - name: Deploy to EC2 via SSH
          uses: appleboy/ssh-action@master
          with:
            host: ${{ secrets.EC2_HOST }}
            username: ubuntu
            key: ${{ secrets.EC2_SSH_KEY }}
            script: |
              aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin ${{ steps.login-ecr.outputs.registry }}
              docker stop backend-api || true
              docker rm backend-api || true
              docker pull ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
              docker run -d -p 5000:5000 --name backend-api --env-file /home/ubuntu/.env ${{ steps.login-ecr.outputs.registry }}/learnsphere-be:latest
  ```

---

### 4. Deliverables
* Complete React Frontend Web Interface with smooth, responsive layout across browsers.
* S3 Bucket `learnsphere-fe-static` and Amazon CloudFront Distribution successfully established, enabling global access via HTTPS protocol.
* GitHub Actions CI/CD Workflow running 100% successfully: Every `git push` to `main` branch automatically builds Frontend, syncs to S3/CloudFront, and packages Docker image deploying the new version to EC2.
