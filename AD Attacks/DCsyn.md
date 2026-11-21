# DCsyn attack

## What it is?
Domain Controllers (DCs) in an Active Directory environment synchronize directory information with each other using the Microsoft Directory Replication Service Remote Protocol (MS-DRSR).
DCSync is an attack technique that abuses this legitimate replication mechanism to retrieve sensitive Active Directory information, including password hashes, without directly accessing the Domain Controller's NTDS.dit database.
To perform a DCSync attack, an account must have the appropriate directory replication permissions:
- Replicating Directory Changes
- Replicating Directory Changes All
- Replicating Directory Changes In Filtered Set (required in certain scenarios, particularly when retrieving specific filtered attributes).
By default, these permissions are generally assigned to highly privileged groups, such as Domain Admins and Enterprise Admins.
However, if these permissions are mistakenly granted to an ordinary domain user or compromised account, an attacker can abuse them to request password hashes from the Domain Controller.
This can lead to a complete domain compromise, especially if the attacker retrieves the krbtgt account hash, which can be used to forge Kerberos Golden Tickets and establish persistence.

## Step 1 : check for replication priveleges 
In this example, an ordinary domain user has replication privileges, representing a serious Active Directory misconfiguration.
<img width="1110" height="338" alt="image" src="https://github.com/user-attachments/assets/fa0799ee-a9d3-4a20-9ccf-d75d680dec36" />

## Step 2 : perform the attack using impacket-secretsdump
secretsdumb.exe is used for dcsyn attack, it impersonates a legitimate domain controller over the MS-DRSR replication protocol and asks the real DC to replicate its directory data, including password hashes for every account
```bash
impacket-secretsdump domain.com/username@DC ip -outputfile dcsyn.txt
```
<img width="657" height="112" alt="image" src="https://github.com/user-attachments/assets/a780fd2b-8537-47b0-aa7e-782906a28d77" />

results:
retreiving all the hashes from the dc without touching it or attacking ntds.dit, a simple account with hight privelages can lead to a full domain compromise, those hashes can be used for other attacks such as golden ticket targeting the krbtgt account, or pass-the-hash
<img width="800" height="330" alt="image" src="https://github.com/user-attachments/assets/af2d6d41-5fd1-4108-b1c0-5dd3919a0950" />




