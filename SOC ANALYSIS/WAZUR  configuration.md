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

3. Set vmware netwo

