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
- Search for "IAM" in the top search bar
- Click on IAM (Identity and Access Management)
- In IAM Dashboard, click "Users" in left sidebar
- Click "Create user" button
- Enter:

   - User name: vulnerable-user
   -  Check "Provide user access to the AWS Management Console" (optional)
   -  Click Next
- Set permissions:

  - Select "Attach policies directly"
  - Don't attach any policies yet (we'll create and attach the vulnerable policy first)
  - Click Next
- Click "Create user"
- Create access keys:

  - Click on the new user
  - Go to "Security credentials" tab
  - Scroll to "Access keys" section
  - Click "Create access key"
  - Select "Command Line Interface (CLI)"
  - Click "Create access key"
  - SAVE these credentials: `access kwy` and `secret key`

    <img width="1127" height="395" alt="image" src="https://github.com/user-attachments/assets/103357dc-a6b6-4c39-af8b-fd757d7948c9" />
    

  - Download .csv file.

    <img width="1132" height="573" alt="image" src="https://github.com/user-attachments/assets/d3a7eff7-de47-4821-b0de-237c41074712" />

---
## VULNERABLE IAM POLICY

Once the user has the created, the next thing to do is to create a vulnerable IAM policy for the created user
- In IAM Dashboard, click "Policies" in left sidebar
- Click "Create policy" button
- Go to JSON tab and paste this policy:
  ```{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:RunInstances",
                "ec2:CreateTags",
                "iam:PassRole",
                "iam:CreatePolicy",
                "iam:CreatePolicyVersion",
                "iam:AttachUserPolicy",
                "iam:ListUsers",
                "iam:ListPolicies"
            ],
            "Resource": "*"
        }
    ] 
- Click "Next: Tags" (skip tags)
- Click "Next: Review"
- Set:

  - Name: `VulnerableEC2Policy`
  - Description: `Vulnerable policy for privilege escalation demo`
  - Click `Create policy`
- Attach the policy to the user

  - Go to "Users" → Click "vulnerable-user"
  - Go to "Permissions" tab
  - Click "Add permissions" → "Attach policies directly"
  - Search for VulnerableEC2Policy (the policy we created earlier)
  - ✅ Check the policy
  - Click "Next", then "Add permissions"
---
## PRIVILEGE ESCALATION

The vulnerability was exploited and least privilege was put in place, to do that, we use the aws cli
on kali:
- install aws cli

  - `sudo apt update`
  - `sudo apt install awscli -y`

  <img width="623" height="371" alt="Screenshot 2026-03-25 130410" src="https://github.com/user-attachments/assets/00ee0d52-7cb6-409c-b3cb-760d7855c29e" />


- configure the cli `aws configure` enter:

  - access key: it was saved earlier
  - secret key: it was saved earlier
  - region; us-east
  - output format: json

<img width="587" height="228" alt="Screenshot 2026-03-25 132104" src="https://github.com/user-attachments/assets/e9e04863-0688-4a1c-a8ea-8f88706e475d" />

- confirm current permission `aws iam list-attached-user-policies --user-name lowpriv-user`
- perform privilege escalation by attaching admin policy to the vuilnerable user
  ```
  aws iam attach-user-policy \
  --user-name lowpriv-user \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess```
- Verify the escalation `aws iam list-attached-user-policies --user-name lowpriv-user`
- you should see' Administratoraccess': this guve the misconfigured user full control of the AWS Account
- To Proof of Privilege Escalation, run only what an admin that run `aws iam list-users`

<img width="636" height="310" alt="Screenshot 2026-03-25 132201" src="https://github.com/user-attachments/assets/bd61395e-73f9-4f51-bd0c-ba8434021413" />

---
## LEAST PRIVILEGE
To mediate this, we perform a least priviliege policy, giving the user access to just what it needs and revoking the ability to escalate funtion.
- Remove the Admin Access
  ```
  aws iam detach-user-policy \
  --user-name vuln-user \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
- Detach the misconfiguration policy
  ```
  aws iam detach-user-policy \
  --user-name vuln-user \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
- Return to the web UI
- Go to policies
- add new policy
- go to the json tab
- write this policy:
  ```
  {
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iam:GetUser",
        "iam:ListUsers"
      ],
      "Resource": "*"
    }
  ]
this policy makes privilege escalation impossible
- Name it leastprivilege policy
- Go to the IAM dashboard
- click on the user
- Go to permission tab
- Remove the misconfiguration added earler
- Add permmission
- search `leastprivilegepolicy`
- Add it.
- The policy has been updated
- Test the fix to make sure everything works perfectly

  - Return to aws cli
  - with the vulnerable user, try to add administrative access
    ```
    aws iam attach-user-policy \
    --user-name vuln-user \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
  - You should get an access denied prompt. This indicated that the user can n longer escalate it privilege.
    
    <img width="643" height="160" alt="Screenshot 2026-03-25 134157" src="https://github.com/user-attachments/assets/8fc6dfae-f966-459e-9bab-72bf4d58a6bf" />

    












