# pfSense Installation

## Environment
- Hypervisor: VMware
- pfSense Version: 2.6.0
- Network Interfaces: 2 (WAN + LAN)
- Kali Linux / virtual windows machine

## Network Adapter Configuration
- WAN Interface: NAT
- LAN Interface: Host-Only

## Installation Steps
1. Download the pfSense ISO from [Netgate](https://www.pfsense.org/download/).  
2. Create a new VM:
   - OS Type: **FreeBSD 13 or later (64-bit)**  
   - Memory: 2GB+ recommended  
   - Disk: 20GB+
   - Add another network

     - Go to the vm settings
     - click add
     - click network
     - click custom or you choose host only
     - Add vmnet2
  
      <img width="755" height="712" alt="Screenshot 2026-02-28 153607" src="https://github.com/user-attachments/assets/9ff0d175-48f1-4d3f-ad42-87f0241f2b1a" />
      
     - Finish


3. Attach the ISO as a boot disk and install pfSense.
   follow the image guide below for the installation.

   <img width="925" height="517" alt="Screenshot 2026-02-28 153901" src="https://github.com/user-attachments/assets/52b2ebe5-e0f9-4046-a20a-91a615b9134f" />

   <img width="923" height="517" alt="Screenshot 2026-02-28 153928" src="https://github.com/user-attachments/assets/98eb0888-b559-44d0-9733-7dc17515286d" />

   <img width="919" height="510" alt="Screenshot 2026-02-28 154008" src="https://github.com/user-attachments/assets/e9824f4d-c3f3-4db5-8eaa-fbe58a304644" />

   <img width="925" height="519" alt="Screenshot 2026-02-28 154028" src="https://github.com/user-attachments/assets/11593bf9-2ae2-4dec-a013-969954f677b1" />

   <img width="925" height="515" alt="Screenshot 2026-02-28 154052" src="https://github.com/user-attachments/assets/50c67e1d-7010-4e07-80e8-8996abc84641" />

   <img width="917" height="524" alt="Screenshot 2026-02-28 154126" src="https://github.com/user-attachments/assets/a4b6e666-be99-4b14-bd1b-cd43ed0b0307" />

   <img width="917" height="516" alt="Screenshot 2026-02-28 154153" src="https://github.com/user-attachments/assets/36a5016d-cf7a-44e0-8057-3207f9ed112d" />

   <img width="916" height="515" alt="Screenshot 2026-02-28 154315" src="https://github.com/user-attachments/assets/ab524879-cf5c-4442-aaf8-b43c3a70dd23" />

   <img width="925" height="518" alt="Screenshot 2026-02-28 154353" src="https://github.com/user-attachments/assets/cc6ef0f7-0ee0-4224-8796-5e01cb386796" />

   <img width="924" height="521" alt="Screenshot 2026-02-28 154420" src="https://github.com/user-attachments/assets/9a3a74a5-550b-40c9-94b2-8cb9a8d760fc" />

   <img width="921" height="512" alt="Screenshot 2026-02-28 154501" src="https://github.com/user-attachments/assets/7e2d8ece-15eb-447e-a7c8-3870612cdec3" />

   <img width="923" height="516" alt="Screenshot 2026-02-28 154525" src="https://github.com/user-attachments/assets/1da1c8c9-676e-4b41-b461-fe810c43a473" />

4. The installation begins

   <img width="923" height="525" alt="Screenshot 2026-02-28 171047" src="https://github.com/user-attachments/assets/d2e7ea9b-c418-40e6-adac-c25bb4773a47" />

5. Once the installation is done, enter the shell by pressing 8
6. Run the following codes one after the other to get your ip address
   ```
   pfctl
   pfctl -d
   ifconfig
   exit
   ```

<img width="917" height="520" alt="image" src="https://github.com/user-attachments/assets/a6857a76-8217-47e2-824f-c04102740595" />

7. Go to your kali or windows browser to access the webgui
8. On the url page, input the ip address you got from the pfsense
9.  The default log in details is
    ```
    username : admin
    password : pfsense
    
    ```

   <img width="1290" height="695" alt="Screenshot 2026-03-01 201532" src="https://github.com/user-attachments/assets/ab84a104-4fef-4a75-86cc-0a92f45f76e6" />
10. Follow the log in prompt till you get to the dashbord.

<img width="1359" height="614" alt="Screenshot 2026-03-02 164817" src="https://github.com/user-attachments/assets/9fd7a51d-3100-44e3-9153-9ecc4e19f760" />

11.  Configure LAN/WAN interfaces and admin credentials.
12.  Installation is complete

