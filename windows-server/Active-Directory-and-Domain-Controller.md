## AD and DC SETUP

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

  
