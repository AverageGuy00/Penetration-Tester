# Users

These are the `users` within the organization's `AD environment`. `Users` are considered `leaf objects`, which means they cannot contain any other objects within them. Another example of a `leaf object` is a `mailbox` in `Microsoft Exchange`. A `user object` is considered a `security principal` and has a `security identifier (SID)` and a `global unique identifier (GUID)`.
# Contacts

A contact object is usually used to represent an external user and contains informational attributes such as first name, last name, email address, telephone number, etc. They are `leaf objects` and are NOT security principals (securable objects), so they don't have a SID, only a GUID.

# Printers

A printer object points to a printer accessible within the AD network. Like a contact, a printer is a `leaf object` and not a security principal, so it only has a GUID. Printers have attributes such as the printer's name, driver information, port number, etc.

# Computers

A `computer object` is any `computer` joined to the `AD network` (`workstation` or `server`). `Computers` are `leaf objects` because they do not contain other objects. However, they are considered `security principals` and have a `SID` and a `GUID`. Like `users`, they are prime targets for attackers since full administrative access to a `computer` (as the all-powerful `NT AUTHORITY\SYSTEM` account) grants similar rights to a standard `domain user` and can be used to perform the majority of the `enumeration` tasks that a `user account` can.

# Shared Folders

A `shared folder object` points to a folder on a specific `computer`. `Access` can range from open to `everyone`, to only `authenticated users`, or restricted to specific `users/groups`. Those not explicitly allowed are denied access. `Shared folders` are **not security principals** (they only have a `GUID`) and store attributes such as `name`, `system location`, and `security rights`.
# Groups

A `group` is a `container object` that can hold `users`, `computers`, and other `groups`. Unlike `contacts`, it **is a security principal**, with both a `SID` and `GUID`. `Groups` manage permissions by granting access collectively instead of individually. `Nested groups` allow one group to be a member of another, which can cause users to inherit unintended rights — a common focus in penetration testing. Tools like `BloodHound` help map and audit these relationships. Key `group` attributes include `name`, `description`, `membership`, and `group memberships`.

# Organizational Units (OUs)

An `organizational unit (OU)` is a `container` that `systems administrators` can use to store similar `objects` for easier administration. `OUs` are often used for `administrative delegation` of tasks without granting a `user account` full administrative rights.

For example, a top-level `OU` called `Employees` can contain child `OUs` such as `Marketing`, `HR`, `Finance`, and `Help Desk`. If an `account` is given the right to `reset passwords` over the top-level `OU`, that account can reset passwords for **all users** in the company. However, if the `OU` structure places specific departments as child `OUs` under the `Help Desk OU`, then any `user` in the `Help Desk OU` would inherit those delegated rights.

Other tasks that may be delegated at the `OU` level include `creating/deleting users`, `modifying group membership`, `managing Group Policy links`, and `performing password resets`.

`OUs` are very useful for managing `Group Policy` settings across subsets of `users` and `groups` within a `domain`. For example, privileged `service accounts` may be placed in a dedicated `OU` with a `Group Policy object (GPO)` assigned to enforce stricter `password policies`.

### **Scenario A: Departmental Delegation**

Each department has its own admins who can only manage their staff.

```
OU: Employees
   |
   +-- OU: HR
   |     - HR_Admins  ---> can reset ---> HR Users
   |
   +-- OU: Marketing
         - Marketing_Admins  ---> can reset ---> Marketing Users
```

**Description:** 

Here, rights are scoped tightly. `HR admins` can only reset `HR users’` passwords, and `Marketing admins` can only reset `Marketing users’` passwords. This prevents cross-department mistakes or abuse. It’s a `least privilege model` — simple, but effective.

### **Scenario B: Centralized Help Desk**

One group (HelpDesk) gets authority to reset for everyone in Employees OU.

```
OU: Employees
   |
   +-- OU: Finance
   |     - HelpDesk  ---> can reset ---> Finance Users
   |
   +-- OU: HR
   |     - HelpDesk  ---> can reset ---> HR Users
   |
   +-- OU: Marketing
         - HelpDesk  ---> can reset ---> Marketing Users
```

**Description:**  

The `HelpDesk group` has broad `delegation`, meaning they can reset passwords for all `standard users` across `departments`. This is common in large organizations where help desks support the whole company. However, if `privileged accounts` (`admins`, `service accounts`) are mixed inside `Employees`, `HelpDesk` could reset them too — making this model risky without separation.

### Scenario C: Tiered Access Model

Privileged accounts are isolated; HelpDesk only manages standard users.

```
OU: PrivilegedAccounts
   - Domain Admins (NO delegation)
   - Service Accounts (NO delegation)

OU: Employees
   |
   +-- OU: Staff
         - HelpDesk  ---> can reset ---> Staff Users
```

**Description:**  

This is a more secure setup. `Privileged accounts` (`admins`, `service accounts`, `sensitive users`) are moved into a `separate OU` with no `delegation` allowed. `HelpDesk` is restricted to resetting `regular staff accounts`. This stops attackers from compromising `HelpDesk accounts` and immediately gaining control of `high-value targets`. It aligns with Microsoft’s `Tiered Admin Model`.
### Scenario D: Nested Delegation Risk

OU nesting causes unintended inheritance.

```
OU: Employees
   |
   +-- OU: HelpDesk
   |     - HelpDesk  ---> can reset ---> HelpDesk Users
   |                                 ---> Contractors Users (child OU!)
   |
   +-- OU: Contractors
```

**Description:**  

If `Contractors OU` is created under `HelpDesk OU`, the `delegation rights` cascade downward. Suddenly, `HelpDesk users` can reset `contractor passwords`, even if that wasn’t intended. This is a common `misconfiguration` that `penetration testers` and `attackers` love to exploit — especially if `contractors` have access to `sensitive apps`. Tools like `BloodHound` will highlight these unintended paths.

# Domain

A `domain` is the structure of an `Active Directory (AD) network`. Domains contain `objects` such as `users` and `computers`, which are organized into `container objects`: `groups` and `OUs`. Every `domain` has its own separate `database` and sets of `policies` that can be applied to any and all `objects` within the `domain`. Some `policies` are set by default (like the `domain password policy`), while others are created and applied based on the organization's needs (for example, blocking access to `cmd.exe` for all non-administrative `users` or mapping `shared drives` at `log in`).

# Domain Controllers

`Domain Controllers (DCs)` are the `brains` of an `AD network`. They handle `authentication requests`, verify `users` on the `network`, and control who can access the various `resources` in the `domain`. All `access requests` are validated via the `domain controller`, and `privileged access requests` are based on predetermined `roles` assigned to `users`. A `DC` also enforces `security policies` and stores information about every other `object` in the `domain`.

- `Domain` = the **organization’s rulebook and directory** (logical). 
- `Domain Controller` = the **librarian/server that enforces the rulebook** (physical/VM).

# Sites

In `Active Directory`, a `site` represents the **physical network topology** of an organization. `Sites` are defined by one or more `IP subnets` and are used to manage `replication traffic` and optimize `authentication`.

- A `site` is defined by one or more `IP subnets`.   
- `Domain Controllers` are placed into `sites` to represent where they physically reside on the network.
- `Replication` is fast and frequent **within a site** (intra-site replication).
- `Replication` between `sites` is less frequent and can be scheduled (inter-site replication), to conserve bandwidth over slow WAN links.
- `Clients` will normally authenticate with a `DC` in their closest `site` to reduce latency.

```
                         DOMAIN: soupdecode.local
                              (One domain)

         ┌─────────────────────────┐   ┌─────────────────────────┐   ┌─────────────────────────┐
         │         SITE A          │   │         SITE B          │   │         SITE C          │
         │ Headquarters - Manila   │   │ Branch Office - Cebu    │   │ International - Tokyo   │
         │-------------------------│   │-------------------------│   │-------------------------│
         │ Subnet: 10.10.0.0/24    │   │ Subnet: 10.20.0.0/24    │   │ Subnet: 10.30.0.0/24    │
         │ DC1, DC2                │   │ DC3                     │   │ DC4                     │
         │ 500 Users, PCs          │   │ 100 Users, PCs          │   │ 200 Users, PCs          │
         └─────────────────────────┘   └─────────────────────────┘   └─────────────────────────┘
                  │                           │                           │
                  │ Intra-site replication    │ Intra-site replication    │ Intra-site replication
                  ▼                           ▼                           ▼
       ─────────────────────────────────── Inter-site replication (WAN) ───────────────────────────────────
```

# Built-in

In AD, built-in is a container that holds [default groups](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups) in an AD domain. They are predefined when an AD domain is created.

# Foreign Security Principals

A `foreign security principal (FSP)` is an `object` created in `Active Directory (AD)` to represent a `security principal` that belongs to a `trusted external forest`. They are created when a `user`, `group`, or `computer` from an `external forest` is added to a `group` in the `current domain`. They are created automatically after adding a `security principal` to a `group`.

```
+----------------------------+        +---------------------------------+        +----------------------+
| External Forest            |        | ForeignSecurityPrincipals       |        | Internal Domain      |
| partner.local              | -----> | FSP Object (SID placeholder)    | -----> | Group: IT_Admins     |
| User: JohnDoe              |        | in corp.local                   |        | in corp.local        |
+----------------------------+        +---------------------------------+        +----------------------+
```

