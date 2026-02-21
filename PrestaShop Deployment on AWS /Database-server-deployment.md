### DATABASE SERVER DEPLOYMENT
---
### METHODOLOGY USED
1. Log in to AWS Management Console
2. Go to EC2 Dashboard
3.  In the search box, type EC2. Select it and click "Launch Instance."
4.  Configure Instance:
   -  Name and tags: PrestaShop-Database
   -  Application and OS Images (AMI): Ubuntu Server 24.04 LTS (Free tier eligible)
   -   Instance type: t3.micro
   -   Key pair: Click "Create new key pair";  Key pair name: prestashop-key; Key pair type: RSA;  Private key file format: .pem              ○ Click "Create key pair" (file will download—save it safely!)
   -  Network settings: Click "Edit"; security group name: database-sg; Description: Database security group; 
      Inbound security group rules:  
      Rule 1: SSH, Port 22, Source: My IP  
      Rule 2: MySQL/Aurora, Port 3306, Source: application-server 

