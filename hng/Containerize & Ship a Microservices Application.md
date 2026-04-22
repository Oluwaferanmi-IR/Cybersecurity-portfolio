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

3. Open the ubuntu
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

6. Clone the repository on your laptop

   - cloning to desktop `cd /mnt/c/users/user/desktop`
   - clone the forked repo `git clone https://github.com/oluwaferanmi-IR/hng14-stage2-devops`
   - go into the folder `cd hn14-stage2-devops`
  <img width="981" height="153" alt="Screenshot 2026-04-22 130812" src="https://github.com/user-attachments/assets/68970db9-5cd4-4608-9d9f-bcdb8a304afc" />

7. Open code in vs code `code .`
   <img width="1123" height="530" alt="image" src="https://github.com/user-attachments/assets/d8813704-86df-4677-82a9-d1232034bb70" />

8. Create working branch `git checkout -b feat/containerize-microservices`
9. verify `git branch` it should show `* feat/containerize-microservices
  main`






