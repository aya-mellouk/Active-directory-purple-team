# Password Spraying attack

## What is it
it is an attack that attempts to authenticate to multiple user object in the domain using a single password, or a list of known used password to avoid detection and lockout. This technique is particularly effective against organisations that reuse passwords. If malicious actors compromise a user object via password spraying, then they control the user object and inherit the user object’s access and privileges.

## Step 1: identify open ports and create a list of domain usernames 

<img width="507" height="417" alt="image" src="https://github.com/user-attachments/assets/51566367-3126-4ebb-bf3d-a480a6e82cb0" />

## Step 2 : perform the attack with the chosen tool

```bash
crackmapexec smb 192.168.75.162 -u users.txt -d tsuquiat.local -p password.txt --continue-on-success --delay 2 | grep '\[+\]'
```

<img width="777" height="112" alt="image" src="https://github.com/user-attachments/assets/c9e9bc9e-7064-48e6-b339-1bba96b37be1" />

## Key takeaways
-This attack succeeded based on the following conditions:
 -Weak/absent lockout policy : no effective threshold or lockout window, so all accounts could be tested without triggering a block.
 -Predictable, shared, or default passwords : 4 accounts share the identical password ncc1701.
 -No MFA : password alone was sufficient to authenticate.
 -Valid, enumerable username list : users.txt gave full coverage of domain accounts to target.
