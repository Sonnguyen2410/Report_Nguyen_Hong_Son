---
title: "Local Source Code & Dockerfile Preparation"
date: 2026-07-27
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

In this step, practitioners test the **LearnSphere** project source code locally, verify Frontend static compilation, and construct an optimized Multi-stage `Dockerfile` to containerize the Backend service.

---

### 1.1. Local Source Code Inspection & Testing

The LearnSphere codebase is structured as a Monorepo repository:

```text
LearnSphere/
├── LearnSphere_BE/       # Backend Node.js / Express.js REST API
├── LearnSphere_FE/       # Frontend React.js / Vite Single Page Application
├── .github/workflows/    # CI/CD automation workflows
└── docs/                 # Project documentation
```

#### Run Backend local test suite:

Open your Terminal / PowerShell window and execute the Backend test suite:

```powershell
cd LearnSphere_BE
npm ci
npm test
```

> **Requirement:** All backend unit tests must pass cleanly (`Pass`) with 0 failures (`Failed`).

#### Test Frontend production build:

Navigate to the `LearnSphere_FE` directory and run Vite's static bundler:

```powershell
cd ..\LearnSphere_FE
npm ci
npm run build
```

> **Requirement:** TypeScript compiler reports zero errors and Vite creates the output directory `LearnSphere_FE/dist`.

---

### 1.2. Write Multi-stage Dockerfile for Backend

To build a Production-Grade container image, create a `Dockerfile` inside `LearnSphere_BE/` using a **Multi-stage Build** approach on lightweight `node:24-alpine`:

```dockerfile
# Stage 1: Build Dependencies
FROM node:24-alpine AS builder

WORKDIR /app

# Copy package descriptors to leverage Docker layer caching
COPY package*.json ./

# Install all dependencies using npm ci
RUN npm ci

# Copy full application source code
COPY . .

# Stage 2: Production Runtime
FROM node:24-alpine AS runner

WORKDIR /app

# Create non-root system group and user (UID 1001) for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -u 1001 -S nodejs -G nodejs

# Copy app code and dependencies from builder stage
COPY --from=builder /app ./

# Transfer directory ownership to non-root user
USER nodejs

# Expose backend application port
EXPOSE 5000

# Container periodic healthcheck directive
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:5000/health/ready || exit 1

# Launch Node.js application server
CMD ["node", "src/server.js"]
```

---

### 1.3. Create `.dockerignore` File

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

### 1.4. Build & Verify Docker Image Locally

Build the local test Docker Image:

```powershell
cd LearnSphere_BE
docker build -t learnsphere-be:local .
```

Run the container locally:

```powershell
docker run -d -p 5000:5000 --name test-be --env-file .env.example learnsphere-be:local
```

Verify application readiness endpoint:

```powershell
curl http://localhost:5000/health/ready
```

**Expected Result:** Returns JSON response `{"status":"ready"}` with HTTP Status `200 OK`. Clean up local container afterwards:

```powershell
docker stop test-be && docker rm test-be
```

![Packaging LearnSphere Backend into a Docker image](/images/5-Workshop/5.4/5.4.1.png)
<p align="center"><i>Figure 5.4.1 — Local source code inspection and Backend Docker image containerization.</i></p>
