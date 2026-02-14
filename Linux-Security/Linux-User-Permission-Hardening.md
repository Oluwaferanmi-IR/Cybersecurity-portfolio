# Linux User and Permission Hardening

## Overview
This project demonstrates practical techniques for creating linux users and hardening Linux user permissions to reduce unauthorized access, privilege abuse, and security misconfigurations. It focuses on applying the **principle of least privilege** to ensure only users with right amount of authorization can access information.

## Objective
- Understand Linux file and directory permissions
- Harden user and group access
- Restrict unauthorized privilege escalation
- Secure sensitive system files
- Demonstrate Linux hardening techniques 

## Tools Used
- Linux Command Line Interface
- Kali Linux

## Permission Basics
### File Permission Structure
```text
drwxr-x---
│ │ │ │
│ │ │ └── Others permissions
│ │ └──── Group permissions
│ └────── User (Owner) permissions
└──────── File type (DIRECTORY)
```
r ----------- read
w ----------- write
x ----------- execute

## Methodology
**- Creating a new user** 
 To create new user, we run `sudo useradd Marvel`

<img width="641" height="126" alt="new user" src="https://github.com/user-attachments/assets/339cc50e-ef9a-435c-8e90-b76ac20139cc" />

**- Creating a password for the new user**
Adding a password to the new user we run `sudo passwd Marvel`

  <img width="648" height="222" alt="new user password" src="https://github.com/user-attachments/assets/2af265eb-c73e-40f3-84be-b2204b4b172f" />


 - Randomly creating new user doesn't give the new user sudo privilege just a random privilege. To give sudo privileges to the new user we run `sudo usermod -aG sudo Marvel` 

**- Remove unnecessary sudo access**

  <img width="650" height="57" alt="sudo access" src="https://github.com/user-attachments/assets/5ea4e716-161a-43bc-92b9-6a8ca7b9a39c" />

**- Switch user and Verify sudo access: it shows what commands (if any) the new user can run with sudo. From this example, the new user has no sudo privilege**

  <img width="496" height="173" alt="switch user" src="https://github.com/user-attachments/assets/4104f753-8276-4bc6-aff5-cae98de43ebf" />

**- Restrict access to critical system files**

  <img width="650" height="93" alt="restrict usermod" src="https://github.com/user-attachments/assets/684ab3c2-560a-4695-9660-f4235b5d018d" 
/>
- /etc/shadow stores password hashes
- chmod 640:changes the ways users can interact with the password hashes
     - Owner (root): read/write
     - Group (shadow): read
     - Others: no access
- chown root:shadow sets proper ownership
  
**-confirm the change**

   <img width="639" height="201" alt="permission changed" src="https://github.com/user-attachments/assets/6a253ce5-7ac0-4988-8d69-6ff4697adbcd" />

**-** Hardening File Permissions (configuration files)

   <img width="673" height="59" alt="harden permission" src="https://github.com/user-attachments/assets/2fad4458-1958-4420-827f-28faccfca503" />


**- Find insecure permissions: This identifies files writable by anyone (security risk)
**
   <img width="449" height="48" alt="Screenshot 2026-01-26 023208" src="https://github.com/user-attachments/assets/56e6fb90-0d7d-4a7d-9881-11643e719133" />

**- Edit sudoer safely: run **sudo visudo****
  
  <img width="636" height="449" alt="sudo visudo" src="https://github.com/user-attachments/assets/8fa33dc2-85cb-4f13-ba98-c8dde61c2d8e" />


## Findings
- Default permissions may allow unauthorized access
- Excessive sudo privileges increase risk

## Security Risks Addressed
- Unauthorized file access
- Excessive user privileges
- Weak permission configurations
- Privilege escalation
- Insider threats

