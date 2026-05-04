## SPLUNK CONFIGURATION

---

## OVERVIEW
Splunk is a Security Information and Event Management (SIEM) platform that collects, indexes, searches, and visualizes machine-generated data (logs) from virtually any source in real time. It is used to monitor infrastructure, detect threats, and investigate incidents.

---

### CONFIGURATION STEPS
1. Download Splunk Enterprise

   - Go to https://www.splunk.com
   - Click Products > Splunk Enterprise > Free Trial
<img width="1157" height="524" alt="Screenshot 2026-05-02 180125" src="https://github.com/user-attachments/assets/1e309e20-cc74-4f08-b816-3e01238464fc" />

   - Create an account or log in
   - Select Windows as your platform
<img width="1149" height="612" alt="Screenshot 2026-05-02 180335" src="https://github.com/user-attachments/assets/c53cdc5b-53e3-4949-bca0-3b950a23ae44" />

   - Download the .msi installer
  
2. Run the Installer

   - Double-click the downloaded .msi file
   - Check the box to accept the License Agreement

     <img width="492" height="386" alt="Screenshot 2026-05-04 133617" src="https://github.com/user-attachments/assets/513a17a5-7dbc-45d4-a0a0-2250ed169d7a" />

   - Choose Customized Installation (recommended) or Local System
   - Set your installation path (default is C:\Program Files\Splunk)
   - Click Next

3. Create Admin Credentials

   - Enter your desired admin username
   - Enter a strong password

     <img width="489" height="386" alt="Screenshot 2026-05-04 133645" src="https://github.com/user-attachments/assets/e129dc7a-3624-47fb-9ec0-ec380a6750b1" />

   - Click Next then Install
   - Wait for installation to complete
   - Check the box Launch browser with Splunk Enterprise at the end of install
   - Splunk will open in your browser with a url of like `http://localhost:8000`
   - Login with the admin credentials you just created

     <img width="1150" height="613" alt="Screenshot 2026-05-04 135602" src="https://github.com/user-attachments/assets/8e234179-6bbe-4fb3-b755-fe84cbb33ffa" />

   - The splunk dashboard will open after the login process

     <img width="1143" height="614" alt="Screenshot 2026-05-04 135656" src="https://github.com/user-attachments/assets/5fcb9c6c-4244-4d4e-94f2-82a07ddc63ed" />

4.  Enable Receiving Port (9997)

   - On the dashboard, go to Settings > Forwarding and Receiving
   - Click Configure Receiving
   - Click New Receiving Port
   - Enter 9997 and click Save

     <img width="1137" height="398" alt="Screenshot 2026-05-04 140801" src="https://github.com/user-attachments/assets/ca682cdb-a8a5-4f7b-9dec-da7a71ad16b9" />

5. Download Universal Forwarder so we can forward data from our computer to the server

   - Go to https://www.splunk.com/en_us/download/universal-forwarder.html
   - Select Windows platform
   - Download the .msi file
   - Launch the .msi file
   - check the agreement box
  
     <img width="494" height="383" alt="Screenshot 2026-05-04 135908" src="https://github.com/user-attachments/assets/f93deb73-0d89-47d4-bf43-cc99f58b2908" />

   - Click customise options
   - leave the password page blank
   - choose virtual account
  
     <img width="493" height="379" alt="Screenshot 2026-05-04 140050" src="https://github.com/user-attachments/assets/0adf0ec9-af36-4021-b47e-cd55351b9e2c" />

   - click all the options that pops in the next page and choose choose a path `DOWNLOAD`
     
     <img width="484" height="379" alt="Screenshot 2026-05-04 140132" src="https://github.com/user-attachments/assets/0dd9cbe8-f706-4286-a759-ba1d67a402c8" />
     
   - Create credentials with username and strong password

     <img width="490" height="388" alt="Screenshot 2026-05-04 140318" src="https://github.com/user-attachments/assets/d5057401-132f-4bb8-b83b-3b00e015d1c6" />

   - When prompted for Deployment Server: Hostname: 127.0.0.1 (if Splunk is on the same machine) ; Port: 8089
  
     <img width="490" height="381" alt="Screenshot 2026-05-04 140434" src="https://github.com/user-attachments/assets/26236e13-5a00-40d7-84e6-8a12fc89d3bc" />

   - When prompted for Receiving Indexer: Hostname: 127.0.0.1; Port: 9997

     <img width="489" height="382" alt="Screenshot 2026-05-04 140709" src="https://github.com/user-attachments/assets/6c8d158c-1a12-431f-8873-fe27a0e6cd0b" />

   - Click Next > Install

6. Verify proper installation

   - On the dashboard, go to search
   - click data summary, you should see the hostname of your omputer
   - You can verify your hostname by going to cmd and runthe command `hostname`

     <img width="843" height="222" alt="Screenshot 2026-05-04 141240" src="https://github.com/user-attachments/assets/7f21a730-6057-447a-884b-fb5397015309" />

7. Verify Data in Splunk

   - on the dashboard
   - Open Search & Reporting
   - Run this search: `host='ayoife'`  set to verbose mode for more intense search
<img width="1143" height="534" alt="Screenshot 2026-05-04 141508" src="https://github.com/user-attachments/assets/3a356aba-4d2b-4cbc-8ada-dea139b4d79f" />

8. Get dataset we can use on the server

   - go to `github.com/splunk/botsv3`
   - Download the dataset in the github
   - Extract the dataset into this path `c:\Program Files\Splunk\etc\apps`

     <img width="612" height="445" alt="image" src="https://github.com/user-attachments/assets/f75ef355-bc0f-4cf2-814d-31ce44d201ae" />

  - Restart the server: dashboard ---> settings ----> server control ----> Restart splunk

    <img width="1142" height="233" alt="image" src="https://github.com/user-attachments/assets/a45fbbef-3602-4ce5-bcbe-62f00d677e1d" />

9. Check the dateset

    - go to dashboard ---> search --- `index="botsv3"

      <img width="1140" height="528" alt="Screenshot 2026-05-04 161813" src="https://github.com/user-attachments/assets/d91f5d24-1d45-4d11-84f6-ffef43031518" />

      
