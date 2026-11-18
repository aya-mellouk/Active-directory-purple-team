# AS-REP Roasting attack

## What is it?

Some AD users can be set to **not require Kerberos pre-authentication**.

Normally, Kerberos works like this:
- Client sends AS-REQ with pre-auth (encrypted timestamp with user pass hash)
- KDC checks it, if valid sends back AS-REP (with the TGT)

If pre-auth is disabled on an account:
- Client sends AS-REQ
- KDC skips the pre-auth check
- KDC sends AS-REP anyway

The AS-REP is encrypted with the user's password hash, any user in the domain can request it, even unprivileged ones, and crack it offline.
this option is registered  in `userAccountControl` → `DONT_REQUIRE_PREAUTH`.
<img width="612" height="591" alt="image" src="https://github.com/user-attachments/assets/095e371a-fbfa-445d-bff2-2c1e8cf4330e" />


## Step 1 : Request the AS-REP

```
impacket-GetNPUsers -usersfile userwordlist -request -dc-ip <ipv4> tsuquiat.local/
```
<img width="1277" height="98" alt="image" src="https://github.com/user-attachments/assets/afd14709-dda8-45a0-ac70-1c8744daa695" />

## Step 2: Crack it offline

<img width="1127" height="75" alt="image" src="https://github.com/user-attachments/assets/a7a18698-c3f7-41f9-8666-3a19fc80a052" />

If the password is weak, hashcat cracks it and gives the cleartext password.

## Condition for it to work

- Only works if the password is weak/crackable, or the ticket uses a weak encryption type (RC4)

## Bonus way in (ACL abuse)

If you have `GenericWrite` / `GenericAll` on a target user:
- You can flip their `userAccountControl` to disable preauth yourself
- Run the ASREPRoast attack
- Reset the flag back after

