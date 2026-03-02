# IDS/IPS Implementation with Suricata
Suricata is an open-source Network Intrusion Detection System (NIDS) / Intrusion Prevention System (IPS) tool. It’s a tool that watches network traffics to detect / block malicious activity and threats.

## Objective
Detect and analyze malicious network activity using an intrusion detection system.

## Tool Used
- Suricata IDS/IPS

## Installation steps
- Log in to pfSense WebGUI: `https://<ipaddr we got abovw>`
- Navigate to System → Package Manager → Available Packages
- Search for Suricata → Click Install
- After installation, the Suricata menu appears under Services → Suricata

## Configuration
- Add Interface:

  - Go to Interfaces → +Add
  - Select WAN interface for monitoring external traffic
- Mode: IDS (Detection only)

  - IDS = alerts only, does not block traffic
- Rules:

  - Choose Emerging Threats Open (ET Open)
  - Download and update rules via the Suricata GUI

## Test detection
- Open Kall VM
- Run Nmap SYN scan `nmap -sS -p 1-1000 <pfSense WAN IP`
- Monitor Suricata alerts:

  - Go to Services → Suricata → Logs → Alerts
  - Look for:

    - `ET SCAN NMAP` alerts
    - TCP scan signatures
    - Reconnaissance attempts
      
Result: Suricata logs detected traffic but did not block it yet.

## IPS Mode
- Go back to Suricata interface and Edit the WAN interface
- Switch Mode from IDS → IPS (Inline)
- Apply the configuration and restart Suricata
- Suricata will now actively block malicious traffic : Nmap scans should be stopped

## Verify the new IPS Protection
- From Kali, try the same nmap SYN scan again

  - In IPS mode, the scan should fail
  - Suricata alerts will now also show Blocked actions

- Malicious traffic actively blocked
- Repeat scans prevented

