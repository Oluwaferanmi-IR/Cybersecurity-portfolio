## ACTIVE DIRECTORY AND DOMAIN CONTROLLER SETUP

Setting up the server alone is not sufficient; it is essential to configure Active Directory and promote the server to a Domain Controller in order to enable centralized management and authentication within the network.

## PROCEDURE
1. open cmd and run `ipconfig` to get the ipaddr of the windows server
2. Set a static Ip for the server

   - Open control panel --> Network and internet -->  Network and sharing center
   - Right click on 'change adapter setting'
   - Click on properties
   - Clcik on IPv4 --> properties
   - set a static IP

      - use the following IPaddr (use the ipaddr gotten from the `ipconfig` cmd ran earlier
      - The subnet mask will fit itself out
      - Set the gateway as seen in the `ipconfig` cmd
      - change the preferred DNS server to `127.0.0.1` (the server itself)
      - check the 'validate upon exit' box --> OK
        <img width="391" height="452" alt="image" src="https://github.com/user-attachments/assets/8ed2af34-8d48-42e4-859f-298a67b90868" />
  3. Add the role
     - On the server manager dashboard
     - Go to manage --> Add roles and features.
     - Follow the prompt (click on next)
     - When yu get to server roles. check the `Active directory Domain services`
     - Click **Add features** when prompted, the **NEXT** until **INSTALL**
       <img width="779" height="545" alt="Screenshot 2026-04-27 113126" src="https://github.com/user-attachments/assets/2247eede-02b3-4059-9355-2b10f2015543" />
  4. Promote to Domain controller

     - Once the AD role is installed, back to the server dashboard, click the **Flag iicon** (notification) on the top bar of the dashboard and select **Promote this server to a domain controller**
     -  Select **Add a new forest**. Root domain name: ferammi.local.
       <img width="784" height="386" alt="Screenshot 2026-04-27 114049" src="https://github.com/user-attachments/assets/803c222a-c2a5-46c2-9dc3-04130b06d1a5" />
     -   Set a DSRM Password (keep this safe) and click Next through the defaults.
     -   Click Install. The server will reboot. Log back in as FERANMI\Administrator. 
 ---
 ### For the sake of this project, we created a **sales** environment
 1. Create the OU (Organizational Unit)

    - In the server manager dashoard
    - click tools -----> Active Directory Users and Computers
      <img width="1365" height="700" alt="Screenshot 2026-04-27 121227" src="https://github.com/user-attachments/assets/e56c1c37-263a-4a65-add1-1b81c6d3e077" />
    - Right-click feranmi.local -----> New -----> Organizational Unit. Name it Sales.
  2. Create the user

     - Right-click the new sales OU -----> New -----> User.
     - First Name: Marvel, Login Name: Marvel. Click Next.
     - Set a password (e.g., P@ssword123) and uncheck "User must change password at next logon" for this lab. (as you wish)
       <img width="1043" height="640" alt="Screenshot 2026-04-27 121417" src="https://github.com/user-attachments/assets/ac77f6fb-6250-4a5b-98b4-576cefc6aaa0" />
     - Follow the prompt till finish

---
### CONNECT THE WINDOWS 11 VM ( THE STAFF SYSTEM)

1. Set the client DNS

   - On your windows machine, go to control panel -----> Network and internet -----> Network and sharing center  -----> Change adapter settings ----> right click and choose properties ------> IPv4 -----> properties
   - Set the Preferred DNS server to the IP of your Server .Leave the IP settings as DHCP or set a static one in the same range
<img width="487" height="543" alt="image" src="https://github.com/user-attachments/assets/acb45e9c-2ecd-4a54-897f-e66b0dd1fb3b" />
   - click close

2. Join the domain

   - Click Start, type `sysdm.cpl`, and press Enter.
<img width="807" height="571" alt="Screenshot 2026-04-27 122026" src="https://github.com/user-attachments/assets/895f00ec-016b-45c6-90de-ee254e1749a5" />

   - Click Change... -----> Select Domain  -----> Type feranmi.local.
   - Enter the Server's Admin credentials (FERANMI\Administrator and your password). 
 <img width="997" height="592" alt="Screenshot 2026-04-27 122250" src="https://github.com/user-attachments/assets/9f1d4df5-2a03-4e7c-b8e3-d119e5b91d35" />
 
    - Restart the windows machine
    - On the login screen, select Other User.
    - Log in as marvel.ferry

with that the system has been added to your sever and can be controlled from server dashboard.
 




  
