## Server Setup & Nginx Configuration
---
## Overview

This project involves provisioning a Linux server on AWS EC2, create a non-root user called hngdevops with sudo privileges, disabling root SSH login, Configure UFW to allow only ports 22, 80, and 443 (all other ports closed) configuring Nginx as a web server, securing it with SSL and hardening it following security best practices.

## What Was Built

- A live server running on **AWS EC2** (Ubuntu 22.04 LTS)
- **Nginx** configured to serve two endpoints:
  - `GET /` → Static HTML page displaying HNG username
  - `GET /api` → JSON response with track and username info
- **HTTPS** secured with a valid Let's Encrypt SSL certificate
- **HTTP → HTTPS** redirect with 301 status code
- **UFW Firewall** configured to allow only ports 22, 80 and 443
- **SSH hardened** — root login disabled, password auth disabled
- Non-root user `hngoluwaferanmi` with sudo privileges configured

## Tools used

- AWS EC2 - Cloud Servie Provider
- Ubuntu 22.04 LTS - Server Operating Syatem
- Nginx - Web Server
- Certbot + Let's Encrypt - SSL certificate
- UFW - Firewall
- DuckDns - Free Domain name.

## Procedures
---
1. Create an AWS Account
2. Launch EC2 instance

   - Name server - hng-sever
   - Applicatin and OS image - Ubunu Server
   - Instance type - t2.micro
   - Create SSH key pair

     - Under key pair, click edit
     - give it a name (hng-key)
     - key pair type: RSA
     - Private key format (.pem)
     - create key pair
    - configure network settings

      - Allow ssh traffic from anywhere (port 22)
      - Allow HTTP traffic from anywhere (port 80)
      - Allow HTTPs traffic from anywhere (port 443)
        <img width="730" height="511" alt="Screenshot 2026-04-14 121523" src="https://github.com/user-attachments/assets/e45d0fd9-b559-40c5-ae05-96959fdc1b64" />

    - storage - 8 GiB gp2
    - Launch the instance

3. Connect to the server from laptop cmd

   - on EC2 dashboard
   - Click on the created instance
   - click connect
   - click ssh-client
   - copy the example you find on the page
   - open your cmd
   - go into the folder where you stored the key-pair
   - paste the ssh command
     <img width="1023" height="476" alt="image" src="https://github.com/user-attachments/assets/1f6585eb-62fe-4339-8dcb-215dc5eabaaf" />

  4. update the server `sudo apt update && sudo apt upgrade -y`
  5. Create the new user `sudo useradd hngoluwaferanmi`

<img width="693" height="328" alt="Screenshot 2026-04-14 123503" src="https://github.com/user-attachments/assets/192efc02-4605-4ac6-8c8e-d63cd7e6e954" />

  6. GIve the user sudo privilege `sudo usermod -aG sudo hngoluwaferanmi`
  7. Give the user SSH Access

     - create .ssh directory for the user `sudo mkdir -p /home/hngoluwaferanmi/.ssh`
     - Copy the authorized key from the ubuntu user `sudo cp /home/ubuntu/.ssh/authorized_key /home/hngoluwaferanmi/.ssh/authorized_keys`
     - Give the user ownership of these files `sudo chown -R hngoluwaferanmi:hngoluwaferanmi /home/hngoluwaferanmi/.ssh`
     - set correct permissions `sudo chmod 700 /home/hngoluwaferanmi/.ssh` `sudo chmod 600 /home/hngoluwaferanmi/.ssh/authorized_keys`

       <img width="1020" height="370" alt="Screenshot 2026-04-14 131521" src="https://github.com/user-attachments/assets/8504fe7f-7185-4c9d-ad35-20e55d4675d9" />
  8. Configure Passwordless sudo for specific commands `sudo visudo -f /etc/sudoers.d/hngoluwaferanmi`

     - This opens a text editor in the terminal and type
       `hngoluwaferanmi ALL=(root) NOPASSWD:/usr/sbin/sshd, /usr/sbin/ufw`
      save and exit
       <img width="1080" height="177" alt="Screenshot 2026-04-14 133944" src="https://github.com/user-attachments/assets/61d60844-7248-4de3-8206-b0f3ce875a43" />
  9. Harden SSH security `sudo nano /etc/ssh/sshd_config`

      - search `PermitROOTlogin` and set to no
      - serach `Password Authentication` and set to no
      - search `PubkeyAuthentication` and set to Yes
        
        <img width="727" height="479" alt="Screenshot 2026-04-14 135037" src="https://github.com/user-attachments/assets/c181317a-e029-4b98-9aee-826af9dfa153" />


      - Restart SSH to apply changes `sudo systemctl restart sshd`
   10. Open a new terminal and test logging in as the new user
   11. While logged in as the user, configure UFW firewall

       ```
       sudo ufw allow 22
       sudo ufw allow 80
       sudo ufw allow 443
       sudo ufw enable
       ```
       
       <img width="729" height="280" alt="Screenshot 2026-04-14 135448" src="https://github.com/user-attachments/assets/4410eee9-6930-40d7-84fe-a68245895d55" />
   12. Get a domain. I got a free domain from duckdns.org
   13. Point your domain to the server. (for duckdns.org, just copy your server public ip from aws and update it with the ip given by duckdns)
   14. Confirm your domain resolves to your ip by doing `nslookup hngoluwaferanmi.duckdns.org`
   15. Install Nginx. Back on the server as hngoluwaferanmi

       - `sudo apt install nginx -y`
       - check if it is running `sudo systemctl status nginx`

         <img width="1082" height="373" alt="Screenshot 2026-04-14 142211" src="https://github.com/user-attachments/assets/5432177f-5541-4680-a6ae-09b59064ca0f" />

       - Open browser and visit `http://hngoluwaferanmi.duckdns.org` This should show the defaault welcome to nginx page.
      
         <img width="1142" height="291" alt="Screenshot 2026-04-14 144553" src="https://github.com/user-attachments/assets/1e7df99e-273b-4bb0-b5b3-0fb10d8b7f3b" />

  16. Create HTML page

      - create the folder `sudo mkdir -p /var/www/mysite`
      - create and open the HTML file `sudo nano /var/www/mysite/index.html`
      - fill with your html code
   ```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>server setup</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
        }
        .card {
            background: white;
            padding: 40px;
            border-radius: 10px;
            text-align: center;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 { color: #333; }
        p { color: #666; }
    </style>
</head>
<body>
    <div class="card">
        <h1>HNG DevOps Track</h1>
        <p>Username: <strong>I am Oluwferanmi Devops specialits strong></p>
        <p>Stage 0 - Server Setup & Nginx Configuration</p>
    </div>
</body>
</html>
```
<img width="780" height="500" alt="Screenshot 2026-04-14 145527" src="https://github.com/user-attachments/assets/d0cec048-9957-4733-9cd2-719fa794dff1" />

  17. Configure nginx `sudo nano /etc/nginx/sites-available/mysite` fill it with
```
server {
    listen 80;
    server_name hngoluwaferanmi.duckdns.org;

    root /var/www/mysite;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    # Return JSON at /api
    location /api {
        default_type application/json;
        return 200 '{"message": "HNGI14 Stage 0", "track": "DevOps", "username": "OluwaferanmiWhitehat"}';
    }
}
```

<img width="1062" height="289" alt="Screenshot 2026-04-14 160538" src="https://github.com/user-attachments/assets/d7c5ec4d-72e8-43f9-a316-8fce72707e95" />

  18. Enable the new config `sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/`
  19. Disable the default site `sudo rm /etc/nginx/sites-enabled/default`
  20. Test config for errors `sudo nginx -t` --> you should see test is successful
  21. Get SSL certificate from let's encrypt

      - Install certbot `sudo apt install certbot python3-certbot-nginx -y`
      - get certificate `sudo certbot --nginx -d hngoluwaferanmi.duckdns.iorg`
      - Enter your email
      - Agree to terns (Y)
      - Shere email with EFF (N)
        Certbot will automatically get your certificate and update nginx config. you should see successfully received certificate
  22. Reload nginx `sudo systemctl reload nginx`
  23. Test setup

      - test HTTPS homepage `https://hngoluwaferanmi.duckdns.org` - should show your HTML page

        <img width="1133" height="659" alt="Screenshot 2026-04-14 155836" src="https://github.com/user-attachments/assets/06a54fa5-f821-429b-94de-9c59841909f7" />
        
      - test API endpoint `https://hngoluwaferanmi.duckdns.org/api` - should return json file

        <img width="1147" height="233" alt="Screenshot 2026-04-14 155903" src="https://github.com/user-attachments/assets/076acda6-62f7-463c-a36b-2d10fe71e5d8" />

      -  test HTTP redirect `http://hngoluwaferanmi.duckdns.org` -should automatically redirect to `https://`
      
      


         




    
       

       

