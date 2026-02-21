1. Access PrestaShop Installer:

   - Open web browser
   - Navigate to `http://<public_ip of application server>`

      <img width="743" height="416" alt="Screenshot 2026-02-19 023028" src="https://github.com/user-attachments/assets/9d54dedb-c978-4559-a90d-2c09587b56af" />

   - Complete the installation wizard
   - At system configuration: set database server address (use database public IP). Do not use localhost or 127.0.0.1. The name, login, and password should be the same as the user created earlier while configuring the database server
   - Test database connection: you should see a message saying `database is connected.` Click next and complete the installation.
     
     <img width="734" height="549" alt="Screenshot 2026-02-19 124013" src="https://github.com/user-attachments/assets/08f7ed28-42ef-4886-8ba0-ca518be57d20" />

1. Return to SSH terminal on application server
2. Remove installation directory : `sudo rm -rf /var/www/html/install`
3. Rename admin directory: `sudo mv /var/www/html/admin /var/www/html/admin_secure` (if you want to)
4. Open a web browser to access the backend and the storefront.

   - Front End Access Type:` http://<application public_ip>`

     <img width="1107" height="665" alt="Screenshot 2026-02-19 124643" src="https://github.com/user-attachments/assets/9b651cd6-bc9f-41df-a014-044df4c0c4b6" />

   - Back End Access Type: `http://<application public_ip>/admin`
  
     <img width="1115" height="597" alt="Screenshot 2026-02-19 125517" src="https://github.com/user-attachments/assets/154c4c04-596b-4275-aa55-5c01df0efd4d" />

     You must remove the installation directory before you can access 
the backend. 


