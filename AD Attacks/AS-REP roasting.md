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
This flag lives in `userAccountControl` → `DONT_REQUIRE_PREAUTH`.


## Step 1 : Request the AS-REP

```
impacket-GetNPUsers -usersfile userwordlist -request -dc-ip <ipv4> tsuquiat.local/
```



## Step 2: Crack it offline

```bash
hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt
```

If the password is weak, hashcat cracks it and gives the cleartext password.

## Condition for it to work

- Only works if the password is weak/crackable, or the ticket uses a weak encryption type (RC4)

## Bonus way in (ACL abuse)

If you have `GenericWrite` / `GenericAll` on a target user:
- You can flip their `userAccountControl` to disable preauth yourself
- Run the ASREPRoast attack
- Reset the flag back after

