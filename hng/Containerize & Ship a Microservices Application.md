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






