---
title: "System Monitoring & Alerting Setup (CloudWatch & SNS)"
date: 2026-07-27
weight: 11
chapter: false
pre: " <b> 5.4.11. </b> "
---

After deploying the system to Production, **Amazon CloudWatch** is configured to monitor EC2 resources and automatically trigger notifications to system administrators via **Amazon SNS (Simple Notification Service)** upon system degradation.

All monitoring resources in this step are provisioned in the **Singapore (`ap-southeast-1`)** region.

---

### 11.1. Create Amazon SNS Alert Topic

1. Open **AWS Management Console** -> **Amazon SNS** service -> **Topics** -> click **Create topic**.
2. **Type:** `Standard`.
3. **Name:** `LearnSphere-Alerts`.
4. Click **Create topic**.

---

### 11.2. Register Email Subscription & Confirm Subscription

1. On the `LearnSphere-Alerts` Topic detail page, click **Create subscription**:
   - **Protocol:** `Email`.
   - **Endpoint:** Enter administrator email address (e.g., `son.nguyenhong2410@hcmut.edu.vn`).
2. Click **Create subscription**.
3. Open your email inbox -> Open email from **AWS Notifications** -> Click **Confirm subscription**.
4. Confirm in SNS Console that the Subscription displays a valid ARN and is no longer `Pending confirmation`.

![SNS Topic channel dispatching LearnSphere operations alerts via email](/images/5-Workshop/5.4/5.4.11.2.png)
<p align="center"><i>Figure 5.4.11.2 — SNS Topic channel dispatching operational alert notifications to confirmed administrator emails.</i></p>

---

### 11.3. Create CloudWatch Alarm 1 — EC2 CPUUtilization > 80% for 10 Minutes

1. Open **CloudWatch Console** -> **Alarms** -> **All alarms** -> click **Create alarm**.
2. **Select metric:** Select `EC2` -> `Per-Instance Metrics` -> select `CPUUtilization` for EC2 Instance ID `i-008c48e6c120b2978`.
3. Configure parameters:
   - **Statistic:** `Average`
   - **Period:** `5 minutes`
   - **Threshold type:** `Static`
   - **Whenever CPUUtilization is:** `Greater than 80%`
   - **Datapoints to alarm:** `2 out of 2` (Triggers when average CPU exceeds 80% for two 5-minute periods = 10 minutes)
4. **Configure actions:** Send notification to SNS Topic `LearnSphere-Alerts`.
5. **Alarm name:** `LearnSphere-EC2-HighCPU`.
6. Click **Create alarm**.

![CloudWatch tracking EC2 CPU and alerting when exceeding 80% for 10 minutes](/images/5-Workshop/5.4/5.4.11.3.png)
<p align="center"><i>Figure 5.4.11.3 — CloudWatch Alarm monitoring EC2 CPU utilization and triggering alerts when exceeding 80%.</i></p>

---

### 11.4. Create CloudWatch Alarm 2 — EC2 StatusCheckFailed >= 1 in 60 Seconds

1. Click **Create alarm** -> Select `StatusCheckFailed` metric for EC2 Instance ID `i-008c48e6c120b2978`.
2. Configure parameters:
   - **Statistic:** `Maximum`
   - **Period:** `1 minute`
   - **Threshold type:** `Static`
   - **Whenever StatusCheckFailed is:** `Greater than or equal to 1`
   - **Datapoints to alarm:** `1 out of 1` (Triggers immediately within 60 seconds of server or hardware failure)
3. **Notification Action:** Send notification to SNS Topic `LearnSphere-Alerts`.
4. **Alarm name:** `LearnSphere-EC2-StatusCheckFailed`.
5. Click **Create alarm**.

![CloudWatch detecting EC2 status check failure and dispatching SNS notification](/images/5-Workshop/5.4/5.4.11.4.png)
<p align="center"><i>Figure 5.4.11.4 — CloudWatch Alarm detecting EC2 hardware/network failures and pushing urgent alerts via SNS.</i></p>

---

### 11.5. Verify Monitoring Operational Status

Navigate to **CloudWatch** -> **Alarms** -> **All alarms**, confirm both `LearnSphere-EC2-HighCPU` and `LearnSphere-EC2-StatusCheckFailed` display status **OK**. The monitoring infrastructure is ready to dispatch email alerts whenever system anomalies occur.
