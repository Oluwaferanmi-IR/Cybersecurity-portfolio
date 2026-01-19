# Network Traffic Analysis Using Wireshark

## Objective
The objective of this project is to capture and analyze network traffic in order to understand normal network behavior and identify potential security risks such as unencrypted communication and suspicious traffic patterns.

## Tools Used
- Kali Linux
- Wireshark

## Environment
- Local network
- Live packet capture on active network interface

## Methodology
1. Launched Wireshark on Kali Linux
2. Selected the active network interface
3. Captured live network traffic
4. Applied display filters including:
   - tcp
   - udp
   - dns
   - http
5. Inspected packet details and protocol behavior

## Findings
- Observed TCP three-way handshake processes
- Identified DNS queries to external servers
- Detected HTTP traffic transmitting data in plaintext
- Verified normal network communication patterns

## Security Implications
Unencrypted protocols such as HTTP can expose sensitive information to attackers through packet sniffing and man-in-the-middle attacks.

## Conclusion
This project demonstrates the importance of network traffic monitoring and how
packet analysis can be used to identify insecure communication and improve
network security posture.
