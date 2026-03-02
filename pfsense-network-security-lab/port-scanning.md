# Attack Simulation: Port Scanning

## Objective
Simulate reconnaissance activity from an external attacker and observe firewall behavior.

## Attacker Profile
- System: Kali Linux
- Network Position: WAN
- Tool Used: Nmap

## Attack Method
- SYN scan targeting internal LAN subnet

## Expected Outcome
- No open ports discovered
- Hosts appear filtered or unreachable
- Scan attempts blocked by firewall

## Observed Result
- pfSense blocked scanning attempts
- Firewall logs recorded blocked traffic

## SOC Relevance
- Demonstrates reconnaissance detection
- Confirms perimeter firewall effectiveness
- Provides actionable logs for analysis
