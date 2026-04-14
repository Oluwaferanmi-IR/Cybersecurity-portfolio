## Linux Server Setup & Nginx Configuration
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
       

       

