The Windows client `authentication` process uses multiple `modules` that handle `logon`, `credential retrieval`, and `verification`. Among the available mechanisms, `Kerberos` is one of the most common and complex. The `Local Security Authority (LSA)` is a protected `subsystem` that authenticates `users`, manages local `logins`, enforces `security policies`, and translates between `usernames` and `security identifiers (SIDs)`.

The `security subsystem` maintains local `security policies` and `user accounts` on a system. On a `Domain Controller`, these policies and accounts apply to the entire `domain` and are stored in `Active Directory`. The `LSA subsystem` also provides services for `access control`, `permission checks`, and generating `security audit` messages.

![[{DF0FF2BB-56AC-4E1D-8B47-463CCE62DF1F}.png]]

---
Local `interactive logon` relies on several coordinated `components`: the `logon process (WinLogon)`, the `logon user interface process (LogonUI)`, `credential providers`, the `Local Security Authority Subsystem Service (LSASS)`, one or more `authentication packages`, and either the `Security Accounts Manager (SAM)` or `Active Directory`.

In this context, `authentication packages` are `Dynamic-Link Libraries (DLLs)` that perform the actual `authentication` checks. For example, for `non-domain-joined` and `interactive logins`, the `Msv1_0.dll` `authentication package` is typically used.

`WinLogon` is a trusted system process responsible for managing security-related user interactions, such as:

- Launching `LogonUI` to prompt for credentials at login
- Handling password changes
- Locking and unlocking the workstation

To obtain a `user's account name and password`, `WinLogon` relies on `credential providers` installed on the system. These credential providers are `COM` objects implemented as `DLLs.`

`WinLogon` is the only `process` that intercepts `login requests` from the `keyboard`, received through `RPC messages` from `Win32k.sys`. During `logon`, it launches the `LogonUI` application to display the graphical `user interface`. After the `credential provider` collects the user’s `credentials`, `WinLogon` forwards them to the `Local Security Authority Subsystem Service (LSASS)` for `authentication`.

---
## LSASS

The `Local Security Authority Subsystem Service (LSASS)` is made up of multiple `modules` and manages all `authentication` processes. Located at `%SystemRoot%\System32\Lsass.exe`, it enforces the `local security policy`, authenticates `users`, and forwards `security audit logs` to the `Event Log`. In short, `LSASS` acts as the gatekeeper for `Windows` operating systems. A detailed overview of the `LSASS architecture` can be found online for deeper reference.

| **Authentication Packages** | **Description**                                                                                                                                                                                                                                                              |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Lsasrv.dll`                | The `LSA Server service` both enforces `security policies` and acts as the` security package manager` for the `LSA`. The `LSA` contains the Negotiate function, which selects either the` NTLM or Kerberos protoco`l after determining which `protocol` is to be successful. |
| `Msv1_0.dll`                | `Authentication `package for local machine logons that don't require custom authentication.                                                                                                                                                                                  |
| `Samsrv.dll`                | The `Security Accounts Manager (SAM)` stores local security accounts, enforces locally stored `policies, and supports APIs.`                                                                                                                                                 |
| `Kerberos.dll`              | `Security packag`e loaded by the `LSA` for `Kerberos-based authentication` on a machine.                                                                                                                                                                                     |
| `Netlogon.dll`              | Network-based logon service.                                                                                                                                                                                                                                                 |
| `Ntdsa.dll`                 | This library is used to create new records and folders in the `Windows registry.`                                                                                                                                                                                            |

---
## SAM database

The `Security Account Manager (SAM)` is a `database file` in `Windows` that stores `user account credentials`. It authenticates both `local` and `remote users` and uses `cryptographic protections` to prevent unauthorized access. `User passwords` are stored as `hashes` in the registry, typically as `LM` or `NTLM` hashes. The `SAM file` is located at `%SystemRoot%\system32\config\SAM` and mounted under `HKLM\SAM`. Viewing or accessing this file requires `SYSTEM` privileges.

`Windows systems` can operate in either a `workgroup` or a `domain`. In a `workgroup`, the system manages the `SAM database` locally, storing all existing `user accounts` in it. In a `domain` setup, however, the `Domain Controller (DC)` validates credentials against the `Active Directory` database (`ntds.dit`), located at `%SystemRoot%\ntds.dit`.

To strengthen protection against offline cracking of the `SAM database`, Microsoft introduced `SYSKEY (syskey.exe)` in `Windows NT 4.0`. When enabled, `SYSKEY` encrypts portions of the `SAM file` on disk, ensuring that `password hashes` for all local accounts are protected with a system-generated `key`.

#### Credential Manager

![[{E91E5E07-A6F2-408C-87C4-0614603A06BE}.png]]
`Credential Manager` is a built-in `Windows` feature that lets `users` store and manage `credentials` for accessing `network resources`, `websites`, and `applications`. These saved `credentials` are stored per `user profile` in the user’s `Credential Locker`. The encrypted `credentials` are located at:

```powershell
PS C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\
```

---

## NTDS

It’s common to find `network environments` where `Windows systems` are joined to a `Windows domain`. This setup enables centralized `management`, allowing `administrators` to efficiently oversee all systems within the organization. In these environments, `logon requests` are sent to `Domain Controllers` within the same `Active Directory forest`. Each `Domain Controller` stores a file named `NTDS.dit`, which is synchronized across all `Domain Controllers`, except for `Read-Only Domain Controllers (RODCs)`.

`NTDS.dit` is a database file that stores Active Directory data, including but not limited to:

- User accounts (username & password hash)
- Group accounts
- Computer accounts
- Group policy objects

