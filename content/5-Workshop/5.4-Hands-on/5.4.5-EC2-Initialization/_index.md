---
title: "Launch & Configure EC2 Server"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---

In this step, practitioners launch an **Amazon EC2** instance, attach an IAM Instance Profile, install Docker Engine, and configure 2GB of virtual Swap RAM.

---

### 5.1. Launch EC2 Instance

1. Open **AWS Management Console** -> navigate to **Amazon EC2** -> click **Launch instance**.
2. **Name:** `LearnSphere-Backend-Server`.
3. Instance Specifications:

| Property | Value |
|---|---|
| AMI | Amazon Linux 2023 64-bit (x86) |
| Instance type | `t3.small` (2 vCPU, 2.0 GiB RAM) |
| Key pair (login) | **Proceed without a key pair** (100% SSH-less SSM management) |
| IAM instance profile | `LearnSphereEc2Role` |
| Instance ID | `i-008c48e6c120b2978` |

4. **Network settings:**
   - **Security Group:** Create Security Group `learnsphere-backend-sg`.
   - **Inbound Rules:** Add Custom TCP rule, Port `5000`, Source: AWS Managed Prefix List `com.amazonaws.global.cloudfront.origin-facing` (Only CloudFront traffic allowed to port 5000).
   - **SSH Port 22:** Remove SSH port 22 rule completely.
5. Click **Launch instance**.

![Launching EC2 instance for LearnSphere Backend](/images/5-Workshop/5.4/5.4.5.1.png)
<p align="center"><i>Figure 5.4.5.1 — Launching t3.small EC2 server with attached IAM Role and Security Group.</i></p>

---

### 5.2. Install Docker Engine & Configure 2GB Swap RAM

Connect to EC2 via **AWS SSM Session Manager** (Click **Connect** -> **Session Manager**), then run the setup script:

```bash
# Update OS packages and install Docker
sudo yum update -y
sudo yum install -y docker

# Start and enable Docker service
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user

# Verify Docker & AWS CLI versions
docker --version
aws --version
```

#### Configure 2.0GB Swap RAM to Prevent Out-Of-Memory Crashes:

Because the Node.js Backend handles PDF document parsing and OCR image processing, a 2.0GB Swap file is added:

```bash
# Allocate 2GB swap file
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Auto-mount on server reboot
echo '/swapfile swap swap defaults 0 0' | sudo tee -a /etc/fstab

# Check memory status
free -h
```

> **Result:** `free -h` shows ~1.9 GB Physical RAM and 2.0 GB Swap RAM active.

![Docker environment and Swap memory allocation on EC2](/images/5-Workshop/5.4/5.4.5.2.png)
<p align="center"><i>Figure 5.4.5.2 — Docker Engine runtime environment and 2.0GB Swap RAM memory configured successfully.</i></p>

---

### 5.3. Confirm IAM Instance Profile & SSM Connectivity

Verify in EC2 Console:
- Attached IAM Role is `LearnSphereEc2Role`.
- Server status displays `Managed` in AWS Systems Manager.
- Connection succeeds via **Session Manager** without opening SSH port 22.
