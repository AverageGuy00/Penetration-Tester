
`Rights and privileges` are the cornerstones of `AD management` and, if mismanaged, can easily lead to abuse by attackers or penetration testers. `Access rights` and `privileges` are two important topics in `AD` (and `infosec` in general), and we must understand the difference.

- `Rights` are typically assigned to users or groups and deal with permissions to access an object such as a file.
- `Privileges` grant a user permission to perform an action such as run a program, shut down a system, reset passwords, etc.

`Privileges` can be assigned individually to users or conferred upon them via built-in or custom group membership. Windows computers have a concept called `User Rights Assignment`, which, while referred to as rights, are actually types of `privileges` granted to a user.

---

# Built-in AD Groups

`AD` contains many default or built-in `security groups`, some of which grant their members powerful `rights` and `privileges` which can be abused to escalate privileges within a `domain` and ultimately gain `Domain Admin` or `SYSTEM privileges` on a `Domain Controller (DC)`.

|              Group Name              |                                                                                                                                                                                    Description                                                                                                                                                                                    |
| :----------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|         `Account Operators`          |      Members can create and modify most types of accounts, including those of users, local groups, and global groups, and members can log in locally to domain controllers. They cannot manage the Administrator account, administrative user accounts, or members of the Administrators, Server Operators, Account Operators, Backup Operators, or Print Operators groups.       |
|           `Administrators`           |                                                                                                                           Members have full and unrestricted access to a computer or an entire domain if they are in this group on a Domain Controller.                                                                                                                           |
|          `Backup Operators`          | Members can back up and restore all files on a computer, regardless of the permissions set on the files. Backup Operators can also log on to and shut down the computer. Members can log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, which, if taken, can be used to extract credentials and other juicy info. |
|             `DnsAdmins`              |                                                                                                    Members have access to network DNS information. The group will only be created if the DNS server role is or was at one time installed on a domain controller in the domain.                                                                                                    |
|           `Domain Admins`            |                                                                                                                        Members have full access to administer the domain and are members of the local administrator's group on all domain-joined machines.                                                                                                                        |
|          `Domain Computers`          |                                                                                                                                           Any computers created in the domain (aside from domain controllers) are added to this group.                                                                                                                                            |
|         `Domain Controllers`         |                                                                                                                                                 Contains all DCs within a domain. New DCs are added to this group automatically.                                                                                                                                                  |
|           `Domain Guests`            |                                                                                                     This group includes the domain's built-in Guest account. Members of this group have a domain profile created when signing onto a domain-joined computer as a local guest.                                                                                                     |
|            `Domain Users`            |                                                                                                                         This group contains all user accounts in a domain. A new user account created in the domain is automatically added to this group.                                                                                                                         |
|         `Enterprise Admins`          |    Membership in this group provides complete configuration access within the domain. The group only exists in the root domain of an AD forest. Members in this group are granted the ability to make forest-wide changes such as adding a child domain or creating a trust. The Administrator account for the forest root domain is the only member of this group by default.    |
|         `Event Log Readers`          |                                                                                                                             Members can read event logs on local computers. The group is only created when a host is promoted to a domain controller.                                                                                                                             |
|    `Group Policy Creator Owners`     |                                                                                                                                                        Members create, edit, or delete Group Policy Objects in the domain.                                                                                                                                                        |
|       `Hyper-V Administrators`       |                                                                          Members have complete and unrestricted access to all the features in Hyper-V. If there are virtual DCs in the domain, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.                                                                          |
|             `IIS_IUSRS`              |                                                                                                                                           This is a built-in group used by Internet Information Services (IIS), beginning with IIS 7.0.                                                                                                                                           |
| `Pre–Windows 2000 Compatible Access` |                                        This group exists for backward compatibility for computers running Windows NT 4.0 and earlier. Membership in this group is often a leftover legacy configuration. It can lead to flaws where anyone on the network can read information from AD without requiring a valid AD username and password.                                        |
|          `Print Operators`           |                                          Members can manage, create, share, and delete printers that are connected to domain controllers in the domain along with any printer objects in AD. Members are allowed to log on to DCs locally and may be used to load a malicious printer driver and escalate privileges within the domain.                                           |
|          `Protected Users`           |                                                          Members of this [group](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#protected-users) are provided additional protections against credential theft and tactics such as Kerberos abuse.                                                          |
|    `Read-only Domain Controllers`    |                                                                                                                                                             Contains all Read-only domain controllers in the domain.                                                                                                                                                              |
|        `Remote Desktop Users`        |                                                                                                              This group is used to grant users and groups permission to connect to a host via Remote Desktop (RDP). This group cannot be renamed, deleted, or moved.                                                                                                              |
|      `Remote Management Users`       |                                                                                                       This group can be used to grant users remote access to computers via [Windows Remote Management (WinRM)](https://docs.microsoft.com/en-us/windows/win32/winrm/portal)                                                                                                       |
|           `Schema Admins`            |                                                          Members can modify the Active Directory schema, which is the way all objects with AD are defined. This group only exists in the root domain of an AD forest. The Administrator account for the forest root domain is the only member of this group by default.                                                           |
|          `Server Operators`          |                                                                                                   This group only exists on domain controllers. Members can modify services, access SMB shares, and backup files on domain controllers. By default, this group has no members.                                                                                                    |

---

# User Rights Assignment

Depending on their `group` membership and `Group Policy (GPO)`, users can be granted `rights` or `privileges` that attackers can abuse. If you can write a `GPO` applied to an `OU` you control (for example via `SharpGPOAbuse`), you can assign high‑impact `rights` like `SeDebugPrivilege`, `SeBackupPrivilege`, or `SeRestorePrivilege`, grant `Log on as a service`/`SeServiceLogonRight`, add accounts to `Restricted Groups` or privileged `group` membership, or deploy a `scheduled task`/startup script that runs as `SYSTEM` — all of which enable escalation (dump `NTDS.dit`, dump `LSASS`, create service accounts, etc.). Audit writable `GPOs`, monitor changes to `member` attributes on `privileged groups`, and alert on assignment of dangerous `User Rights Assignment` entries.

|            Privilege            |                                                                                                                         Description                                                                                                                          |
| :-----------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| `SeRemoteInteractiveLogonRight` |                                     This privilege could give our target user the right to log onto a host via `Remote Desktop (RDP)`, which could potentially be used to obtain sensitive data or escalate privileges.                                      |
|       `SeBackupPrivilege`       | This grants a user the ability to create system backups and could be used to obtain copies of sensitive system files that can be used to retrieve passwords such as the `SAM` and `SYSTEM Registry hives` and the `NTDS.dit` Active Directory database file. |
|       `SeDebugPrivilege`        |              This allows a user to debug and adjust the memory of a process. With this privilege, attackers could utilize a tool such as Mimikatz to read the memory space of the `LSASS` process and obtain any credentials stored in memory.               |
|    `SeImpersonatePrivilege`     |          This privilege allows us to impersonate a token of a privileged account such as `NT AUTHORITY\SYSTEM`. This could be leveraged with a tool such as JuicyPotato, RogueWinRM, PrintSpoofer, etc., to escalate privileges on a target system.          |
|     `SeLoadDriverPrivilege`     |                                                         A user with this privilege can load and unload device drivers that could potentially be used to escalate privileges or compromise a system.                                                          |
|   `SeTakeOwnershipPrivilege`    |                          This allows a process to take ownership of an object. At its most basic level, we could use this privilege to gain access to a `file share` or a file on a share that was otherwise not accessible to us.                           |

---

# Viewing a User's Priviliges

After logging into a host, typing the command `whoami /priv` will give us a listing of all user rights assigned to the current user. Some rights are only available to administrative users and can only be listed/leveraged when running an elevated `CMD` or `PowerShell` session. These concepts of elevated rights and `User Account Control (UAC)` are security features introduced with `Windows Vista` that default to restricting applications from running with full permissions unless absolutely necessary. If we compare and contrast the rights available to us as an admin in a `non-elevated` console vs. an `elevated` console, we will see that they differ drastically. First, let's look at the rights available to a `standard Active Directory user`.

### Standard Domain User's Rights

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

We can see that the rights are very limited, and none of the "dangerous" rights outlined above are present. Next, let's take a look at a privileged user. Below are the rights available to a `Domain Admin` user.

### Domain Admin Rights Non-Elevated

In a `non-elevated` `CMD` or `PowerShell` session a `Domain Admin` often looks like a regular user because `User Account Control (UAC)` keeps high‑impact `rights` gated until you run an `elevated` shell. This is by design (least privilege): normal processes don’t get full admin `privileges` unless explicitly elevated. To see the true effective rights for an account, run `whoami /priv` in an `elevated` shell — that’s where `Domain Admin` level `rights` appear.

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

### Domain Admin Rights Elevated

If we enter the same command from an elevated `PowerShell` console, we can see the complete listing of rights available to us.

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeMachineAccountPrivilege                 Add workstations to domain                                         Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Enabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeEnableDelegationPrivilege               Enable computer and user accounts to be trusted for delegation     Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled
```


---


`User rights` scale with `group` membership and explicit `privileges`.  

A `Backup Operators` member often has UAC‑gated rights (e.g., `SeBackupPrivilege`) that aren’t active in a standard shell, but `whoami /priv` will show `SeShutdownPrivilege` even non‑elevated — meaning they can shut down a `Domain Controller (DC)` if they log on locally (not via `RDP` or `WinRM`).  
This shutdown ability won’t directly expose secrets, but it’s a high‑impact denial or disruption vector; always audit writable `groups` and UAC‑gated `User Rights Assignment`.

### Backup Operator Rights

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

In `Active Directory`, `rights` granted via built-in `security groups` can significantly impact both attack and defense. Low-privileged users added to these groups can escalate access or compromise the `domain`. Best practice: keep most built-in `groups` empty, only add accounts for specific tasks, and ensure these accounts have strong `passwords`/`passphrases` and are separate from daily-use `sysadmin` accounts. Monitor any membership or extra `privileges` closely.

