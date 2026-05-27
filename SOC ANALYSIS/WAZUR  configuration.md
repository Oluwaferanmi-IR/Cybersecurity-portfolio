## WAZUH- CONFIGURATION
---
Wazuh is an open-source security monitoring and threat detection platform used for collecting, analyzing, and responding to security events across systems, networks, and cloud environments. It combines SIEM (Security Information and Event Management) and XDR (Extended Detection and Response) capabilities into a single solution. It has a number of functions that is critical to security. Some of those functions includes:
- log monitoring and analysis
- Intrusion Detection
- Endpoint Security
- File integrity monitoring
- Vulnerability detection
- Compliance monitoring. It makes sure you are compliant with standards like GDPS, PCI-DSS, ISO 27001
- Incident response automation

---
## CONFIGURATION SET-UP
1. Go to `https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html` to download the official OVA file.
2. Import the .OVA file into VMware

   - Open VMware Workstation
   - Click File → Open
   - Select the .ova file you downloaded
   - When it asks to import, click Retry if it gives an OVF warning — it will still work
   - Name it something like Wazuh-Server

<img width="947" height="409" alt="Screenshot 2026-05-27 144427" src="https://github.com/user-attachments/assets/845b60c3-86f0-4dd2-a250-8fd75160aa3f" />

3. Set vmware network to **bridged** so that your vmware can communicate with your host (windows)
<img width="746" height="394" alt="Screenshot 2026-05-27 145331" src="https://github.com/user-attachments/assets/0308ce1a-6ea8-4770-a499-5dbe7a188cfe" />

4. Start the Wazuh vm
5. Once credential is prompted, log inwith:

   - username: wazuh-user
   - password: wazuh
<img width="301" height="244" alt="Screenshot 2026-05-27 145751" src="https://github.com/user-attachments/assets/0ff6fa97-ff02-4ac8-a9b6-298cedd59955" />

6. Once successfully logged in, get your ip address that you would use to access wazuh dashboard. run the command `ip a` **OR** `ifconfig`

<img width="855" height="283" alt="image" src="https://github.com/user-attachments/assets/f023c38b-de4c-44e9-b0ea-d8f00161c88d" />

7. Head over to your windows browser and login to wazuh dashboard `https://<wazuh-vm ip>`
8. Once the dashboard loads, you would be prompted to login;

   - username: admin
   - password: admin
<img width="1019" height="562" alt="Screenshot 2026-05-27 154403" src="https://github.com/user-attachments/assets/66de8177-5d97-42dd-88a4-48304fb57b9b" />

9. After the login, when you access the dashboard, you might be prompted with an api connection error just like this:
<img width="1014" height="429" alt="Screenshot 2026-05-27 154555" src="https://github.com/user-attachments/assets/0ab908a8-d86d-4bb9-bf53-e902941c4980" />

10. If you get that error message, head back to your wazuh-vm terminal and run `sudo systemctl status wazuh-manager`. Be sure it shows active. If it says failed or inactive, run `sudo systemctl restart wazuh-manager` also check if port 55000 is in use `sudo ss -tulnp | grep 55000`

    **Listening port**
    <img width="895" height="61" alt="Screenshot 2026-05-27 160505" src="https://github.com/user-attachments/assets/e433f016-a5ca-4286-8887-0b25ae6a7504" />

    **Wazuh-status**
    <img width="951" height="530" alt="Screenshot 2026-05-27 160526" src="https://github.com/user-attachments/assets/db3f7a9c-fc2e-4a1b-839c-2d100fb0d18b" />

11. Reload your browser, the error should be fixed
12. Go to Agent management to add a new agent. Click on `Deploy new agent`

<img width="1016" height="307" alt="Screenshot 2026-05-27 160906" src="https://github.com/user-attachments/assets/e801f447-0014-441b-882b-0217f769ca1e" />

13. Click your desired agent. I used windows for this lab
14. Set the server Ip to the ip address of your wazuh-vm ip

<img width="1006" height="622" alt="image" src="https://github.com/user-attachments/assets/b79def4f-cb38-4ff5-8d2f-5c779cb03dde" />

15. Wazuh will automatically populate the rest of the field.
16. Scroll down to copy the link to download your windows wazuh agent

<img width="979" height="576" alt="image" src="https://github.com/user-attachments/assets/f02fb2bc-73ce-41e6-aebb-e26d4b98a144" />

17. To download the wazuh- agent, run powershell as administrator and paste the command your copied

<img width="971" height="220" alt="image" src="https://github.com/user-attachments/assets/49b63c20-dabf-4c69-ab2b-bd5d92cb5726" />

18. Start the wazuh-agent byt running `NET START wazuh

<img width="382" height="69" alt="Screenshot 2026-05-27 162153" src="https://github.com/user-attachments/assets/51a0061d-6610-41c5-b63d-6f6d63f2b6eb" />

19. Verify it is working

    - Go back to your Wazuh dashboard
    - Click Agents — you should see your Windows machine listed as Active
      
<img width="1010" height="480" alt="image" src="https://github.com/user-attachments/assets/e3fd55b9-d730-4968-b0c9-7536ea6dd0e0" />

20. Click on it and explore the events coming
    
<img width="1005" height="626" alt="image" src="https://github.com/user-attachments/assets/6236840c-e2a5-4b9c-8eea-afe510c8cf54" />

21. Simulate attacks and watching Wazuh detect them.












