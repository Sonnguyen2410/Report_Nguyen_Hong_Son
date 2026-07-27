---
title: "Local Source Code & Dockerfile Preparation"
date: 2026-07-27
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

# 5.4.1. Local Source Code & Dockerfile Preparation

In this step, practitioners prepare the **LearnSphere** project source code on their local machine, verify the Monorepo directory structure, and craft an optimized multi-stage `Dockerfile` to containerize the Backend service.

---

### 1. Verify Monorepo Source Code Structure

Open your Terminal and navigate to the LearnSphere project directory:

```bash
cd LearnSphere
ls -la
```

The directory structure must include:
- `LearnSphere_BE/`: Backend Node.js/Express application source code.
- `LearnSphere_FE/`: Frontend React/Vite application source code.
- `.github/workflows/`: CI/CD automation workflow configurations.

---

### 2. Craft Optimized Backend Dockerfile (`LearnSphere_BE`)

Create a `Dockerfile` inside the `LearnSphere_BE/` directory utilizing multi-stage builds based on lightweight Linux Alpine:

```dockerfile
# Stage 1: Build dependencies & production ready image
FROM node:24-alpine AS builder

WORKDIR /app

# Copy package descriptors to leverage Docker layer caching
COPY package*.json ./

# Install all dependencies including devDependencies
RUN npm ci

# Copy full application code
COPY . .

# Stage 2: Production Runtime Image
FROM node:24-alpine AS runner

WORKDIR /app

# Create non-root system group and user for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -u 1001 -S nodejs -G nodejs

# Copy app artifacts and dependencies from builder stage
COPY --from=builder /app ./

# Transfer directory ownership to non-root user
USER nodejs

# Expose backend application port
EXPOSE 5000

# Periodic container healthcheck directive
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:5000/health/ready || exit 1

# Launch application
CMD ["node", "src/server.js"]
```

---

### 3. Create `.dockerignore` File

Create a `.dockerignore` file in `LearnSphere_BE/` to exclude unwanted build artifacts:

```text
node_modules
.env
.env.*
.git
.gitignore
README.md
dist
logs
```

---

### 4. Build & Test Container Locally

Build the local test Docker Image:

```bash
cd LearnSphere_BE
docker build -t learnsphere-be:local .
```

Run the container on port 5000:

```bash
docker run -d -p 5000:5000 --name test-be --env-file .env.example learnsphere-be:local
```

Verify container health:

```bash
curl http://localhost:5000/health/ready
```

**Expected Result:** Terminal returns HTTP `200 OK` status. After verification, clean up local test containers:

```bash
docker stop test-be && docker rm test-be
```
