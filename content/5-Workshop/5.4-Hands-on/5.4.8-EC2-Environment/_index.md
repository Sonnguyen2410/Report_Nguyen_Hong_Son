---
title: "Configure Backend Environment on EC2"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 5.4.8. </b> "
---

In this step, practitioners create a Production environment variable file on the EC2 server at `/home/ec2-user/.env`, set strict file permissions, and verify IAM Instance Profile authentication.

---

### 8.1. Initialize Production `.env` File on EC2

Connect to EC2 via **AWS SSM Session Manager** and create the `.env` file:

```bash
sudo touch /home/ec2-user/.env
sudo chmod 600 /home/ec2-user/.env
sudo vi /home/ec2-user/.env
```

---

### 8.2. Declare Production Environment Variables

Populate the `/home/ec2-user/.env` file:

```dotenv
PORT=5000
NODE_ENV=production
TRUST_PROXY=true

MONGODB_URI=mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
MONGODB_REQUIRE_TRANSACTIONS=true

JWT_SECRET=c84ac761c5224c53b96ad34fc94a8194c84ac761c5224c53b96ad34fc94a8194
FRONTEND_URL=https://d2onzy56n3iw1w.cloudfront.net

AWS_REGION=ap-southeast-1
AWS_S3_BUCKET=learnsphere-media-575620421319

AI_PROVIDER=bedrock
BEDROCK_REGION=ap-southeast-1
BEDROCK_MODEL_ID=apac.amazon.nova-lite-v1:0
GROQ_API_KEY=gsk_learnsphere_ai_inference_key_sample
EOF
```

> **Security Requirement:** **Do NOT declare `AWS_ACCESS_KEY_ID` or `AWS_SECRET_ACCESS_KEY`** in `.env` because the Backend automatically inherits IAM credentials from `LearnSphereEc2Role` attached to the EC2 Instance Profile.

---

### 8.3. Validate Variable Declaration Without Exposing Values

Run an `awk` script to check variable presence without printing sensitive credentials to terminal logs:

```bash
sudo awk -F= '
  /^[A-Z0-9_]+=/ {
    if (length($2) > 0) print "OK: " $1;
    else print "MISSING: " $1
  }
' /home/ec2-user/.env
```

> **Expected Result:** All required variables display `OK: VARIABLE_NAME`.

![Verifying production environment variables on EC2](/images/5-Workshop/5.4/5.4.8.3.png)
<p align="center"><i>Figure 5.4.8.3 — Verifying production environment variable declarations on EC2 via awk script.</i></p>

---

### 8.4. Verify IAM Instance Profile & S3 Access from EC2

Execute AWS CLI identity verification commands from the EC2 terminal:

```bash
aws sts get-caller-identity
aws s3api head-bucket --bucket learnsphere-media-575620421319
```

**Expected Output:**
```text
{
    "UserId": "AROAXXXXXXXXXXXXX:i-008c48e6c120b2978",
    "Account": "575620421319",
    "Arn": "arn:aws:sts::575620421319:assumed-role/LearnSphereEc2Role/i-008c48e6c120b2978"
}
```

> This proves EC2 is receiving short-lived temporary credentials from the `LearnSphereEc2Role` IAM Role via IMDSv2.

![EC2 receiving temporary credentials from IAM Role](/images/5-Workshop/5.4/5.4.8.4.png)
<p align="center"><i>Figure 5.4.8.4 — Verifying EC2 receiving temporary credentials safely from IAM Role via IMDSv2.</i></p>
