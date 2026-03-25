### DATABASE SERVER DEPLOYMENT
---

### METHODOLOGY USED

1. Log in to AWS Management Console
2. Go to EC2 Dashboard
3. In the search box, type EC2. Select it and click "Launch Instance."
4. Configure Instance

   - Name and tags: PrestaShop-Database
   - Application and OS Images (AMI): Ubuntu Server 24.04 LTS (Free tier eligible)

     <img width="1123" height="607" alt="Screenshot 2026-02-18 203131" src="https://github.com/user-attachments/assets/5c020d60-38e6-4068-b8d5-c89ccc130156" />

   - Instance type: t3.micro
   - Key pair: Click "Create new key pair";  Key pair name: prestashop-key; Key pair type: RSA;  Private key file format: .pem ○   "Create key pair" (file will download—save it safely!)
   - Network settings: Click "Edit"; security group name: database-sg; Description: Database security group; Inbound security group rules:  
   Rule 1: SSH, Port 22, Source: My IP  
   Rule 2: MySQL/Aurora, Port 3306, Source: application-server

     <img width="1134" height="610" alt="Screenshot 2026-02-18 203216" src="https://github.com/user-attachments/assets/165423fd-65d2-4980-9050-a55b58400f85" />

   - Configure storage: 8 GB gp3 (default)
   - Launch Instance
5. Click on view all instances
6. Wait until Instance State = Running
7. Select your instance and note: Instance ID;  Public IPv4 address (for SSH access) Private IPv4 address (for database connection)
8. Connect to Database Server

   - Go to EC2 Instance Connect.
   - Click on SSH Client
   - Copy the SSH example you find on the page

     <img width="1128" height="602" alt="Screenshot 2026-02-18 204726" src="https://github.com/user-attachments/assets/403301c5-3ae0-4cf4-b44c-04096ae86864" />
     

   - On windows, click the Windows key + R
   - Type `cmd`
   - Run `Cd` into the folder where you saved the key pair you downloaded earlier
   -  Paste the SSH : `ssh -i "Prestashop-key.pem" ubuntu@ec2<yourpublicip>.eu-north-1.compute.amazonaws.com`
  
    <img width="1080" height="556" alt="Screenshot 2026-02-18 211925" src="https://github.com/user-attachments/assets/562ce2ec-0f9c-43f9-a297-f92421d65a5d" />


   -  If you see "Are you sure you want to continue connecting?" type “yes”
9.  Install MySQL on Database Server

    - Update package list: `sudo apt update`
    - Upgrade existing packages:`sudo apt upgrade -y`
    - Install MySQL Server: `sudo apt install mysql-server -y`
    - start MySQL `sudo systemctl start mysql`
    - Verify MySQL is running:`sudo systemctl status mysql`
    - Do secure installation `sudo mysql_secure_installation`
    - Answer the prompts: Validate Password Component? N (or Y if you want strong password enforcement)
    -  Remove anonymous users? Y
    -  Disallow root login remotely? N (remote access is needed)
    -  Remove test database? Y
    -  Reload privilege tables? Y
10. Configure MySQL for Remote Access

    - run `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`
    - Find the line: bind-address = 127.0.0.1 Change it to bind-address = 0.0.0.0

      <img width="1095" height="621" alt="Screenshot 2026-02-18 235729" src="https://github.com/user-attachments/assets/49cfb67c-d9c9-49d2-88d9-9b8319451c6b" />

    - Save and Exit
11. Restart MySQL: `sudo systemctl restart mysql`
12. Verify it's running: `sudo systemctl status mysql`
13. Login to MySQL:

    - run `sudo mysql -u root -p `

      <img width="824" height="311" alt="Screenshot 2026-02-18 230747" src="https://github.com/user-attachments/assets/10406ac8-9299-418a-9868-6bd0c63b74be" />

    - Enter the password set earlier if you set one. if not, just press enter
    - Run the following command

      - `CREATE DATABASE prestashop;`
      - `CREATE USER 'prestashoptest'@'%' IDENTIFIED BY '<put password here>'; `
      - `SELECT user, host FROM mysql.user;` to check if the user was successfully created
      - `GRANT ALL PRIVILEGES ON prestashop. * TO prestashoptest’@'%';` - This is trying to give all rights on the database to the user 
      - `FLUSH PRIVILEGES;`: Applies the changes immediately.
      - `SHOW DATABASES;`




