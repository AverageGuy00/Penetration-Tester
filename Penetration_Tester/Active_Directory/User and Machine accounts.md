User accounts are created on `local systems` or in `Active Directory (AD)` to let people or programs log in and access resources. When a user logs in, the system verifies their `password` and issues an `access token` that contains their `security identity` and `group memberships`. This token is presented whenever the user interacts with processes.

Accounts let employees or contractors log in, allow services to run under a specific `security context`, and manage access to files, applications, or network shares. `Groups` make administration easier by assigning privileges once to the group instead of each user individually.

In `AD`, account provisioning and management is essential. Most users have at least one account, but IT staff may have multiple. `Service accounts` are common for applications and background tasks. An organization with 1,000 employees may have 1,200+ accounts.

Companies also keep many `disabled accounts` for audit purposes, often placed in OUs like `FORMER EMPLOYEES`, ensuring records are retained while privileges are removed.

---

# Local Accounts

Local accounts are stored on `local systems` (a standalone server or workstation). Rights granted to these accounts apply only on that host — they are `security principals` but can control access **only locally**.

`Administrator`: SID `S-1-5-domain-500`; the first account created at installation with near-complete control of the system. It cannot be deleted (but can be disabled or renamed). Windows 10 / Server 2016 disable the built-in `Administrator` by default and create another local account in the local `Administrators` group during setup.

`Guest`: disabled by default; intended for temporary, limited logins. Historically has a blank password by default — keep it disabled to avoid anonymous access risks.

`SYSTEM` (NT AUTHORITY`SYSTEM`): the OS’s internal service account used to run many processes and services. It has permissions over almost everything on the host, does not have a regular user profile, does not appear in `User Manager`, and cannot be added to groups. Achieving `SYSTEM` is the highest local privilege on a Windows host.

`Network Service`: a predefined account used by the Service Control Manager (SCM) for services; when a service runs as `Network Service`, it presents credentials to remote services.

`Local Service`: another predefined SCM account with minimal local privileges; it presents anonymous (minimal) credentials to the network.

---

# Domain Users

`Domain users` differ from `local users` in that their rights are granted by the `domain`. This allows them to access resources such as file servers, printers, intranet hosts, and other objects, based on permissions assigned to their `user account` or their `group memberships`.

Unlike local accounts, a `domain user` can log in to any host in the domain.

A key account to remember in `Active Directory` is the `KRBTGT` account. This is a built-in service account for the **Key Distribution Service** that handles authentication and access to domain resources.

The `KRBTGT` account is a **high-value target** for attackers because control of it enables **unconstrained access** to the domain. It can be abused for `privilege escalation` and `persistence` through attacks such as the `Golden Ticket` attack.

---
 
# User Naming Attributes

Security in `Active Directory (AD)` can be strengthened by using consistent user naming attributes to identify `user objects`. These attributes help with both authentication and administration.

| `UserPrincipalName` (UPN) |                                                                                     This is the primary logon name for the user. By convention, the UPN uses the email address of the user.                                                                                     |
| :-----------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|       `ObjectGUID`        |                                                                   This is a unique identifier of the user. In AD, the ObjectGUID attribute name never changes and remains unique even if the user is removed.                                                                   |
|     `SAMAccountName`      |                                                                                             This is a logon name that supports the previous version of Windows clients and servers.                                                                                             |
|        `objectSID`        |                                                                 The user's Security Identifier (SID). This attribute identifies a user and its group memberships during security interactions with the server.                                                                  |
|       `sIDHistory`        | This contains previous SIDs for the user object if moved from another domain and is typically seen in migration scenarios from domain to domain. After a migration occurs, the last SID will be added to the `sIDHistory` property, and the new SID will become its `objectSID` |
### Common User Attributes

```powershell-session
PS C:\htb Get-ADUser -Identity htb-student

DistinguishedName : CN=htb student,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
Enabled           : True
GivenName         : htb
Name              : htb student
ObjectClass       : user
ObjectGUID        : aa799587-c641-4c23-a2f7-75850b4dd7e3
SamAccountName    : htb-student
SID               : S-1-5-21-3842939050-3880317879-2865463114-1111
Surname           : student
UserPrincipalName : htb-student@INLANEFREIGHT.LOCAL
```

---

# Domain-joined vs. Non-Domain-joined Machines

`Domain-joined hosts` have centralized management via a `Domain Controller (DC)`. They receive updates, policies, and configurations through `Group Policy`. Users can log in and access resources from **any host in the domain**, not just their primary workstation. This setup is standard in enterprise environments where centralized control and resource sharing are essential.

`Non-domain joined hosts` (workgroup computers) are not managed by domain policy. Resource sharing beyond the local network is more difficult. This model is common for home PCs or small business LAN clusters. Users manage their own systems, and accounts only exist locally on each machine — profiles do not migrate between workgroup hosts.

---

# Machine Accounts in AD

A `machine account` running under `NT AUTHORITY\SYSTEM` in an `Active Directory (AD)` environment has many of the same rights as a standard `domain user account`.

This means an attacker does not always need valid `user credentials` to start enumerating or attacking the domain.

- `SYSTEM` access to a `domain-joined host` can be obtained via remote code execution or local privilege escalation
- While often seen only as a way to steal local data (passwords, SSH keys, sensitive files), `SYSTEM` access actually provides **read access across the domain**.
- This makes it an excellent **staging point** for reconnaissance and enumeration before launching further `AD-related attacks`.

