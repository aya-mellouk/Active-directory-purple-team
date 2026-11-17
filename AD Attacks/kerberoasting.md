# Kerberoasting Attack

## What is it?

Kerberoasting is an attack against Active Directory service accounts. Any authenticated domain user can request a Kerberos service ticket (TGS) for any account that has a Service Principal Name (SPN) set. Part of that ticket is encrypted with the service account's own password hash, so an attacker can grab that ticket and crack it **offline**. If that  service account have excessive priveleges, it can lead to privelage escalation  ( CVE-2022-37966 )

## Step 1 : Request the TGS (ticket)

From the attack machine, request a service ticket for every account that has an SPN, using a domain account:

```bash
impacket-GetUsersSPNs domainname/user:password -dc-ip <DC_IP> -request -outputfile hashes.txt
```

Example output:

<img width="1287" height="122" alt="image" src="https://github.com/user-attachments/assets/4f1fa373-9366-456b-9ddd-c1ddfef40a1d" />



## Step 2 : Crack the ticket offline with hashcat

Pick the hashcat mode that matches the ticket's encryption type:

| Ticket encryption type | Hashcat mode |
|---|---|
| RC4 (etype 23)   | 13100 |
| AES128 (etype 17) | 19600 |
| AES256 (etype 18) | 19700 |

Example (RC4 ticket):

<img width="648" height="82" alt="image" src="https://github.com/user-attachments/assets/7d682cac-2099-4d4e-a962-c1f037f631a9" />


If the service account's password is weak, hashcat recovers it in plaintext.
<img width="1120" height="150" alt="image" src="https://github.com/user-attachments/assets/564852d5-32ec-4c55-808f-2c95b95ecaf7" />


## Step 3 : Impact

The 'sqlservice' account had a weak password and excessive privileges. After recovering its password through Kerberoasting, an attacker could gain administrator-level access and potentially move further across the domain.

## Key takeaways 

- Kerberoasting itself isn't the vulnerability, the real risk comes from:
  - Manually-set, weak/short service account passwords (crackable offline)
  - Service accounts carrying more privilege than they need
  - Use of weak ticket encryption (RC4) instead of AES

