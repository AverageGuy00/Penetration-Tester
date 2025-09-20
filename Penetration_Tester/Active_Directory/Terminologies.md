# `Object`

An object can be defined as ANY resource present within an Active Directory environment such as OUs, printers, users, domain controllers, etc

# `Attributes`

Every object in Active Directory has an associated set of [attributes](https://docs.microsoft.com/en-us/windows/win32/adschema/attributes-all) used to define characteristics of the given object. A computer object contains attributes such as the hostname and DNS name. All attributes in AD have an associated LDAP name that can be used when performing LDAP queries, such as `displayName` for `Full Name` and `given name` for `First Name`.

# `Schema`

`Schema` in Active Directory is the blueprint of the environment. It defines what object types (like `user`, `computer`) can exist and their attributes (some required, some optional). When an object is created from a class, it’s called _instantiation_, and the object is an _instance_. For example, `RDS01` is an instance of the `computer` class in AD.

```
Schema = AD's blueprint  
- Defines object classes (e.g., `user`, `computer`)  
- Each class has attributes (required or optional)  
- Creating an object = instantiation  
- Result = instance of that class  

Example:  
Class: computer  
Instance: RDS01 (with attributes like hostname, OS version, etc.)
```

# `Domain

A domain is a logical group of objects such as computers, users, OUs, groups, etc. We can think of each domain as a different city within a state or country. Domains can operate entirely independently of one another or be connected via trust relationships.

# `Forest`

A forest is a collection of Active Directory domains. It is the topmost container and contains all of the AD objects introduced below, including but not limited to domains, users, groups, computers, and Group Policy objects. A forest can contain one or multiple domains and be thought of as a state in the US or a country within the EU. Each forest operates independently but may have various trust relationships with other forests.

# `Tree`

A tree is a collection of Active Directory domains that begins at a single root domain. A forest is a collection of AD trees. Each domain in a tree shares a boundary with the other domains. A parent-child trust relationship is formed when a domain is added under another domain in a tree. Two trees in the same forest cannot share a name (namespace). Let's say we have two trees in an AD forest: `inlanefreight.local` and `ilfreight.local`. A child domain of the first would be `corp.inlanefreight.local` while a child domain of the second could be `corp.ilfreight.local`. All domains in a tree share a standard Global Catalog which contains all information about objects that belong to the tree.

# `Container`

Container objects hold other objects and have a defined place in the directory sub-tree hierarchy.

```
🌳 Domain: corp.local
│
├── 📁 OU: HR                          <-- Custom OU (can have GPOs, delegation)
│   ├── 👤 Alice (user)  
│   └── 👤 Bob (user)
│
├── 📁 OU: IT                          <-- Custom OU (good for structuring IT assets)
│   ├── 💻 Server01 (computer)
│   └── 👤 AdminUser (user)
│
├── 📁 Container: Users                <-- Built-in container (no GPOs, no delegation)
│   ├── 👤 Guest (default account)
│   └── 👤 KRBTGT (system account)
│
├── 📁 Container: Computers            <-- Built-in container (computers land here by default)
│   ├── 💻 Workstation01
│   └── 💻 Workstation02
│
└── 📁 Container: Builtin              <-- Built-in container (system/security groups)
    ├── 👥 Administrators
    └── 👥 Users
```
# `Leaf`

Leaf objects do not contain other objects and are found at the end of the sub-tree hierarchy. Basically, it is an actual object (user, computer, printer, etc.).

# `Global Unique Identifier (GUID)`

A **GUID** is a unique 128-bit value assigned to every Active Directory object (users, groups, computers, domains, etc.) and stored in the **ObjectGUID** attribute. It never changes for the lifetime of the object, making it the most reliable way to identify and query objects. Unlike names or SIDs, which may change or overlap, GUIDs guarantee uniqueness across the enterprise. When searching in AD, querying by GUID ensures accurate results, especially in large environments where similar names exist.

# `Security principals`

A security principal is any identity that an operating system can authenticate and authorize, such as a user, group, computer account, or process running under an account, which can then be assigned permissions to access resources. These are not managed by AD but rather by the [Security Accounts Manager (SAM)](https://en.wikipedia.org/wiki/Security_Account_Manager).

# `Security Identifier (SID)`

A **Security Identifier (SID)** is a unique, immutable value used by Windows to identify a security principal (user, group, or process) and manage its permissions and access rights. A SID can only be used once. Even if the security principle is deleted, it can never be used again in that environment to identify another user or group. There are also [well-known SIDs](https://ldapwiki.com/wiki/Wiki.jsp?page=Well-known%20Security%20Identifiers) that are used to identify generic users and groups.

# `Distinguished Name (DN)`

A [Distinguished Name (DN)](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/ldap/distinguished-names) describes the full path to an object in AD (such as `cn=bjones, ou=IT, ou=Employees, dc=inlanefreight, dc=local`). In this example, the user `bjones` works in the IT department of the company Inlanefreight, and his account is created in an Organizational Unit (OU) that holds accounts for company employees. The Common Name (CN) `bjones` is just one way the user object could be searched for or accessed within the domain.

```
Domain: inlanefreight.local
|
|-- Users OU
|    |-- bjones  --> DN: cn=bjones,ou=Users,dc=inlanefreight,dc=local
|    |-- ajones  --> DN: cn=ajones,ou=Users,dc=inlanefreight,dc=local
|
|-- Sales OU
|    |-- bjones  --> DN: cn=bjones,ou=Sales,dc=inlanefreight,dc=local
|    |-- ksmith  --> DN: cn=ksmith,ou=Sales,dc=inlanefreight,dc=local
|
|-- Managers OU
     |-- bjones  --> DN: cn=bjones,ou=Managers,dc=inlanefreight,dc=local
```

# `Relative Distinguised Name (RDN)`

A **Relative Distinguished Name (RDN)** is the unique name of an object within its parent container in a directory, used as a single component of the object’s **Distinguished Name (DN)**. AD does not allow two objects with the same name under the same parent container, but there can be two objects with the same RDNs that are still unique in the domain because they have different DNs.

```
inlanefreight.local (domain)
│
├─ Users (OU)
│   ├─ bjones (RDN)
│   └─ ajones (RDN)
│
├─ Sales (OU)
│   ├─ bjones (RDN)
│   └─ ksmith (RDN)
│
└─ Managers (OU)
    └─ bjones (RDN)
```


# `sAMAccountName`

The [sAMAccountName](https://docs.microsoft.com/en-us/windows/win32/ad/naming-properties#samaccountname) is the user's logon name. Here it would just be `bjones`. It must be a unique value and 20 or fewer characters.

# `userPrincipalName`

The [userPrincipalName](https://social.technet.microsoft.com/wiki/contents/articles/52250.active-directory-user-principal-name.aspx) attribute is another way to identify users in AD. This attribute consists of a prefix (the user account name) and a suffix (the domain name) in the format of `bjones@inlanefreight.local`. This attribute is not mandatory.

# `FSMO Roles`

In Active Directory (AD), if multiple Domain Controllers (DCs) try to make changes at the same time, conflicts can occur. To prevent this, Microsoft introduced **FSMO roles**, which assign specific responsibilities to certain DCs. This ensures that authentication and permissions continue working smoothly even if some DCs are down.

There are five `FSMO roles:`

- `Schema Master (1 per forest):` Controls updates to the AD schema.
- `Domain Naming Master (1 per forest):` Manages creation or deletion of domains in the forest.
- `RID Master (1 per domain):` Allocates unique IDs to objects so no two objects share the same identifier.
- `PDC Emulator (1 per domain):` Handles time synchronization, password changes, and backward compatibility with older systems.
- `Infrastructure Master (1 per domain):` Updates references to objects in other domains.


# `Global Catalog`

A **Global Catalog (GC)** is a domain controller in Active Directory that stores:

- A **full copy** of all objects in its own domain.
- A **partial copy** of objects from all other domains in the forest

Standard domain controllers only store objects for their own domain, but a GC allows users and applications to **find information about any object across the entire forest**.

**Functions of a Global Catalog:**

- **Authentication:** Includes authorization for all groups a user belongs to when creating an access token.
- **Object search:** Enables searching for objects across all domains in the forest using just one attribute, making the directory structure transparent. 

**Key point:** A GC is a feature enabled on a domain controller to improve authentication and facilitate forest-wide searches.

# `Read-Only Domain Controller (RODC)`

A `Read-Only Domain Controller (RODC)` is a domain controller with a read-only Active Directory database. It does not cache user passwords (except for its own computer account and KRBTGT passwords), and any changes made on the RODC are not replicated to other `DCs, SYSVOL, or DNS. RODCs include a read-only DNS server`, support administrator role separation, reduce replication traffic in the environment, and prevent unintended modifications to `SYSVOL` from spreading to other domain controllers.

# `Replication`

[Replication](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/replication/active-directory-replication-concepts) happens in AD when AD objects are updated and transferred from one Domain Controller to another. Whenever a DC is added, connection objects are created to manage replication between them. These connections are made by the Knowledge Consistency Checker (KCC) service, which is present on all DCs. Replication ensures that changes are synchronized with all other DCs in a forest, helping to create a backup in case one domain controller fails.

# `Service Principal Name (SPN)`

A [Service Principal Name (SPN)](https://docs.microsoft.com/en-us/windows/win32/ad/service-principal-names) uniquely identifies a service instance. They are used by Kerberos authentication to associate an instance of a service with a logon account, allowing a client application to request the service to authenticate an account without needing to know the account name.

# `Group Policy Object (GPO)`

[Group Policy Objects (GPOs)](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/policy/group-policy-objects) are virtual collections of policy settings. Each GPO has a unique GUID. A GPO can contain local file system settings or Active Directory settings. GPO settings can be applied to both user and computer objects. They can be applied to all users and computers within the domain or defined more granularly at the OU level.

- Password policie
- Desktop backgrounds
- Software installation
- Security settings
- Network and login scripts

# `Access Control List (ACL)`

An **Access Control List (ACL)** is a list of rules that defines **who (users or groups) can access a resource** and **what actions they are allowed to perform** on it.

- Each resource (file, folder, object, etc.) can have its own ACL.
- Each entry in the ACL, called an **Access Control Entry (ACE)**, specifies a **security principal** (user, group, or process) and the **permissions** they have (e.g., read, write, execute).
- ACLs are used by operating systems and applications to enforce **security and access control**, ensuring only authorized users can perform certain actions.

# `Access Control Entries (ACEs)`

An **Access Control Entry (ACE)** is a single entry in an **Access Control List (ACL)** that specifies the **permissions granted or denied** to a particular **security principal** (like a user, group, or process) for a resource.

**Example:**  
```
For a file:

- ACE 1 → User Alice: Read & Write
- ACE 2 → Group Managers: Read only
- ACE 3 → Everyone else: No acces
```

# `Directionary Access Control List (DACL)`

A **Discretionary Access Control List (DACL)** is a type of **Access Control List (ACL)** that specifies **which users or groups are allowed or denied access to a resource**.

Here’s a simple visual to explain `ACL → DACL → ACE` hierarchy:

```pgsql
Access Control List (ACL)  ← The full list of access rules for a resource
|
|-- Discretionary ACL (DACL)  ← Controls who is allowed or denied access
|    |
|    |-- Access Control Entry (ACE)  ← Alice: Read & Write
|    |-- Access Control Entry (ACE)  ← Group Managers: Read only
|    |-- Access Control Entry (ACE)  ← Everyone else: No access
|
|-- System ACL (SACL)  ← Used for auditing access (optional, separate from DACL)
```

# `System Access Control Lists (SACL)`

Allows for administrators to log access attempts that are made to secured objects. ACEs specify the types of access attempts that cause the system to generate a record in the security event log.

# `Fully Qualified Domain Name (FQDN)`

An FQDN is the complete name for a specific computer or host. It is written with the hostname and domain name in the format [host name].[domain name].[tld]. This is used to specify an object's location in the tree hierarchy of DNS. The FQDN can be used to locate hosts in an Active Directory without knowing the IP address, much like when browsing to a website such as google.com instead of typing in the associated IP address.

# `Tombstone`

A **tombstone** in Active Directory is a temporary marker that represents a deleted object, used to replicate the deletion to other domain controllers before the object is permanently removed.

# `AD Recycle Bin`

The **AD Recycle Bin**, introduced in Windows Server 2008 R2, allows administrators to **recover deleted Active Directory objects** without restoring from backups or rebooting domain controllers. When enabled, deleted objects are preserved for a configurable period (default 60 days), and most of their attributes are retained, making full restoration easier and more reliable.

# `SYSVOL`

The **SYSVOL folder** stores public domain files such as **system policies, Group Policy settings, logon/logoff scripts**, and other scripts used in Active Directory. Its contents are **replicated to all domain controllers** using File Replication Services (FRS), ensuring consistency across the domain.

# `AdminSDHolder`

The `AdminSDHolder` object in Active Directory is used to **protect the ACLs of members of privileged built-in groups** (like Domain Admins). It acts as a template that holds the correct security descriptor.

`AdminSDHolder`

- Think of `AdminSDHolder` as a **template for security rules** for the most important users in Active Directory, like administrators.
- It ensures that **privileged accounts always have the correct access rules**, even if someone tries to change them.

`SDProp process`

- This is a background process that runs every hour on a special domain controller called the **PDC Emulator**.
- Its job is to **check all privileged accounts** and make sure their access rules match the `AdminSDHolder` template.
- If someone tries to give themselves extra permissions on an admin account, `SDProp` will **undo it automatically.

# `dsHeuristics`

The **dsHeuristics** attribute is a **configuration setting on the Active Directory Directory Service** that controls multiple forest-wide behaviors.

- One important use is to **exclude certain groups from `AdminSDHolder` protection
- Normally, members of **protected groups** (like Domain Admins) have their permissions automatically corrected by the `SDProp process`.
- If a group is excluded via `dsHeuristics`, changes to its members’ permissions **won’t be reverted** when `SDProp` runs.
- 
**Key point:** `dsHeuristics` allows administrators to **fine-tune which groups are protected** by AdminSDHolder and which are not.

# `adminCount`

The adminCount attribute determines whether or not the SDProp process protects a user. If the value is set to 0 or not specified, the user is not protected. If the attribute value is set to 1, the user is protected.

# `Active Directory Users and Computers (ADUC)`

ADUC is a GUI console commonly used for managing users, groups, computers, and contacts in AD. Changes made in ADUC can be done via PowerShell as well.

# `ADSI Edit`

ADSI Edit is a GUI tool used to manage objects in AD. It provides access to far more than is available in ADUC and can be used to set or delete any attribute available on an object, add, remove, and move objects as well. It is a powerful tool that allows a user to access AD at a much deeper level. Great care should be taken when using this tool, as changes here could cause major problems in AD.

# `sIDHistory`

The **sIDHistory** attribute stores **previous Security Identifiers (SIDs)** that were assigned to a user or group.

- It’s mainly used during **domain migrations** so users keep their access rights in the new domain
- If **misconfigured or abused**, an attacker can use sIDHistory to **gain elevated permissions** that the account had in the old domain.
- Proper **SID filtering** prevents old SIDs from being used maliciously in the new domain

# `NTDS.DIT`

The **NTDS.DIT** file is the **main Active Directory database** stored on a Domain Controller at `C:\Windows\NTDS\`.

- It contains **all AD data**, including users, groups, group memberships, and **password hashes**.
- If an attacker gains full access to a domain, they can extract this file to
    - Perform **pass-the-hash attacks**
    - Crack passwords offline using tools like **Hashcat**
- If the **“Store passwords with reversible encryption”** setting is enabled, NTDS.DIT can also store **cleartext passwords** for affected users.

**Key point:** NTDS.DIT is the **central repository of AD information**, and protecting it is critical for domain security.

