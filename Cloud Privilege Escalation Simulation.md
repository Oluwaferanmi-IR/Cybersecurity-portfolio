###   CLOUD PRIVILEGE ESCALATION
---
## OVERVIEW
This lab simulates a cloud privilege escalation scenario using AWS IAM. A misconfigured IAM user (vuln-user) was created with excessive permissions, allowing an attacker to escalate privileges to full administrative access. The exercise demonstrates both exploitation and remediation using the principle of least privilege. The objectives include:
- Create AWS IAM user with misconfigured permissions
- Perform privilege escalation 
- Fix with least privilege configuration
---
## CREATING THE MISCONFIGURED USER USING AWS MANAGEMENT CONSOLE (WEB UI)
- Go to `https://console.aws.amazon.com`
- Login as root user or an admin IAM user. (If you don't already have an account, create one)
