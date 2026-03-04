# Cowrie Honeypot Lab
In this lab I demonstrated the setup, scanning, and monitoring of a Cowrie SSH honeypot using Wireshark.

---

## Overview
This project demonstrates the installation and configuration of a Cowrie SSH honeypot, scanning its port, and monitoring traffic using Wireshark.  

---

## Objectives
- Install Cowrie SSH honeypot on a Linux VM
- Configure and run Cowrie
- Scan Cowrie’s SSH port using Nmap
- Capture and analyze SSH traffic using Wireshark

---

## Tools Used
- **Cowrie Honeypot** – SSH honeypot emulator
- **Wireshark / Tshark** – Network packet capture and analysis
- **Nmap** – Network scanning
- **Ubuntu / Kali** – Virtual environment

---

## Lab Setup
1. **Environment**
   - Ubuntu Desktop VM
   - Network: NAT / Host-only (depending on lab)
   - Kali -Attacker machine
   - Network : NAT
2. **Cowrie Installation**
   - Installed from repository / downloaded package
   - Default SSH port: `2222`
  
---

## Step-by-Step Procedure
1. Install Kali linux and Ubuntu
2. Install Cowrie honeypot

   - Update the system `sudo apt update && sudo apt upgrade -y`
   - Install the dependecies required by cowrie `sudo apt install git python3 python3-venv python3-pip libssl-dev libffi-dev build-essential -y`
   - Clone cowrie repository from github `git clone https://github.com/cowrie/cowrie.git` A folder called **cowrie** is downloaded
   - Go to the cowrie directory `cd cowrie`
   - Create a Python virtual environment : This isolates Cowrie’s Python dependencies.
     ```
     python3 -m venv cowrie-env
     source cowrie-env/bin/activate
     ```
   - Install the required python packages
     ```
     pip install --upgrade pip
     pip install -r requirements.txt
     ```

     <img width="874" height="606" alt="Screenshot 2026-03-04 102118" src="https://github.com/user-attachments/assets/ac126b1a-f207-4238-99fa-71dbda9daca7" />

   - install Cowrie in editable mode from the project root `pip install -e .`
  3. start cowrie `cowrie start` or `bin/cowrie start`
  4. check that cowried is running `cowrie status` or `bin/cowrie status`
  5. Verify listening port `sudo netstat -tulnp | grep python`
  6. Run scan on the attacker machine (kali)

     - Basic scan `nmap <ubuntuip>`
     - Service version scan `nmap -sV <ubuntuip>`
     - Aggresive scan `nmap -A <ubuntuip>`
     - Scan the cowrie honeypot port `nmap -sV -p 2222 <ubuntuip>`

       <img width="648" height="244" alt="image" src="https://github.com/user-attachments/assets/b5710ccf-7110-490a-bc7d-9c8128a50e2c" />
  7. Capture Network Traffic with Wireshark

     - Install wireshark `sudo apt install wireshark`
     - start wireshark `sudo wireshark`
     - choose the vm network interface. (eth0)
     - start capturing
     - run nmap scans
     - stop capturing.
  8. Apply capture filter for Cowrie traffic
     ```
     ip.addr == <ubuntuip>
     tcp.port == 2222
     ```
     <img width="1132" height="587" alt="image" src="https://github.com/user-attachments/assets/364886b5-75ce-455f-b4f4-28637593161b" />
  9. Test SSH connection `ssh -p 2222 user@<target-ip>`
  10. Observe the cowrie log to see the login attempts.
      ```
      cd ~/Desktop/cowrie/var/log/cowrie
      cat log
      ```
      <img width="808" height="485" alt="Screenshot 2026-03-04 115122" src="https://github.com/user-attachments/assets/18b4d243-954d-4bd7-a11c-1d25be0dbfbb" />

## Result
1. Cowrie successfully ran on port 2222
2. Nmap detected port open
3. Wireshark captured SSH handshake traffic





  
