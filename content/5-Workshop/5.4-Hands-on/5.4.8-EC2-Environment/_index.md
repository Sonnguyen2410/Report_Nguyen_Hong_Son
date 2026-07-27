---
title: "Configure Backend Environment on EC2"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 5.4.8. </b> "
---

# 5.4.8. Configure Backend Environment on EC2 Server

In this step, practitioners create a Production environment variable configuration file (`/home/ec2-user/.env`) on the EC2 instance for the Backend container to read upon launching.

---

### 1. Create Production `.env` File on EC2

Connect to your EC2 instance via **AWS SSM Session Manager** and create the `.env` file:

```bash
cat << 'EOF' > /home/ec2-user/.env
PORT=5000
NODE_ENV=production
TRUST_PROXY=true
MONGODB_URI=mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
JWT_SECRET=c84ac761c5224c53b96ad34fc94a8194c84ac761c5224c53b96ad34fc94a8194
FRONTEND_URL=https://d2onzy56n3iw1w.cloudfront.net
AWS_REGION=ap-southeast-1
AWS_S3_BUCKET=learnsphere-media-575620421319
GROQ_API_KEY=gsk_learnsphere_ai_inference_key_sample
EOF
```

---

### 2. Configure File Permissions

Restrict file permissions so only system processes can access sensitive environment parameters:

```bash
# Set strict permissions
chmod 600 /home/ec2-user/.env

# Grant read access for SSM execution commands
sudo chmod 644 /home/ec2-user/.env
```

---

### 3. Verify ECR & Docker Daemon Status

Execute an ECR login test to confirm EC2 IAM Instance Profile permissions:

```bash
# ECR Login
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 575620421319.dkr.ecr.ap-southeast-1.amazonaws.com

# Check running Docker containers
docker ps
```

**Expected Result:** Docker Daemon is running and ready to receive container deployment commands from GitHub Actions.
