# Operation Endgame - TryHackMe Write-Up

## Overview
This write-up documents my approach to the TryHackMe **Operation Endgame** room, which focused on enumerating an Active Directory environment, abusing Kerberos-related weaknesses, identifying credential exposure, and escalating privileges to full domain compromise.

## Objectives
- Perform reconnaissance against the target
- Enumerate valid domain users
- Identify accounts vulnerable to Kerberoasting
- Crack recovered ticket hashes
- Validate credentials and identify password reuse
- Analyze privilege escalation paths in Active Directory
- Access the target with recovered credentials
- Retrieve the final flag

## Skills Demonstrated
- Nmap service enumeration
- SMB enumeration
- RID brute-force user enumeration
- Kerberoasting
- Targeted Kerberoasting
- Password hash cracking
- Credential validation
- BloodHound privilege path analysis
- RDP access and post-exploitation
- Active Directory privilege escalation

## Tools Used
- `Nmap`
- `CrackMapExec`
- `Impacket`
- `John the Ripper`
- `BloodHound`
- `xfreerdp`

## Attack Path Summary
1. Identified the target as a Windows Server 2019 Domain Controller
2. Enumerated domain users through RID brute-force using the guest account
3. Performed Kerberoasting against a service account and cracked the recovered hash
4. Identified password reuse and gained access to another user account
5. Used BloodHound to identify a viable privilege escalation path
6. Performed targeted Kerberoasting to recover additional credentials
7. Discovered exposed credentials in a script on the target
8. Logged in with the recovered account and obtained the final flag

## 1. Initial Reconnaissance

### Goal
Identify the target services, operating system, and possible attack surface.

### Action
Performed an Nmap scan against the target to enumerate open ports and exposed services.

```bash
nmap -sC -sV -Pn -oN nmap.txt TARGET_IP
```

### Result
The scan showed the target was a Windows Server 2019 Domain Controller with services commonly associated with Active Directory, including Kerberos, LDAP, SMB, and DNS.


### Why it mattered
This confirmed that the environment was domain-based and helped guide the next steps for enumeration and authentication attacks.

## 2. User Enumeration

### Goal
Identify valid domain user accounts that could be targeted in later attacks.

### Action
Used SMB and RID brute-force enumeration through the guest account to discover domain users.

### Result
Recovered a list of valid domain usernames for further testing and Kerberos-based attacks.

### Why it mattered
Having valid usernames was necessary for targeted enumeration, Kerberoasting, password spraying checks, and later privilege escalation analysis.

## 3. Kerberoasting

### Goal
Identify service accounts with SPNs that could be roasted.

### Action
Used Impacket to request service tickets for accounts associated with SPNs.

### Result
Recovered a Kerberos ticket hash for a target account.

### Why it mattered
This allowed offline password cracking, which led to valid credentials and further access in the domain.

## 4. Password Reuse and Account Access

### Goal
Check whether the recovered password was reused by other domain users.

### Action
Tested the cracked credentials against additional accounts using CrackMapExec.

### Result
Confirmed password reuse and gained access to another valid user account.

### Why it mattered
This expanded access in the domain and opened the path toward privilege escalation.

## 5. BloodHound Analysis

### Goal
Identify a viable privilege escalation path within the Active Directory environment.

### Action
Used BloodHound to analyze relationships, group memberships, and attack paths between compromised accounts and more privileged users.

### Result
Identified a path that could be abused to move closer to administrative access.

### Why it mattered
This helped focus the next steps and showed which account or permission to target for privilege escalation.

## 6. Targeted Kerberoasting

### Goal
Recover credentials for a more valuable account identified during the privilege escalation process.

### Action
Performed targeted Kerberoasting against the identified account to request a crackable service ticket.

### Result
Recovered a Kerberos ticket hash and cracked it offline to obtain additional credentials.

### Why it mattered
This provided access to a more privileged account and moved the attack closer to full domain compromise.

## 7. Credential Discovery

### Goal
Search the target system for exposed credentials or sensitive files that could assist with further access.

### Action
Reviewed accessible files and scripts on the compromised system to identify hardcoded credentials.

### Result
Discovered exposed credentials stored in a script on the target.

### Why it mattered
These credentials provided another access path and helped progress toward domain-level compromise.

## 8. Privilege Escalation and Flag Retrieval

### Goal
Use the recovered credentials to access the target with higher privileges and complete the objective.

### Action
Logged in with the recovered account and used the available privileges to access the final objective.

### Result
Obtained the final flag and achieved full compromise of the environment.

### Why it mattered
This confirmed the full attack path from initial enumeration to successful privilege escalation in the Active Directory environment.
