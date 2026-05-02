As a Soc analyst intern at HSC consult, I was given the following task to do a cloud forensic investigation
- Task Overview: https://docs.google.com/document/d/16LhNsj7Etu0WlGgB-PLlXm1necLDarmd/editusp=sharing&ouid=114133189131518812175&rtpof=true&sd=true
- Log to analyse: 


## REPORT

Analyst: Olajide Oluwaferanmi Marvellous

Role: Lead Cloud forensic Engineer

Date of Incident 2026-04-20 

Date of Report 2026-04-30 

--- 

## EVENT SUMMARY
Our organization’s AWS environment has been hit by a sophisticated actor. This actor obtained a valid IAM Access Key (AKIA_DEV_PROD_9982) and used it to conduct a multistage attack for over approximately three hours (01:15–04:50 UTC, April 20 2026). The attack progressed from initial reconnaissance through privilege escalation, data exfiltration, backdoor creation, and an attempted evasion of audit logging. The attacker operated using  two distinct IP addresses, escalated an existing user to full administrator, exfiltrated sensitive financial documents and database credentials, created a backdoor IAM user with administrator access, and finally attempted to disable CloudTrail logging but was not successful. The original stolen access key was then deactivated to erase evidence of the initial compromise vector. 

---

### COMPLETE TIMELINE OF THE BREACH

| TIMELINE | API CALL           | DETAILS           |
|----------|--------------------|-------------------|
|01:15:33|sts:GetCallerIdentity |Attacker confirms stolen key is valid. IP: 190.45.112.3 |
|01:25:44|iam:GetAccountSummary |Maps account structure and IAM configuration 
|01:40:00|s3:ListBuckets |Discovers all S3 buckets in account 
|02:05:45|iam:ListUserPolicies |Checks policies on target user dev_user_musa 
|02:20:00|iam:ListAttachedUserPolicies |Deeper IAM recon on dev_user_musa 
|02:30:10|sts:GetSessionToken |Generates session token for temporary credentials 
|02:40:00|cloudtrail:DescribeTrails |Maps audit logging configuration 
|02:50:33|iam:PutUserPolicy |Attaches FullAdmin (Action:* Resource:*) to dev_user_musa 
|03:00:15|s3:ListObjects |Browses top-secret-financials-2026 bucket 
|03:02:11|secretsmanager:GetSecretValue|Steals db_pass: P@ssw0rd_Cloud_2026! 
|03:10:44|s3:GetObject |Downloads ceo_personal_taxes_2025.pdf 
|03:15:22|iam:CreateUser |Creates backdoor user support_service_backup 
|03:25:33|iam:AttachUserPolicy |Gives backdoor user AdministratorAccess 
|03:35:44|iam:CreateAccessKey |Creates key for backdoor user. IP switches to 45.33.10.11 
|03:40:00|cloudtrail:StopLogging |Attempts to disable CloudTrail — BLOCKED (AccessDenied) 
|04:05:44|sts:GetCallerIdentity |Backdoor key AKIA_FAKE_BACKDOOR_KEY_7721 confirms it works 
|04:50:33|iam:UpdateAccessKey |Deactivates original stolen key AKIA_DEV_PROD_9982 

---


## DELIVERABLES

### 1. Credential Discovery:

Identify the Access Key ID that was stolen and used for the bulk of the attack. THE ACCESS KEY ID THAT WAS STOLEN WAS AKIA_DEV_PROD_9982 

|Attribute |Value |
|----------|------|
|Compromised Access Key ID |AKIA_DEV_PROD_9982 
|First Malicious Use |2026-04-20T01:15:33Z 
|Last Action with this Key |2026-04-20T03:40:00Z (StopLogging attempt) 
|Deactivated By Attacker At |2026-04-20T04:50:33Z 
|Deactivated Using |AKIA_FAKE_BACKDOOR_KEY_7721 (backdoor key)|

### 2. IP Forensic Tracking:
   
- What was the Initial Attacker IP?  190.45.112.3
  
|Field |Value|
|------|-----|
|IP Address |190.45.112.3 
|First Seen | 2026-04-20T01:15:33Z 
|User Agent |aws-cli/2.0.30 
|Key Used |AKIA_DEV_PROD_9982 
|First Action |GetCallerIdentity (STS) — identity confirmation


- What was the Second IP used for persistence? 45.33.10.11
  
|Field|Value |
|-----|------|
|IP Address |45.33.10.11 
|First Seen (Timestamp) |2026-04-20T03:35:44Z 
|User Agent |Mozilla/5.0 (X11; Linux x86_64) 
|Key Used |AKIA_DEV_PROD_9982 (then AKIA_FAKE_BACKDOOR_KEY_7721) 
|First Action |CreateAccessKey for support_service_backup 

- Provide the timestamp of the first action taken from the second IP: *2026-04-20T03:35:44Z*

  
### 3. Obfuscated Data Analysis (Signal vs. Noise):  

There are two distinct Base64 encoded strings in the logs. Decode both.  Which one is legitimate and which is not?  What can you deduce from the decoded strings 

The strings were decoded using cyberchef

The first encoded string S3 PutObject 01:23:45 was:
Encoded: c2V0dGluZzogYmFja3VwX2VuYWJsZWQ9dHJ1ZQ==
Decoded: setting: backup_enabled=true (This is noise)


The second encoded string GetSecretValue (03:02:11 UTC) was:
Encoded: ZGJfcGFzczogUEBzc3cwcmRfQ2xvdWRfMjAyNiE=
Decoded: setting: db_pass: P@ssw0rd_Cloud_2026! (This is a signal)

|    |String 1 |String 2 |
|----|---------|---------|
|Location |S3 PutObject — requestParameters| Secrets Manager — responseElements 
|Decoded (cyberchef)| setting: backup_enabled=true | db_pass: P@ssw0rd_Cloud_2026! 
|Actor |DevOps-Deployer (internal service) | AKIA_DEV_PROD_9982 (attacker) 
|IP |10.0.1.55 (internal) | 190.45.112.3 (external attacker) 
|Verdict | LEGITIMATE — config file write | MALICIOUS — credential theft 
|Action Required |None |URGENT: Rotate db_pass immediately 

### 4. Administrative Escalation: 

Identify the specific timestamp and API call where the attacker granted themselves full administrative privileges. 

|Field | Value|
|------|------|
|Timestamp| 2026-04-20T02:50:33Z 
|API Call| iam:PutUserPolicy 
|Access Key | AKIA_DEV_PROD_9982 |
|Source Ip| 190.45.112.3


### 5. The Theft: 
Which specific file was downloaded from S3? Provide the Bucket name and the File name. 

|Field|Value|
|-----|-----|
|Timestamp| 2026-04-20T03:10:44Z 
|API Call |s3:GetObject 
|Bucket Name| top-secret-financials-2026 
|File / Key Name| ceo_personal_taxes_2025.pdf 
|Access Key Used |AKIA_DEV_PROD_9982


### 6. Persistence & Evasion: *Name the new Backdoor User created by the attacker.*

##### The attacker created a backdoor user named “support_service_backup “

|Field|Value|
|-----|-----|
|Timestamp| 2026-04-20T03:15:22Z 
|API Call |iam:CreateUser 
|Backdoor Username| support_service_backup 
|Source IP | 190.45.112.3 
|Policy Attached| arn:aws:iam::aws:policy/AdministratorAccess (03:25:33Z) 
|New Backdoor Key| AKIA_FAKE_BACKDOOR_KEY_7721 



### 7. What was the final action the attacker took against the original stolen data? Why would an attacker do this? 

*The attacker's final significant action was to deactivate the original stolen access key using the new backdoor key. He did this as an act of evidence destruction because if  we detect the stolen key and investigats, deactuvating the key amkes it aopear the key was probably decommisioned.*

|Field|Value|
|-----|-----|
|Timestamp| 2026-04-20T04:50:33Z 
|API Call |iam:UpdateAccessKey 
|Key Deactivated| AKIA_DEV_PROD_9982 (set to Inactive) 
|Executed By| AKIA_FAKE_BACKDOOR_KEY_7721 (backdoor key) 
|Source IP| 45.33.10.11 
