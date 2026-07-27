---
title: "Configure MongoDB Atlas Database"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 5.4.6. </b> "
---

# 5.4.6. Configure MongoDB Atlas Cloud Database

In this step, practitioners configure security settings for the **MongoDB Atlas** NoSQL database cluster, create a dedicated Database User, and whitelist network access from the EC2 server.

---

### 1. Create Production Database User

1. Log into **MongoDB Atlas Console** $\rightarrow$ select **Database Access** from the left menu.
2. Click **Add New Database User**.
3. **Authentication Method:** Select **Password**.
4. Enter **Username:** `learnsphere_prod`.
5. Generate a strong password and save it securely.
6. **Database User Privileges:** Select **Read and write to any database** (`readWriteAnyDatabase`).
7. Click **Add User**.

---

### 2. Configure Network Access IP Whitelist

1. Select **Network Access** $\rightarrow$ click **Add IP Address**.
2. Enter the **IPv4 Public IP** of your EC2 instance (or `0.0.0.0/0` temporarily for testing).
3. Click **Confirm**.

---

### 3. Retrieve SRV Connection String

1. Navigate to **Database** $\rightarrow$ click **Connect** on your Cluster.
2. Select **Drivers** (Node.js).
3. Copy the standard SRV connection string format:

```text
mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
```

4. Replace `<password>` with the password created in Step 1. This string will populate the `MONGODB_URI` environment variable.
