`NTLM Authentication` is an authentication method in Active Directory that uses `LM` and `NT` hashes with the `NTLMv1` and `NTLMv2` protocols. `LM` and `NT` are the stored password hash types, while `NTLMv1` and `NTLMv2` are the challenge/response protocols that rely on those hashes. `LM` and `NTLMv1` are weak and vulnerable to attacks, while `NTLMv2` is stronger but still susceptible to `relay` and `pass-the-hash` abuse. `Kerberos` is generally preferred over NTLM for security.

#### Hash Protocol Comparison

| **Hash/Protocol** |             **Cryptographic technique**              | **Mutual Authentication** |        **Message Type**         |             **Trusted Third Party**             |
| :---------------: | :--------------------------------------------------: | :-----------------------: | :-----------------------------: | :---------------------------------------------: |
|      `NTLM`       |              Symmetric key cryptography              |            No             |          Random number          |                Domain Controller                |
|     `NTLMv1`      |              Symmetric key cryptography              |            No             |     MD4 hash, random number     |                Domain Controller                |
|     `NTLMv2`      |              Symmetric key cryptography              |            No             |     MD4 hash, random number     |                Domain Controller                |
|    `Kerberos`     | Symmetric key cryptography & asymmetric cryptography |            Yes            | Encrypted ticket using DES, MD5 | Domain Controller/Key Distribution Center (KDC) |

# LM

`LAN Manager (LM)` hashes are the oldest Windows password storage mechanism, introduced in `1987` with `OS/2`. They are stored in the `SAM` database on local hosts and in `NTDS.DIT` on Domain Controllers. LM uses weak hashing: passwords are limited to `14 characters`, not case sensitive, and converted to uppercase, reducing the keyspace to `69 characters`. Before hashing, passwords are split into two `7-character chunks`, each turned into a `DES` key and encrypted with the string `KGS!@#$%`. The two ciphertext blocks are concatenated to form the LM hash. This design means cracking requires brute-forcing only `7 characters twice` instead of the full password, making LM hashes easy to break with tools like `Hashcat`. LM has been disabled by default since `Vista/Server 2008`, but may still exist in older environments.

# NTHash (NTLM)

`NT LAN Manager (NTLM)` is a `challenge-response authentication protocol` used in modern Windows systems. Authentication involves three messages: the client sends a `NEGOTIATE_MESSAGE`, the server responds with a `CHALLENGE_MESSAGE`, and the client replies with an `AUTHENTICATE_MESSAGE`. NTLM hashes are stored locally in the `SAM` database or in the `NTDS.DIT` on a Domain Controller. NTLM supports two hash types: the legacy `LM hash` and the stronger `NT hash`, which is generated as `MD4(UTF-16-LE(password))`.

# NTLM Authentication Request

![NTLM protocol diagram: Client sends NTLM Negotiate, Challenge, and Authenticate messages to Server (DC). Server responds with Netlogon network and validation info.](https://academy.hackthebox.com/storage/modules/74/ntlm_auth.png)

- `Client` sends `NEGOTIATE_MESSAGE` to `Server (DC)` announcing supported NTLM features.
- `Server` replies with `CHALLENGE_MESSAGE` (a random nonce).
- `Client` computes response using stored hash (`LM` or `NT` where `NT = MD4(UTF-16-LE(password))`) and sends `AUTHENTICATE_MESSAGE`.
- `Server` looks up the account hash from `SAM` or `NTDS.DIT`, computes expected response, and compares it to the client’s `AUTHENTICATE_MESSAGE`.
- If the responses match, authentication succeeds and the server proceeds to session setup.
- `Client` sends `Netlogon_network_info` to the DC with network/session details.
- `Server` responds with `Netlogon_Validation_SAM_Info` containing account validation data (group membership, privileges, tokens).
- `Client` receives validation info and the local session/context is built (access granted to resources as permitted).
- Notes: `NTLM` is vulnerable to `pass-the-hash` (reuse of the `NT` hash) and `relay` attacks; offline cracking with `Hashcat` is feasible for short/weak passwords.

An NT hash takes the form of `b4b9b02e6f09a9bd760f388b67351e2b`, which is the second half of the full NTLM hash. An NTLM hash looks like this:

```powershell
Rachel:500:aad3c435b514a4eeaad3b935b51304fe:e46b9e548fa0d122de7f59fb6d48eaa2:::
```

Looking at the hash above, we can break the NTLM hash down into its individual parts:

- `Rachel` is the username
- `500` is the Relative Identifier (RID). 500 is the known RID for the `administrator` account
- `aad3c435b514a4eeaad3b935b51304fe` is the LM hash and, if LM hashes are disabled on the system, can not be used for anything
- `e46b9e548fa0d122de7f59fb6d48eaa2` is the NT hash. This hash can either be cracked offline to reveal the clear-text value (depending on the length/strength of the password) or used for a pass-the-hash attack. Below is an example of a successful pass-the-hash attack using the [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) tool:

```bash
OathBornX@htb[/htb]$ crackmapexec smb 10.129.41.19 -u rachel -H e46b9e548fa0d122de7f59fb6d48eaa2

SMB         10.129.43.9     445    DC01      [*] Windows 10.0 Build 17763 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         10.129.43.9     445    DC01      [+] INLANEFREIGHT.LOCAL\rachel:e46b9e548fa0d122de7f59fb6d48eaa2 (Pwn3d!)
```

# NTLMv1 (Net-NTLMv1)

`NTLMv1` is the older `NTLM` variant that uses both the `NT` and `LM` hashes in its challenge/response. The `Server` sends an `8-byte` `challenge` (nonce), the `Client` computes a `24-byte` response derived from the hash(s) and that challenge, and returns it in the `AUTHENTICATE_MESSAGE`. The Net-`NTLMv1` response is a concatenation of three DES encryptions produced from 7-byte chunks of the relevant hash material, which is why it’s weaker and easier to crack offline after capture (tools like `Responder` often surface these). Important: `Net-NTLMv1` responses **cannot** be used directly for `pass-the-hash` (they’re challenge/response values, not reusable NT hash tokens), but they are highly crackable and enable offline recovery of the underlying password/hash. TL;DR: `NTLMv1` = `8-byte challenge` → `24-byte response` based on `LM/NT` hash chunks via DES; old, weak, crackable — avoid it.

## V1 Challenge & Response Algorithm

```bash
C = 8-byte server challenge, random
K1 | K2 | K3 = LM/NT-hash | 5-bytes-0
response = DES(K1,C) | DES(K2,C) | DES(K3,C)
```

An example of a full NTLMv1 hash looks like:

## NTLMv1 Hash Example

```powershell
u4-netntlm::kNS:338d08f8e26de93300000000000000000000000000000000:
9526fb8c23a90751cdd619b6cea564742e1e4bf33006ba41:cb8086049ec4736c
```

`NTLMv1` was the building block for modern `NTLM authentication`. Like any protocol, it has flaws and is susceptible to cracking and other attacks. Now let us move on and take a look at `NTLMv2` and see how it improves on the foundation that version one set.

# NTLMv2 (Net-NTLMv2)

`NTLMv2` (introduced in `Windows NT 4.0 SP4`, default since `Server 2000`) is a hardened replacement for `NTLMv1`. It uses `HMAC-MD5` and a client-generated `blob` (which contains a `timestamp`, a `client nonce` and other metadata) to produce the authentication response. On an `8-byte` server `challenge`, the client returns an `NTLMv2` response that is essentially `HMAC-MD5(NTLMv2_hash, server_challenge || blob)` plus the `blob` itself so the server can validate the timestamp and nonce. A second LMv2-style response (where used) provides compatibility. The inclusion of the `client challenge` and `timestamp` increases entropy and helps mitigate simple replay attacks, making `NTLMv2` far stronger than `NTLMv1`. Caveats: `NTLMv2` can still be cracked offline against weak passwords (but is much harder than NTLMv1), and it remains susceptible to `relay` and some active network attacks unless protections like `SMB signing`, `LAPS/NLA`, or network-level mitigations are in place.

## V2 Challenge & Response Algorithm

```bash
SC = 8-byte server challenge, random
CC = 8-byte client challenge, random
CC* = (X, time, CC2, domain name)
v2-Hash = HMAC-MD5(NT-Hash, user name, domain name)
LMv2 = HMAC-MD5(v2-Hash, SC, CC)
NTv2 = HMAC-MD5(v2-Hash, SC, CC*)
response = LMv2 | CC | NTv2 | CC*
```

## NTLMv2 Hash Example

```powershell
admin::N46iSNekpT:08ca45b7d7ea58ee:88dcbe4446168966a153a0064958dac6:5c7830315c7830310000000000000b45c67103d07d7b95acd12ffa11230e0000000052920b85f78d013c31cdb3b92f5d765c783030
```

We can see that developers improved upon `v1` by making `NTLMv2` harder to crack and giving it a more robust algorithm made up of multiple stages. We have one more authentication mechanism to discuss before moving on. This method is of note to us because it does not require a persistent network connection to work.

---

# Domain Cached Credentials (MSCache2)

`Domain Cached Credentials (DCC / MSCache2)` store recent domain login hashes on a machine so users can log in when the `Domain Controller` is unreachable. Hosts save up to the last `10` domain user hashes under `HKEY_LOCAL_MACHINE\SECURITY\Cache`. The cached entries use the `MSCache2` format and look like: `$DCC2$10240#username#<hash>`. These cached hashes `cannot be used for pass-the-hash` and are `slow to crack` (even with GPUs), so offline cracking is usually impractical unless the password is weak or the attack is extremely targeted. An attacker must have `local admin` on the host to extract them, and because they’re meant for offline logon resilience, they’re a different threat model than NTLM/kerberos hashes — worth collecting during assessments but often not worth spending huge cracking time on unless you have a strong reason.