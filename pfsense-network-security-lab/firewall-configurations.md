# Firewall Rules Configuration

## Objective
Restrict internal network traffic to essential services while blocking unnecessary and potentially dangerous traffic.

## Default Behavior
- WAN: Block all incoming traffic
- LAN: Allow all outbound traffic

## Custom Rules Implemented (LAN)
1. Allow outbound web traffic (TCP ports 80 and 443)
2. Block all other outbound traffic

## Testing Results
- Web browsing successful from LAN
- ICMP, SSH, and other protocols blocked
- Blocked traffic visible in firewall logs

## Security Impact
- Enforces least-privilege networking
- Limits lateral movement
- Reduces exposure to attacks
