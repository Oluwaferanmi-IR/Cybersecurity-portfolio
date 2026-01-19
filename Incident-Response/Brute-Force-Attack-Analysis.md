# SSH Brute Force Attack Analysis

## Objective
To detect and analyze brute force login attempts and demonstrate basic incident response actions.

## Tools Used
- Linux system logs
- SSH
- Firewall utilities

## Scenario
Multiple failed SSH login attempts were observed from a single external IP address, indicating a brute force attack.

## Detection Method
1. Reviewed authentication logs located at /var/log/auth.log
2. Identified repeated failed login attempts
3. Correlated timestamps and source IP addresses

## Indicators of Compromise (IOCs)
- High volume of failed login attempts
- Repeated authentication failures from the same IP

## Response Actions
- Blocked malicious IP address using firewall rules
- Enforced strong password policies
- Reviewed SSH configuration for security improvements

## Conclusion
This project demonstrates basic threat detection, log analysis, and incident response techniques required for SOC and blue-team roles.
