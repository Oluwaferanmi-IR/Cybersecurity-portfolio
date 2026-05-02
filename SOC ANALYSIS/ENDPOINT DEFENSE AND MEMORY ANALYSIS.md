As a SOC analyst intern at HSC consult, i was given the following tasks and provided with the following scan. The process of solving the task is listed in this project.
- TASK : https://docs.google.com/document/d/1fkNUAoyq0IJ0-9p4es81rKhyMKhJIwxADEkcQEMgC0c/edit?usp=sharing
- Memory Forensic Dataset_ Malfind Output: https://docs.google.com/document/d/1-ecs22vPC7e8F1XE5bYGG_fUtZfeRJxk/edit?usp=sharing&ouid=114133189131518812175&rtpof=true&sd=true
- Memory Forensic Dataset_ Network Scan: https://docs.google.com/document/d/1fT3oDmyOjtAi8puUPAF4cWOj_feNcwaG/edit?usp=sharing&ouid=114133189131518812175&rtpof=true&sd=true
- Memory Forensic Dataset_ Process Tree: https://docs.google.com/document/d/1QP9ktFKt4KETgpZKsooYpwRSb_JlhRan/edit?usp=sharing&ouid=114133189131518812175&rtpof=true&sd=true

---

Analyst: Oluwaferanmi Olajide 

Role: Senior SOC Tier 2 Analyst 

Date: April 24, 2026 

Analysis Type: Memory Forensics

Affected Department: Finance 

## 1. SUMMARY 

A workstation in the finance department triggered a suspicious network connection alert. Local antivirus found no malicious files on disk, prompting a memory dump analysis. Three volatility 
forensic reports were examined: 
1. pstree_output (Process Tree Analysis)  
2. malfind_result (Memory Injection/In-memory artifacts)  
3. netscan_results ( Network Connection artifacts ) 
The analysis confirmed a compromise. A malicious process disguising itself as a legitimate Windows system binary (svchost.exe) was found running in memory with an injected portable executable (PE) file, maintaining an active command and control connection to an external IP address. The attack was fileless (nothing was written to the disk); this is the reason the local antivirus did not detect anything COMPROMISED PROCESS By cross-referencing the process tree (pstree) with the network scan (netscan) using PID as the unique identifier. The following process was identifed to be the carrier of the malware Process Name 

|       |        |
|-------|--------|
|Process Name|svchost.exe 
|ID |3880 
|Session  | 1
|Start time | 2026-03-10 14:46:12 
|Threads | 5 
|Network activity | ESTABLISHED connection to 185.112.55.20:443 
|Memory Anomaly | MZ header + PAGE_EXECUTE_READWRITE  

## 2. PARENT-CHILD PROCESS ANALYSIS 

From pstree, the parent of PID 3880 (svchost.exe) has the following attributes: 

|      |       |
|------|-------|
|Parent process name | notepad.exe 
|Parent PID (PPID) |2550 
|Parent start time | 2026-03-10 14:45:01 
|Child start time |2026-03-10 14:46:12 

- The parent-child process analysis is architecturally significant because in a standard Windows environment, svchost.exe is exclusively spawned by services.exe. This is a fundamental architectural rule of the Windows OS. The two legitimate svchost instances in the memory dump had PIDs of 720 and 884. 


## 3. MEMORY EVIDENCE 

- ### Memory protection flag:

*PAGE_EXECUTE_READWRITE* - Normal memory regions are either readable (for data) or writable (for code). It is never bothe 
simultaneously.PAGE_EXECUTE_READWRITE grants WRITE and EXECUTE permissions in the same memory region. This is an example of injected shellcode or a reflectively loaded PE file being written and then executed 

- ### Magic byte:

*4D 5A (MZ Header)* - Every windows executable file (.exe or .dll) begins with the two bytes 0x4D 0x5A, which render as the ASCII characters "MZ.". The presence of an MZ header at memory address 0X4000000 inside a running svchost.exe 
process means a complete portable executable (PE) file was loaded directly into RAM

- ### C2 IDENTIFICATION
   
From netscan output and filtering by PID 3880:  I was able to filter the following values out


|          |      |
|----------|------|
|Source IP |192.168.1.10
|Source port | 49552 
|Destination IP | 185.112.55.20 
|Destination port | 443 (HTTPS)
|Connection state | ESTABLISHED 
|process | Svchost.exe with PID: 3880) 


## 4. ATTACK CLASSIFICATION 
Based on the totality of evidence across all the forensic artifacts, this attack is classified as "process hollowing." Process hollowing is a sophisticated, fileless, memory-resident attack technique. 


## 5. Recommended remediation

|Actions|Reasons|
|-------|-------|
|Isolate the Finance workstation from the network immediately | Active C2 channel must be severed to prevent data exfiltration or lateral movement. 
|Block IP 185.112.55.20 at the perimeter firewall.| Prevent re-establishment of C2 from any other potentially compromised host. 
|Check all other Finance workstations for svchost.exe processes with non-services.exe parents | Determine if lateral movement has occurred.
|Review how notepad.exe wasused as the initial execution vector. | Identify the root cause. It is most likely a malicious document or macro. 
|Reset credentials of the user logged into the workstation | Assume credentials may have been harvested by the attack so it is only safe to reset credentials. 

