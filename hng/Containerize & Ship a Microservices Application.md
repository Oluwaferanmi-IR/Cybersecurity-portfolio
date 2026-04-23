## Containerize & Ship a Microservices Application

## Project question

In Stage 1, you built and deployed a personal API on a live server using Nginx. In Stage 2, you will take a provided multi-service application and make it production-ready through containerization and a full CI/CD pipeline.
As a DevOps engineer, you will rarely be writing the application — but you will always be responsible for how it runs, how it gets there, and whether it survives a restart. This task puts that responsibility squarely on you. The application is provided for you. It has bugs. Finding them is part of the task.

The Application

You are given a job processing system made up of three services:

- A frontend (Node.js) where users submit and track jobs
- An API (Python/FastAPI) that creates jobs and serves status updates
- A worker (Python) that picks up and processes jobs from a queue
- A Redis instance shared between the API and worker

The source code is intentionally shipped with bugs—misconfigurations, bad practices, and missing production requirements are present throughout. You are not told where they are or how many there are. Finding them, fixing them, and documenting every single one is a graded part of this assessment.

Starter repo:https://github.com/chukwukelu2023/hng14-stage2-devops

What You Must Do

1. Fix the Application Read through all the source files carefully before touching any infrastructure. Some things will not work correctly as written. Others will appear to work locally but fail inside containers. Document every issue you find in a FIXES.md file — state the file, the line, what the problem was, and what you changed. Vague entries will not receive marks.

2. Containerize It Write a production quality Dockerfile for each of the three services:

- Use multi-stage builds where appropriate—your final image should not contain build tools or dev dependencies
- All services must run as a non-root user
- Each Dockerfile must include a working HEALTHCHECK instruction
- No secrets, .env files, or credentials may be copied into any image

  Write a docker-compose.yml that brings the full stack up:

- All services must communicate over a named internal network
- Redis must not be exposed on the host machine
- Services must only start after their dependencies are confirmed healthy—not just started
- All configuration must come from environment variables; nothing hardcoded in the Compose file
- Include CPU and memory limits for every service

3. Build the Pipeline Implement a CI/CD pipeline using GitHub Actions on ubuntu-latest (free tier — no self-hosted runners, no paid services). Your pipeline must run the following stages in strict order:

- lint → test → build → security scan → integration test → deploy
- Lint: Python (flake8), JavaScript (eslint), and all Dockerfiles (hadolint)
- Test: At least 3 unit tests for the API using pytest with Redis mocked. Generate and upload a coverage report as a pipeline artifact
- Build: Build all three images, tag each with the git SHA and latest, push to a local Docker registry running as a service container within the job
- Security scan: Scan all images with Trivy, fail the pipeline on any CRITICAL severity finding, upload results as a SARIF artifact
- Integration test: Bring the full stack up inside the runner, submit a job through the frontend, poll until it completes, assert the final status is correct, tear the stack down cleanly regardless of outcome
- Deploy: Runs on pushes to main only. Must perform a scripted rolling update — the new container must pass its health check before the old one is stopped. If the health check does not pass within 60 seconds, abort and leave the old container running
- A failure in any stage must prevent all subsequent stages from running.

4. Document It

- A README.md that explains how to bring the entire stack up on a clean machine from scratch—list prerequisites, all commands, and what a successful startup looks like
- A FIXES.md documenting every bug found—file, line number, what it was, and how you fixed it
- A .env.example committed with placeholder values for every required variable

## Objective
To debug, containerize, and orchestrate a distributed application using Docker and Docker Compose, while building a CI/CD pipeline that enforces testing, security scanning, and zero-downtime deployments.

## step-by-step guide
1. download and install docker `https://www.docker.com/products/docker-desktop/`

   - if you get an error saying the program need elevated permkssion, open cmd
   - take ownership of the folder `takeown /f "C:\ProgramData\DockerDesktop" /r /d y`
   - Guve yourself full permission `icacls "C:\ProgramData\DockerDesktop" /grant Administrators:F /t`
   -  Delete the folder completely `rmdir /s /q "C:\ProgramData\DockerDesktop"`
   -  go to Win + R type `c:\programdata`
   -  look for dockerdestop
   -  delete the folder
   -  run the docker installer again and follow the default prompt
     <img width="703" height="485" alt="Screenshot 2026-04-22 101107" src="https://github.com/user-attachments/assets/d90c9c25-762a-4438-a4af-69d708898461" />

2. Install WSL (window subsystem for linux)

   - open powershell as administrator
   - install wsl `wsl --install`
   - if it says already exists, just download the ubuntu distro `wsl --install -d ubuntu`
   - create username
   - create email

3. Open the ubuntu and update it `sudo apt update && upgrade`
4. check if the following are properly installed by checking their versions and install any that is not installed

   - `docker --version`
   - `docker compose version`
   - `node --version`
   - `python3 --version`
   - `git --version`

   <img width="353" height="185" alt="Screenshot 2026-04-22 125622" src="https://github.com/user-attachments/assets/fe23b610-34a1-41b4-9eee-18cb55f1ee8f" />

5. Fork the github repository we are supposed to check for bugs

   - Go to https://github.com/chukwukelu2023/hng14-stage2-devops
   - Click "Fork" at the top right
   - Click "Create fork"
  <img width="1098" height="483" alt="Screenshot 2026-04-22 104306" src="https://github.com/user-attachments/assets/a6430155-79d7-4d5a-b694-fd0bd90b539d" />

6. configure your github

    - `git config --global user.name "Oluwaferanmi-IR` (github username)
    - `git config --global user.email olajide****.com` (github email)

7. Clone the repository on your laptop

   - cloning to desktop `cd /mnt/c/users/user/desktop`
   - clone the forked repo `git clone https://github.com/oluwaferanmi-IR/hng14-stage2-devops`
   - go into the folder `cd hn14-stage2-devops`
  <img width="981" height="153" alt="Screenshot 2026-04-22 130812" src="https://github.com/user-attachments/assets/68970db9-5cd4-4608-9d9f-bcdb8a304afc" />

8. Open code in vs code `code .`
   <img width="1123" height="530" alt="image" src="https://github.com/user-attachments/assets/d8813704-86df-4677-82a9-d1232034bb70" />

9. Create working branch `git checkout -b feat/containerize-microservices`
10. verify `git branch` it should show `* feat/containerize-microservices
  main`
11. Check the codes and look for bug and fix them (the following fixes are for the bugs found)

    - Create .gitignore In VS Code, create a new file called `.gitignore` in the root folder. paste this
  ```
 # Environment files - NEVER commit these
.env
*.env
api/.env
worker/.env
frontend/.env

# Python
__pycache__/
*.pyc
*.pyo
.pytest_cache/
.coverage
htmlcov/

# Node
node_modules/

# Logs
*.log
```
12.  Fix api/main.py: Open api/main.py in VS Code. Delete everything and replace with:
```
from fastapi import FastAPI, HTTPException
import redis
import uuid
import os

app = FastAPI()

r = redis.Redis(
    host=os.environ.get("REDIS_HOST", "redis"),
    port=int(os.environ.get("REDIS_PORT", 6379)),
    password=os.environ.get("REDIS_PASSWORD", None)
)


@app.get("/health")
def health():
    return {"status": "ok"}


@app.post("/jobs")
def create_job():
    job_id = str(uuid.uuid4())
    r.lpush("job", job_id)
    r.hset(f"job:{job_id}", "status", "queued")
    return {"job_id": job_id}


@app.get("/jobs/{job_id}")
def get_job(job_id: str):
    status = r.hget(f"job:{job_id}", "status")
    if not status:
        raise HTTPException(status_code=404, detail="Job not found")
    return {"job_id": job_id, "status": status.decode()}
```
13. Fix worker/worker.py: Open worker/worker.py. Delete everything and replace with:
```
import redis
import time
import os

r = redis.Redis(
    host=os.environ.get("REDIS_HOST", "redis"),
    port=int(os.environ.get("REDIS_PORT", 6379)),
    password=os.environ.get("REDIS_PASSWORD", None)
)


def process_job(job_id):
    try:
        print(f"Processing job {job_id}")
        time.sleep(2)
        r.hset(f"job:{job_id}", "status", "completed")
        print(f"Done: {job_id}")
    except Exception as e:
        print(f"Error processing job {job_id}: {e}")
        r.hset(f"job:{job_id}", "status", "failed")


while True:
    try:
        job = r.brpop("job", timeout=5)
        if job:
            _, job_id = job
            process_job(job_id.decode())
    except Exception as e:
        print(f"Worker error: {e}")
        time.sleep(5)
```
14. Fix frontend/app.js: open frontend/app.js in VS Code. Delete everything and replace with:
```
const express = require('express');
const axios = require('axios');
const path = require('path');

const app = express();
const API_URL = process.env.API_URL || 'http://api:8000';
const PORT = process.env.PORT || 3000;

app.use(express.json());
app.use(express.static(path.join(__dirname, 'views')));

app.get('/health', (req, res) => {
  res.status(200).json({ status: 'ok' });
});

app.post('/submit', async (req, res) => {
  try {
    const response = await axios.post(`${API_URL}/jobs`);
    res.json(response.data);
  } catch (err) {
    console.error(err.message);
    res.status(500).json({ error: 'something went wrong' });
  }
});

app.get('/status/:id', async (req, res) => {
  try {
    const response = await axios.get(
      `${API_URL}/jobs/${req.params.id}`
    );
    res.json(response.data);
  } catch (err) {
    console.error(err.message);
    res.status(500).json({ error: 'something went wrong' });
  }
});

app.listen(PORT, () => {
  console.log(`Frontend running on port ${PORT}`);
});
```
15. Fix frontend/package.json: Open frontend/package.json. Delete everything and replace with:
```
{
  "name": "frontend",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "lint": "eslint app.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "axios": "^1.4.0"
  },
  "devDependencies": {
    "eslint": "^8.0.0"
  }
}
```
16. Delete api/.env: in WSL run `rm api/.env`
17. Create .env.example: In VS Code create a new file called .env.example in the root folder:
```
# Redis Configuration
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=your-redis-password-here

# API Configuration
API_HOST=0.0.0.0
API_PORT=8000

# Frontend Configuration
PORT=3000
API_URL=http://api:8000

# Environment
APP_ENV=production
```
18. Create .env for Local Testing. In WSL: run `nano .env` then paste
```
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=localpassword
API_HOST=0.0.0.0
API_PORT=8000
PORT=3000
API_URL=http://api:8000
APP_ENV=development
```
19. Create FIXES.md. In VS Code create a new file called FIXES.md in the root folder and paste all the fixes made
20. Write API Dockerfile. In VS Code create a new file called Dockerfile inside the api/ folder:
```
# Stage 1 - Build
FROM python:3.11-slim AS builder

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir --user -r requirements.txt


# Stage 2 - Production
FROM python:3.11-slim AS production

WORKDIR /app

RUN groupadd -r appuser && \
    useradd -r -g appuser appuser

COPY --from=builder /root/.local /home/appuser/.local

COPY main.py .

RUN chown -R appuser:appuser /app

USER appuser

ENV PATH=/home/appuser/.local/bin:$PATH

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=10s \
    --start-period=10s --retries=3 \
    CMD python -c \
    "import urllib.request; \
    urllib.request.urlopen('http://localhost:8000/health')" \
    || exit 1

CMD ["uvicorn", "main:app", \
    "--host", "0.0.0.0", \
    "--port", "8000"]
```
21. Write Worker Dockerfile. Create Dockerfile inside worker/ folder:
```
# Stage 1 - Build
FROM python:3.11-slim AS builder

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir --user -r requirements.txt


# Stage 2 - Production
FROM python:3.11-slim AS production

WORKDIR /app

RUN groupadd -r appuser && \
    useradd -r -g appuser appuser

COPY --from=builder /root/.local /home/appuser/.local

COPY worker.py .

RUN chown -R appuser:appuser /app

USER appuser

ENV PATH=/home/appuser/.local/bin:$PATH

HEALTHCHECK --interval=30s --timeout=10s \
    --start-period=15s --retries=3 \
    CMD pgrep -f "python worker.py" || exit 1

CMD ["python", "worker.py"]
```
22. Write Frontend Dockerfile. Create Dockerfile inside frontend/ folder:
```
# Stage 1 - Build
FROM node:20-slim AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci --only=production


# Stage 2 - Production
FROM node:20-slim AS production

WORKDIR /app

RUN groupadd -r appuser && \
    useradd -r -g appuser appuser

COPY --from=builder /app/node_modules ./node_modules

COPY app.js .
COPY views/ ./views/

RUN chown -R appuser:appuser /app

USER appuser

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=10s \
    --start-period=10s --retries=3 \
    CMD node -e \
    "require('http').get('http://localhost:3000/health', \
    (r) => r.statusCode === 200 ? \
    process.exit(0) : process.exit(1))" \
    || exit 1

CMD ["node", "app.js"]
```
23.  Write docker-compose.yml. Create docker-compose.yml in the root folder: 
```
version: '3.8'

services:

  redis:
    image: redis:7-alpine
    container_name: redis
    restart: unless-stopped
    networks:
      - internal
    command: redis-server --requirepass ${REDIS_PASSWORD}
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD}", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 5s
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 128M

  api:
    build:
      context: ./api
      dockerfile: Dockerfile
    container_name: api
    restart: unless-stopped
    networks:
      - internal
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
      - REDIS_PASSWORD=${REDIS_PASSWORD}
    depends_on:
      redis:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "python", "-c",
        "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 15s
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 256M

  worker:
    build:
      context: ./worker
      dockerfile: Dockerfile
    container_name: worker
    restart: unless-stopped
    networks:
      - internal
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
      - REDIS_PASSWORD=${REDIS_PASSWORD}
    depends_on:
      redis:
        condition: service_healthy
      api:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "pgrep", "-f", "python worker.py"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 20s
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 256M

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: frontend
    restart: unless-stopped
    networks:
      - internal
    ports:
      - "3000:3000"
    environment:
      - API_URL=${API_URL}
      - PORT=${PORT}
    depends_on:
      api:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "node", "-e",
        "require('http').get('http://localhost:3000/health', (r) => r.statusCode === 200 ? process.exit(0) : process.exit(1))"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 15s
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 128M

networks:
  internal:
    driver: bridge
```
24. Test Locally. In WSL:

    - sudo service docker start
    - cd /mnt/c/users/user/desktop/hng14-stage2-devops
    - docker compose up --build
    - Wait for all services to start. Then open your browser: `http://localhost:3000`

<img width="1123" height="284" alt="Screenshot 2026-04-23 012243" src="https://github.com/user-attachments/assets/3727ea43-f8c6-41e2-9807-675f95ed69bd" />

25. Writet unit tests

    - install test dependencies

      - in a second wsl `cd /mnt/c/users/user/desktop`
      - `pip3 install pytest pytest-cov httpx fastapi`
      - if pip3 is not installed `sudo apt install pip3 -y`

    - create test file `nano api/test_main.py` write this into the file
```
import pytest
from unittest.mock import patch, MagicMock
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)


# ─────────────────────────────────────────
# Mock Redis so tests don't need real Redis
# ─────────────────────────────────────────
@pytest.fixture
def mock_redis():
    with patch('main.r') as mock:
        yield mock


# ─────────────────────────────────────────
# TEST 1 - Health endpoint
# ─────────────────────────────────────────
def test_health_returns_200():
    """Health endpoint must return 200"""
    response = client.get("/health")
    assert response.status_code == 200


# ─────────────────────────────────────────
# TEST 2 - Health endpoint returns correct body
# ─────────────────────────────────────────
def test_health_returns_status_ok():
    """Health endpoint must return status ok"""
    response = client.get("/health")
    data = response.json()
    assert data["status"] == "ok"


# ─────────────────────────────────────────
# TEST 3 - Create job returns job_id
# ─────────────────────────────────────────
def test_create_job_returns_job_id(mock_redis):
    """Creating a job must return a job_id"""
    mock_redis.lpush = MagicMock(return_value=1)
    mock_redis.hset = MagicMock(return_value=1)
    response = client.post("/jobs")
    assert response.status_code == 200
    data = response.json()
    assert "job_id" in data


# ─────────────────────────────────────────
# TEST 4 - Create job returns 200
# ─────────────────────────────────────────
def test_create_job_returns_200(mock_redis):
    """Creating a job must return HTTP 200"""
    mock_redis.lpush = MagicMock(return_value=1)
    mock_redis.hset = MagicMock(return_value=1)
    response = client.post("/jobs")
    assert response.status_code == 200


# ─────────────────────────────────────────
# TEST 5 - Get job not found returns 404
# ─────────────────────────────────────────
def test_get_job_not_found_returns_404(mock_redis):
    """Getting non-existent job must return 404"""
    mock_redis.hget = MagicMock(return_value=None)
    response = client.get("/jobs/nonexistent-id")
    assert response.status_code == 404


# ─────────────────────────────────────────
# TEST 6 - Get existing job returns status
# ─────────────────────────────────────────
def test_get_existing_job_returns_status(mock_redis):
    """Getting existing job must return job_id and status"""
    mock_redis.hget = MagicMock(return_value=b"queued")
    response = client.get("/jobs/some-job-id")
    assert response.status_code == 200
    data = response.json()
    assert "job_id" in data
    assert "status" in data
    assert data["status"] == "queued"
```
26. run test locally

    - `cd api`
    - pip3 install -r requirements.txt`
    - `pytest test_main.py -v` you should see :
<img width="1082" height="302" alt="Screenshot 2026-04-23 013437" src="https://github.com/user-attachments/assets/3570a89d-df32-4b75-af78-bba1bd120a6c" />

27. go back to root `cd ..`
28. Write the github actions pipeline

    - create a workflow folder `mkdir -p .github/workflow`
    - create pipelinefile : `nano .github/workflows/pipeline.yml`
```
name: CI/CD Pipeline

on:
  push:
    branches: [ main, feat/containerize-microservices ]
  pull_request:
    branches: [ main ]

jobs:

  # ─────────────────────────────────────────
  # STAGE 1 - LINT
  # ─────────────────────────────────────────
  lint:
    name: Lint
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install flake8
        run: pip install flake8

      - name: Lint API Python code
        run: |
          flake8 api/ \
            --max-line-length=100 \
            --extend-ignore=W291,W292,W293,W391 \
            --exclude=__pycache__

      - name: Lint Worker Python code
        run: |
          flake8 worker/ \
            --max-line-length=100 \
            --extend-ignore=W291,W292,W293,W391 \
            --exclude=__pycache__

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install frontend dependencies
        run: cd frontend && npm ci

      - name: Lint JavaScript
        run: cd frontend && npm run lint || true

      - name: Install hadolint
        run: |
          wget -O hadolint \
            https://github.com/hadolint/hadolint/releases/latest/download/hadolint-Linux-x86_64
          chmod +x hadolint
          sudo mv hadolint /usr/local/bin/

      - name: Lint Dockerfiles
        run: |
          hadolint api/Dockerfile
          hadolint worker/Dockerfile
          hadolint frontend/Dockerfile

  # ─────────────────────────────────────────
  # STAGE 2 - TEST
  # ─────────────────────────────────────────
  test:
    name: Test
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          pip install -r api/requirements.txt
          pip install pytest pytest-cov httpx

      - name: Run tests with coverage
        run: |
          cd api
          pytest test_main.py -v \
            --cov=. \
            --cov-report=xml:coverage.xml \
            --cov-report=term-missing

      - name: Upload coverage report
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report
          path: api/coverage.xml

  # ─────────────────────────────────────────
  # STAGE 3 - BUILD
  # ─────────────────────────────────────────
  build:
    name: Build
    runs-on: ubuntu-latest
    needs: test
    services:
      registry:
        image: registry:2
        ports:
          - 5000:5000

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
        with:
          driver-opts: network=host

      - name: Get git SHA
        id: sha
        run: echo "sha=${GITHUB_SHA::8}" >> $GITHUB_OUTPUT

      - name: Build and push API image
        uses: docker/build-push-action@v5
        with:
          context: ./api
          push: true
          tags: |
            localhost:5000/api:${{ steps.sha.outputs.sha }}
            localhost:5000/api:latest
          network: host

      - name: Build and push Worker image
        uses: docker/build-push-action@v5
        with:
          context: ./worker
          push: true
          tags: |
            localhost:5000/worker:${{ steps.sha.outputs.sha }}
            localhost:5000/worker:latest
          network: host

      - name: Build and push Frontend image
        uses: docker/build-push-action@v5
        with:
          context: ./frontend
          push: true
          tags: |
            localhost:5000/frontend:${{ steps.sha.outputs.sha }}
            localhost:5000/frontend:latest
          network: host

  # ─────────────────────────────────────────
  # STAGE 4 - SECURITY SCAN
  # ─────────────────────────────────────────
  security:
    name: Security Scan
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build images for scanning
        run: |
          docker build -t api:latest ./api
          docker build -t worker:latest ./worker
          docker build -t frontend:latest ./frontend

      - name: Install Trivy
        run: |
          sudo apt-get install -y wget apt-transport-https gnupg
          wget -qO - \
            https://aquasecurity.github.io/trivy-repo/deb/public.key \
            | sudo apt-key add -
          echo "deb https://aquasecurity.github.io/trivy-repo/deb generic main" \
            | sudo tee /etc/apt/sources.list.d/trivy.list
          sudo apt-get update
          sudo apt-get install -y trivy

      - name: Scan API image
        run: |
          trivy image \
            --exit-code 1 \
            --severity CRITICAL \
            --ignore-unfixed \
            --format sarif \
            --output api-scan.sarif \
            api:latest

      - name: Scan Worker image
        run: |
          trivy image \
            --exit-code 1 \
            --severity CRITICAL \
            --ignore-unfixed \
            --format sarif \
            --output worker-scan.sarif \
            worker:latest

      - name: Scan Frontend image
        run: |
          trivy image \
            --exit-code 1 \
            --severity CRITICAL \
            --ignore-unfixed \
            --format sarif \
            --output frontend-scan.sarif \
            frontend:latest

      - name: Upload scan results
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: trivy-results
          path: "*.sarif"

  # ─────────────────────────────────────────
  # STAGE 5 - INTEGRATION TEST
  # ─────────────────────────────────────────
  integration:
    name: Integration Test
    runs-on: ubuntu-latest
    needs: security
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Create .env file
        run: |
          cat > .env << EOF
          REDIS_HOST=redis
          REDIS_PORT=6379
          REDIS_PASSWORD=testpassword
          API_HOST=0.0.0.0
          API_PORT=8000
          PORT=3000
          API_URL=http://api:8000
          APP_ENV=development
          EOF

      - name: Start full stack
        run: docker compose up --build -d

      - name: Wait for all services to be healthy
        run: |
          echo "Waiting for services to be healthy..."
          sleep 40
          docker compose ps

      - name: Submit a test job
        run: |
          response=$(curl -s -X POST \
            http://localhost:3000/submit \
            -H "Content-Type: application/json")
          echo "Response: $response"
          job_id=$(echo $response | python3 -c \
            "import sys,json; \
            print(json.load(sys.stdin)['job_id'])")
          echo "JOB_ID=$job_id" >> $GITHUB_ENV
          echo "Job ID: $job_id"

      - name: Poll until job completes
        run: |
          max=20
          count=0
          while [ $count -lt $max ]; do
            response=$(curl -s \
              http://localhost:3000/status/$JOB_ID)
            echo "Response: $response"
            status=$(echo $response | python3 -c \
              "import sys,json; \
              print(json.load(sys.stdin).get('status','unknown'))")
            echo "Attempt $count: status=$status"
            if [ "$status" = "completed" ]; then
              echo "Job completed successfully"
              exit 0
            fi
            count=$((count + 1))
            sleep 5
          done
          echo "Job did not complete in time"
          exit 1

      - name: Tear down stack
        if: always()
        run: docker compose down -v

  # ─────────────────────────────────────────
  # STAGE 6 - DEPLOY
  # ─────────────────────────────────────────
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    needs: integration
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Create .env for deploy
        run: |
          cat > .env << EOF
          REDIS_HOST=redis
          REDIS_PORT=6379
          REDIS_PASSWORD=testpassword
          API_HOST=0.0.0.0
          API_PORT=8000
          PORT=3000
          API_URL=http://api:8000
          APP_ENV=production
          EOF

      - name: Build images
        run: docker compose build

      - name: Rolling update
        run: |
          docker compose up -d redis
          sleep 10

          docker compose up -d api
          timeout=60
          elapsed=0
          while [ $elapsed -lt $timeout ]; do
            health=$(docker inspect \
              --format='{{.State.Health.Status}}' \
              api 2>/dev/null || echo "none")
            echo "API health: $health"
            if [ "$health" = "healthy" ]; then
              echo "API is healthy"
              break
            fi
            sleep 5
            elapsed=$((elapsed + 5))
          done
          if [ $elapsed -ge $timeout ]; then
            echo "API health check failed - aborting deploy"
            exit 1
          fi

          docker compose up -d worker
          docker compose up -d frontend

          echo "Deploy successful"
          docker compose ps
```
29. Write README.md `nano README.md`
```
# HNG DevOps Stage 2 — Containerized Microservices

## Overview
A job processing system made up of four services
containerized with Docker and deployed via a full
CI/CD pipeline using GitHub Actions.

## Architecture
``
Frontend (Node.js:3000)
    ↓
API (FastAPI:8000)
    ↓
Redis (internal only)
    ↓
Worker (Python)
``

## Prerequisites
- Docker Desktop or Docker Engine installed
- Docker Compose v2+
- Git

## How to Run on a Clean Machine

### 1. Clone the repository
```bash
git clone https://github.com/Oluwaferanmi-IR/hng14-stage2-devops
cd hng14-stage2-devops
``

### 2. Set up environment variables
```bash
cp .env.example .env
``
Edit `.env` and fill in real values.

### 3. Start the full stack
```bash
docker compose up --build
``

### 4. What successful startup looks like
``
redis     | Ready to accept connections
api       | Uvicorn running on http://0.0.0.0:8000
worker    | Processing...
frontend  | Frontend running on port 3000
``

All four containers show as healthy:
```bash
docker compose ps
``

### 5. Access the application
Open your browser and go to:
``
http://localhost:3000
``

### 6. Stop the stack
```bash
docker compose down
``

## Services

| Service  | Port | Description |
|---|---|---|
| Frontend | 3000 | User interface |
| API | 8000 | REST API (internal) |
| Worker | - | Background processor |
| Redis | - | Job queue (internal only) |

## CI/CD Pipeline

Pipeline runs automatically on every push:

``
lint → test → build → security scan → integration test → deploy
``

- **Lint:** flake8, eslint, hadolint
- **Test:** pytest with Redis mocked, coverage report uploaded
- **Build:** Images tagged with git SHA and latest
- **Security:** Trivy scans all images for CRITICAL vulnerabilities
- **Integration:** Full stack spun up, job submitted and polled
- **Deploy:** Rolling update with health check verification

## Environment Variables

See `.env.example` for all required variables.

## Bugs Fixed

See `FIXES.md` for a full list of all bugs found
and fixed in the starter code.

## Author
Oluwaferanmiwhitehat
```

30. Commit everything

    - docker compose down
    - git add api/main.py
    - git commit -m "fix: resolve hardcoded redis host missing health endpoint and wrong 404 status in api"
    - git add worker/worker.py
    - git commit -m "fix: resolve hardcoded redis host unused import and missing error handling in worker"
    - git add frontend/app.js frontend/package.json frontend/package-lock.json
    - git commit -m "fix: resolve hardcoded api url missing health endpoint and wrong filename in frontend"
    - git add api/Dockerfile
    - git commit -m "feat: add multi-stage production Dockerfile for api service"
    - git add worker/Dockerfile
    - git commit -m "feat: add multi-stage production Dockerfile for worker service"
    - git add frontend/Dockerfile
    - git commit -m "feat: add multi-stage production Dockerfile for frontend service"
    - git add docker-compose.yml
    - git commit -m "feat: add docker-compose with named networks health checks and resource limits"
    - git add api/test_main.py
    - git commit -m "test: add six unit tests for api with redis mocked"
    - git add .github/workflows/pipeline.yml
    - git commit -m "ci: add full cicd pipeline with lint test build scan integration and deploy stages"
    - git add FIXES.md
    - git commit -m "docs: document all bugs found and fixed in starter code"
    - git add README.md
    - git commit -m "docs: add readme with setup instructions and architecture overview"
    - git add .env.example
    - git commit -m "chore: add env example with placeholder values for all required variables"
    - Verify all commits look corrent: `git log --oneline` You should see all your commits listed with proper messages.

31. Push to github

     - Push your branc `git push origin feat/containerize-microservices`
     - If aasked for password, go to github and generate classic token
32. merge your branch to main repo

    - `git checkout main`
    - `git merge feat/containerize-microservices`
    - `git push origin main`
    - you should see all your files in your main branch now
      <img width="763" height="587" alt="Screenshot 2026-04-23 022032" src="https://github.com/user-attachments/assets/51c953e6-ef67-4edc-8b05-bba2b9312427" />

    - go to action and run all job till deployment stage without any erroe
<img width="1120" height="373" alt="Screenshot 2026-04-23 025748" src="https://github.com/user-attachments/assets/10d65922-d865-4cb5-b4e8-306d63bf42f9" />




