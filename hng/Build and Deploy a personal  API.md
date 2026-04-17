## BUILDING AND DEPLOYING A PERSONAL API
---

## PROJECT OVERVIEW
In the previous proect (server setup), we provisioned a server and configured nginx. Now, we will write a small API and deploy it. what this project aim to achieve include:

1. Build the API Write an API with the following three endpoints:

   - GET / : returns the following JSON response exactly: { "message": "API is running" }
   - GET /health : returns the following JSON response exactly:{ "message": "healthy" }
   - GET /me : returns the following JSON response exactly:{ "name": "Your Full Name", "email": "you@example.com", "github": "https://github.com/yourusername" } All three endpoints must return Content-Type: application/json, an HTTP status code of 200, and respond within 500ms.
2. Deploy It: Build your API, test it locally, then deploy it publicly using a VPS with an Nginx Reverse Proxy: Provision a cloud server (you can reuse your Stage 0 server if you want) Run your application on a non-public port Configure Nginx to reverse proxy public traffic to your app The service must be persistently running.
3. Document It Push your code to a public GitHub repository with a README that includes: What the project is and how to run it locally The three endpoints and their expected responses Your live deployment URL Evaluation Criteria All endpoints must return Content-Type: application/json, no HTML, no plain text All endpoints must return HTTP status 200 The /me endpoint must contain your real details, name, email, and a valid GitHub profile link The app must run on a local port with nginx proxying public traffic to it, do not expose your app port directly The service must stay up on its own, use systemd, pm2, supervisor, or equivalent to keep it alive All endpoints must respond within 500ms.

---
## TOOLS
1. AWS --> server
2. Node.js --> Runs our JavaScript API code
3. Express --> Framework that makes building APIs simple
4. Pm2 --> Keeps your app running forever
5. git --> Stores and showcases your code

---
## STEPS
1. Install node.js `curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -` wait for it to finish the run `sudo apt install nodejs -y`
<img width="969" height="492" alt="Screenshot 2026-04-17 011917" src="https://github.com/user-attachments/assets/843a3285-f887-416a-945f-b8e845c3661a" />

2. verify installation `npm --version` `node --version` (should display verison of the two, if installed properly)
<img width="737" height="585" alt="Screenshot 2026-04-17 011939" src="https://github.com/user-attachments/assets/f39ee0fa-475c-45e5-9495-9e9636808357" />

3. create project file `mkdir -p /home/hngdevops/myapi`
4. go into the file directory `cd /home/hngdevops/myapi`
5. check working directory to be sure you are in the project file directory `pwd`
6. Initialise Your Project `npm init -y` (This creates a package.json file )
<img width="566" height="467" alt="Screenshot 2026-04-17 012001" src="https://github.com/user-attachments/assets/74db1efe-a7c6-4368-808a-617cdd658567" />

7. Install Express `npm install express`
8. Write API `nano index.js`

```
const express = require('express');
const app = express();
const PORT = 3000;

// GET /
app.get('/', (req, res) => {
  res.status(200).json({ message: 'API is running' });
});

// GET /health
app.get('/health', (req, res) => {
  res.status(200).json({ message: 'healthy' });
});

// GET /me
app.get('/me', (req, res) => {
  res.status(200).json({
    name: 'Olajide Oluwafeanmi',
    email: 'moffice2104@gmail.com',
    github: 'https://github.com/Oluwaferanmi-IR'
  });
});

app.listen(PORT, () => {
  console.log(`API running on port ${PORT}`);
});
```

<img width="541" height="495" alt="Screenshot 2026-04-17 012120" src="https://github.com/user-attachments/assets/d73368b5-1248-4c1f-97ee-1766a27911f6" />

9. Test that the app runs loacally

    - `node index.js` (it should show API running on port 3000)
    - Open a second CMD window on your laptop and SSH in again
    - Test each endpoint

      - `curl http://localhost:3000/` --> {"message":"API is running"}
      - `curl http://localhost:3000/health` -->  {"message":"healthy"}
      - `curl http://localhost:3000/me` --> {"name":"Olajide Oluwaferanmi","email":"moffice2104@gmail.com","github":"https://github.com/Oluwafranmi-IR"}
    - All three working? Go back to the first window and stop the app:`ctrl + C`

<img width="1083" height="80" alt="Screenshot 2026-04-17 012104" src="https://github.com/user-attachments/assets/134ab148-2e74-4fbc-a820-a87378bc986d" />

10. Install PM2 `sudo npm install -g pm2` (Without PM2 your app dies the moment you close your terminal. PM2 keeps it alive forever)

11. Verrify `pm2 --version`
12. Start App With PM2: `pm2 start index.js --name myapi` (should display a table with myapi status as online)

<img width="717" height="150" alt="Screenshot 2026-04-17 012217" src="https://github.com/user-attachments/assets/706717bd-cf0e-41d3-91bc-450f62c6ee08" />

13. Make PM2 survive server reboots `pm2 startup` This displays a long command. Copy it exactly from your screen and run it. It looks something like: `sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u hngdevops --hp /home/hngdevops`

14. save process list `pm2 save`

<img width="728" height="671" alt="Screenshot 2026-04-17 012406" src="https://github.com/user-attachments/assets/e85cd7a5-fa7a-4672-a1d0-ac94239fc91b" />

15.  Update Nginx Config `sudo nano /etc/nginx/sites-available/mysite` Delete everything ther and paste:
```
# HTTP → redirect to HTTPS
server {
    listen 80;
    server_name hngoluwaferanmi.duckdns.org;
    return 301 https://$host$request_uri;
}

# HTTPS → reverse proxy to Node.js app
server {
    listen 443 ssl;
    server_name hngoluwaferanmi.duckdns.org;

    ssl_certificate /etc/letsencrypt/live/hngoluwaferanmi.duckdns.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/hngoluwaferanmi.duckdns.org/privkey.pem;

    # SSL optimisation
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        # Performance optimisations
        proxy_buffering on;
        proxy_connect_timeout 5s;
        proxy_send_timeout 10s;
        proxy_read_timeout 10s;
    }
}
```

<img width="740" height="505" alt="Screenshot 2026-04-17 012723" src="https://github.com/user-attachments/assets/c1b449b9-6ef8-49c3-9725-171bebafcea6" />

16. Test the config `sudo nginx -t` it should return a configuration successful message
17. reload nginx `sudo systemctl reload nginx`

<img width="719" height="185" alt="Screenshot 2026-04-17 012758" src="https://github.com/user-attachments/assets/fe47ca3b-3b29-4232-acc6-abad5f895a8a" />

18. Test Everything Live: Open browser and run

    - https://hngoluwaferanmi.duckdns.org --> should return {"message":"API is running"}

      <img width="378" height="140" alt="image" src="https://github.com/user-attachments/assets/ac45c413-9b7b-4685-949a-58f08243c562" />

    - https://hngoluwaferanmi.duckdns.org/health ---> {"message":"healthy"}

      <img width="421" height="132" alt="image" src="https://github.com/user-attachments/assets/56b5f9f9-c0a2-4a3c-85ee-b30e61877412" />

    - https://hngoluwaferanmi.duckdns.org/me --> Should show your name, email and GitHub

      <img width="722" height="129" alt="image" src="https://github.com/user-attachments/assets/cca797c8-22c0-4317-8280-372d51f3a52a" />

19. Push Code to GitHub `sudo apt install git -y`
20. Configure Git: `git config --global user.name "Oluwaferanmiwhitehat"`  `git config --global user.email "moffice2104@gmail.com"`
21. Create .gitignore: `nano .gitignore` type inside `node_modules/` save and exit

<img width="723" height="148" alt="Screenshot 2026-04-17 013123" src="https://github.com/user-attachments/assets/6727ae3e-bdf2-4c75-a05d-7acfa8513e37" />

22. create README `nano README.md`
23. paste
```
# HNG DevOps Stage 1 — Personal API

## Live URL
https://hngoluwaferanmi.duckdns.org

## Description
A REST API built with Node.js and Express, deployed
on AWS EC2 with Nginx as a reverse proxy and PM2
for process management.

## How to Run Locally

### Requirements
- Node.js v20+
- npm

### Steps
```bash
git clone https://github.com/yourusername/hng-devops-stage-1
cd hng-devops-stage-1
npm install
node index.js
``
API runs on http://localhost:3000

## Endpoints

| Endpoint | Method | Response |
|---|---|---|
| / | GET | {"message": "API is running"} |
| /health | GET | {"message": "healthy"} |
| /me | GET | {"name":"...","email":"...","github":"..."} |

## Tech Stack
- Node.js + Express
- Nginx (reverse proxy)
- PM2 (process manager)
- AWS EC2 Ubuntu 22.04
- DuckDNS
- Let's Encrypt SSL

## Author
Oluwaferanmiwhitehat
```
save and exit

<img width="723" height="580" alt="Screenshot 2026-04-17 013230" src="https://github.com/user-attachments/assets/e0534dd4-3dbc-44e4-bd3f-c91b53e0ea07" />

24. Create GitHub Repo

    - Go to https://github.com
    - Click "+" → "New repository"
    - Name: hng-devops-stage-1
    - Set to Public
    - Set to Public
    - Do NOT tick "Add README"
    - Click "Create repository"

<img width="664" height="545" alt="image" src="https://github.com/user-attachments/assets/0d3579cc-bb23-4e1c-a300-3e80e4043e8f" />

25. Push Your Code

    - still in /home/hngdevops/myapi dir
    - `git init`
    - `git add .`
    - `git commit -m "HNG DevOps Stage 1 - Personal API"`
    - `git branch -M main`
    - `git remote add origin https://github.com/oluwaferanmi-ir/hng-devops-stage-1.git`
    - `git push -u origin main`

<img width="642" height="501" alt="Screenshot 2026-04-17 013557" src="https://github.com/user-attachments/assets/99a20fa6-45cc-4869-b873-f90d59a01d3a" />

26. If asked for password — use your GitHub Personal Access Token not your GitHub password.

    - Go to https://github.com/settings/tokens
    - Click "Generate new token (classic)"
    - Name it hng-server
    - Tick the "repo" checkbox
    - Click "Generate token"
    - Copy it immediately — paste it as your password when Git asks
