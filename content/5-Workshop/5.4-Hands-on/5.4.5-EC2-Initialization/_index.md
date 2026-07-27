---
title: "Launch & Configure EC2 Server"
date: 2026-07-27
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---

# 5.4.5. Launch & Configure EC2 Server (Amazon Linux 2023 & Security Group)

In this step, practitioners launch an **Amazon EC2** instance, assign a secure IAM Instance Profile, configure locked-down Security Groups, and configure 2.0GB of Swap virtual memory to prevent RAM overflow.

---

### 1. Launch EC2 Instance

1. Navigate to **Amazon EC2** service $\rightarrow$ click **Launch instance**.
2. **Name:** `LearnSphere-Backend-Server`.
3. **Application and OS Images (AMI):** Select **Amazon Linux 2023 AMI** 64-bit (x86).
4. **Instance type:** Select `t3.small` (2 vCPU, 2.0 GiB Memory).
5. **Key pair (login):** Select **Proceed without a key pair** (Server administration will be handled via SSM Session Manager without SSH keys).
6. **Network settings:**
   - **VPC:** Default VPC.
   - **Auto-assign public IP:** `Enable`.
   - **Security Group:** Create Security Group `learnsphere-backend-sg`.
   - **Inbound Rules:** Add Custom TCP rule, Port `5000`, Source: AWS Managed Prefix List `com.amazonaws.global.cloudfront.origin-facing` (or `0.0.0.0/0` temporarily during initial setup). Remove SSH port 22 rule.
7. **Advanced Details:**
   - **IAM instance profile:** Select Role `LearnSphereEc2Role`.
8. Click **Launch instance**.

---

### 2. Configure 2.0GB RAM Swap File on EC2

Connect to your EC2 instance via **AWS SSM Session Manager** (Click **Connect** $\rightarrow$ **Session Manager**), then execute the swap setup script:

```bash
# Create 2GB swap file
sudo dd if=/dev/zero of=/swapfile bs=1M count=2048

# Set strict 600 file permissions
sudo chmod 600 /swapfile

# Format swap space
sudo mkswap /swapfile

# Enable swap space
sudo swapon /swapfile

# Verify swap memory
free -h

# Register auto-mount in /etc/fstab
echo '/swapfile swap swap defaults 0 0' | sudo tee -a /etc/fstab
```

---

### 3. Install Docker Engine on EC2

Execute the Docker installation script on Amazon Linux 2023:

```bash
# Install Docker package
sudo dnf install -y docker

# Enable and start Docker service
sudo systemctl enable --now docker

# Add ec2-user and ssm-user to docker group
sudo usermod -aG docker ec2-user
sudo usermod -aG docker ssm-user
```
