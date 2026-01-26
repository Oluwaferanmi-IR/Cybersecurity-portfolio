# Web Content Enumeration Using DIRB

##  Overview
This project demonstrates the use of **DIRB**, a web content enumeration tool, to discover hidden directories, files, and security misconfigurations on a target web server.  The exercise focuses on **web reconnaissance**, a critical phase in penetration testing and vulnerability assessments.
The scan revealed multiple high-risk misconfigurations, including exposed directories, sensitive configuration files, and an accessible phpMyAdmin installation.

---

##  Tools & Technologies
- **Operating System:** Kali Linux  
- **Tool:** DIRB v2.22  
- **Web Server:** Apache 
- **Protocol:** HTTP
- **Attacked Site:** DVWA 
- **Wordlist:** /usr/share/dirb/wordlists/common.txt`

---

##  Objectives
- Perform directory and file enumeration on a web server  
- Identify exposed or sensitive web resources  
- Interpret HTTP response codes  
- Document security risks and mitigation strategies  

---

##  Methodology

- DIRB was executed using its default wordlist on the test website: **dirb http://10.199.181.232** It generated 4,612 test words and began scanning the target for accessible directories and files
  
  <img width="1236" height="691" alt="dirb tool" src="https://github.com/user-attachments/assets/87c19c0d-afba-46de-8a9c-475da4d88096" />
  
The scan identified multiple publicly accessible directories:
- svn/
- config/
- docs/
- external/
- phpmyadmin/

<img width="649" height="515" alt="dirb directories" src="https://github.com/user-attachments/assets/b9b5b2c9-1905-4618-b88f-384bb0bd27ab" />

 Risk result from the scan:
**- svn/ may expose source code
- config/ may contain credentials or secrets
- phpmyadmin/ provides direct database access**

## **Sensitive Files Found**

| File            | HTTP Code | Risk                          |
| --------------- | --------- | ----------------------------- |
| `php.ini`       | 200       | Server configuration exposure |
| `phpinfo.php`   | 302       | PHP environment disclosure    |
| `server-status` | 200       | Apache internal details       |
| `robots.txt`    | 200       | Reveals hidden paths          |


<img width="644" height="518" alt="dirb php" src="https://github.com/user-attachments/assets/3985f9d9-6ee3-4b42-8437-6da95c482bbc" />

## **Security Impact**
The findings indicate high-risk web server misconfigurations, including:
- Information disclosure
- Increased attack surface
- Potential database compromise
- Weak access control

## **Recommended Mitigations**
- Disable directory listing in the web server configuration
- Remove phpMyAdmin from public access or restrict it by IP
- Remove sensitive files (php.ini, phpinfo.php) from web root
- Apply proper access control and authentication
- Regularly audit web servers for exposed resources
