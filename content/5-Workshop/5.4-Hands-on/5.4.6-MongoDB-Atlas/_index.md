---
title: "Configure MongoDB Atlas Database"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 5.4.6. </b> "
---

The LearnSphere system continues to use **MongoDB Atlas** as its Production database rather than migrating to Amazon RDS/DynamoDB due to the following technical reasons:
- Backend code is natively constructed using Mongoose ODM.
- User, course, and quiz data models strictly fit MongoDB NoSQL Document structures.
- Retaining the database provider minimizes code modification risks during migration.

---

### 6.1. Create Production Database User

1. Log into **MongoDB Atlas Console** -> select **Database Access** from the left menu.
2. Click **Add New Database User**.
3. **Authentication Method:** Select **Password**.
4. **Username:** `learnsphere_prod`.
5. Generate a strong password (>32 characters, kept secure and never committed to Git).
6. **Database User Privileges:** Select **Read and write to any database** (`readWriteAnyDatabase`).
7. Click **Add User**.

---

### 6.2. Configure Network Access IP Access List

1. Select **Network Access** -> click **Add IP Address**.
2. Enter the **IPv4 Public IP** of the EC2 Backend instance (`i-008c48e6c120b2978`).
3. Click **Confirm** to save IP Access List rules.

![MongoDB Atlas cluster utilized as production database](/images/5-Workshop/5.4/5.4.6.png)
<p align="center"><i>Figure 5.4.6 — MongoDB Atlas Cluster database management and EC2 IP Access List configuration.</i></p>

---

### 6.3. Retrieve SRV Connection String & Health Check Integration

1. Navigate to **Database** -> click **Connect** on your Cluster.
2. Select **Drivers** (Node.js).
3. Copy the standard SRV Connection String format:

```text
mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
```

4. This connection string populates the `MONGODB_URI` environment variable on EC2 in **Step 8**.
5. The Backend `/health/ready` check endpoint only returns `ready` when active connectivity to MongoDB Atlas is verified.
