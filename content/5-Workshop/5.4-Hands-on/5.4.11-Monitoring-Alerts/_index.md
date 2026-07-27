---
title: "System Monitoring & Alerting Setup (CloudWatch & SNS)"
date: 2026-07-27
weight: 11
chapter: false
pre: " <b> 5.4.11. </b> "
---

# 5.4.11. System Monitoring & Alerting Setup (CloudWatch & SNS)

In this step, practitioners configure centralized log aggregation using **Amazon CloudWatch Logs**, set up **CloudWatch Alarms**, and integrate **Amazon SNS Topics** to send automatic email alerts during server degradation or high CPU utilization.

---

### 1. Create Amazon SNS Topic & Confirm Email Subscription

1. Access **Amazon Simple Notification Service (SNS)** $\rightarrow$ select **Topics** $\rightarrow$ click **Create topic**.
2. **Type:** `Standard`.
3. **Name:** `LearnSphere-Alerts`.
4. Click **Create topic**.
5. In Topic details $\rightarrow$ click **Create subscription**:
   - **Protocol:** Select `Email`.
   - **Endpoint:** Enter your email address (e.g., `son.nguyenhong2410@hcmut.edu.vn`).
   - Click **Create subscription**.
6. Open your personal Email inbox $\rightarrow$ Open email from AWS Notifications $\rightarrow$ Click **Confirm subscription**.

---

### 2. Configure Centralized CloudWatch Log Group

1. Access **Amazon CloudWatch** service $\rightarrow$ **Logs** $\rightarrow$ select **Log groups**.
2. Click **Create log group**.
3. **Log group name:** `/learnsphere/backend`.
4. **Retention setting:** `30 days`.
5. Click **Create**.

---

### 3. Create CloudWatch Alarm 1 (EC2 High CPU Utilization)

1. In CloudWatch Console $\rightarrow$ **Alarms** $\rightarrow$ click **Create alarm**.
2. Select metric $\rightarrow$ **EC2** $\rightarrow$ **Per-Instance Metrics** $\rightarrow$ select `CPUUtilization` metric for EC2 Backend instance.
3. **Statistic:** `Average`, **Period:** `5 minutes`.
4. **Threshold type:** `Static` $\rightarrow$ **Whenever CPUUtilization is...** `Greater than 80%`.
5. **Datapoints to alarm:** `2 out of 2` (Triggers when CPU exceeds 80% for 10 minutes).
6. **Configure actions:** Select SNS Topic `LearnSphere-Alerts`.
7. **Alarm name:** `LearnSphere-EC2-HighCPU`.
8. Click **Create alarm**.

---

### 4. Create CloudWatch Alarm 2 (EC2 Status Check Failed)

1. Click **Create alarm** $\rightarrow$ Select `StatusCheckFailed` metric.
2. **Statistic:** `Maximum`, **Period:** `1 minute`.
3. **Condition:** `Greater than or equal to 1`.
4. **Datapoints to alarm:** `1 out of 1` (Triggers immediately within 60s of server failure).
5. **Notification Action:** Send notification to SNS Topic `LearnSphere-Alerts`.
6. **Alarm name:** `LearnSphere-EC2-StatusCheckFailed`.
7. Click **Create alarm**.
