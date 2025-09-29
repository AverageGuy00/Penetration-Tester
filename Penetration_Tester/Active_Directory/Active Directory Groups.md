`Active Directory` ships with many `built-in groups`, and most organizations also create `custom groups` to fine-tune access within the domain. Over time, however, the number of `groups` in an `AD environment` can grow rapidly, becoming difficult to manage and increasing the risk of unintentional `privilege escalation`.

To maintain security, it is essential to understand how different `group types` and `scopes` affect access, and for organizations to regularly `audit` their domain. Such audits should focus on identifying which `groups` exist, what `privileges` they confer, and whether `users` belong to more `groups` than necessary for their day-to-day responsibilities.

`Groups` have two key characteristics:

- `Type` → `Security` or `Distribution`
- `Scope` → defines how the `group` is applied in the `domain` or `forest`
---

# Types of Groups

`Security groups` are designed for managing `permissions` and `rights` across multiple `users` efficiently. Any `user` added inherits the `group’s permissions`, which reduces overhead, simplifies administration, and ensures consistent access control.

`Distribution groups` are used by `email applications` (e.g., `Microsoft Exchange`) to send messages to multiple `users` like a mailing list. They cannot be used to assign `permissions` in a `domain` environment.

-------------------

# Group Scopes

```
[ FOREST ROOT ]
        |
        +--------------------+
        |  Universal Groups  |  <--- Forest-wide visibility
        +--------------------+
               /   \
              /     \
   (can include users/groups from any domain)
             /       \
            v         v

+-------------------+       +-------------------+
|     Domain A      |       |     Domain B      |
|                   |       |                   |
| +---------------+ |       | +---------------+ |
| | Global Groups | |       | | Global Groups | |  <--- Domain-specific
| +---------------+ |       | +---------------+ |
|                   |       |                   |
| +---------------+ |       | +---------------+ |
| | Local Groups  | |       | | Local Groups  | |  <--- Domain-only scope
| +---------------+ |       | +---------------+ |
+-------------------+       +-------------------+
```

### Domain Local Group

`Domain Local Groups` are used to assign `permissions` to `resources` within the same `domain` they were created. They **cannot** be used across domains, but they **can contain** `users` and `groups` from other `domains`. Nesting is allowed into other `domain local groups`, but **not** into `global groups`.

```
[ Domain A Resource ]  <-- permissions controlled by Domain Local Group
         ^
         |
   -----------------
   | Domain Local  |  (exists only in Domain A)
   -----------------
     /        \
[User from A]  [User from B]
               (foreign domain user allowed)

Nesting: Domain Local -> Domain Local ✅
Nesting: Domain Local -> Global ❌
```

### Global Group

`Global Groups` are created in one `domain` and can only contain `users` and `groups` from that domain. However, they can be used to grant `access` to `resources` in other domains. They support nesting into both `global groups` and `domain local groups`.

```
+-----------------------------+
|        Global Group         |   <-- Created in Domain A
+-----------------------------+
|  User A1 (Domain A only)    |
|  User A2 (Domain A only)    |
|  Group A1 (Domain A only)   |
+-----------------------------+
            |
            v
+-----------------------------+
| Resource (Domain B server)  |   <-- Permissions applied here
+-----------------------------+
```

### Universal Group

`Universal Groups` can be used to manage `resources` across multiple `domains` and can be assigned permissions to any `object` within the same `forest`. They are available to all `domains` and can contain `users` and `groups` from any `domain`. Unlike `domain local` and `global groups`, universal groups are stored in the `Global Catalog (GC)`, so any membership change triggers **forest-wide replication**.

Best practice: keep `global groups` as members of `universal groups`. Since `global group` membership changes replicate only within a single `domain`, this avoids excessive replication overhead. Adding/removing `individual users` directly to a `universal group` will trigger replication across the entire `forest`, creating unnecessary load.

### AD Group Scope Examples

```powershell-session
PS C:\htb> Get-ADGroup  -Filter * |select samaccountname,groupscope

samaccountname                           groupscope
--------------                           ----------
Administrators                          DomainLocal
Users                                   DomainLocal
Guests                                  DomainLocal
Print Operators                         DomainLocal
Backup Operators                        DomainLocal
Replicator                              DomainLocal
Remote Desktop Users                    DomainLocal
Network Configuration Operators         DomainLocal
Distributed COM Users                   DomainLocal
IIS_IUSRS                               DomainLocal
Cryptographic Operators                 DomainLocal
Event Log Readers                       DomainLocal
Certificate Service DCOM Access         DomainLocal
RDS Remote Access Servers               DomainLocal
RDS Endpoint Servers                    DomainLocal
RDS Management Servers                  DomainLocal
Hyper-V Administrators                  DomainLocal
Access Control Assistance Operators     DomainLocal
Remote Management Users                 DomainLocal
Storage Replica Administrators          DomainLocal
Domain Computers                             Global
Domain Controllers                           Global
Schema Admins                             Universal
Enterprise Admins                         Universal
Cert Publishers                         DomainLocal
Domain Admins                                Global
Domain Users                                 Global
Domain Guests                                Global
```

`Group scope conversion rules` come with restrictions:

- `Global → Universal` only if the `Global Group` is **not a member** of another `Global Group`.
- `Domain Local → Universal` only if the `Domain Local Group` does **not contain** other `Domain Local Groups`
- `Universal → Domain Local` has **no restrictions**.
- `Universal → Global` only if the `Universal Group` does **not contain** other `Universal Groups`.

---

# Built-in vs. Custom Groups

Several built-in `security groups` are created with `Domain Local` scope when a `domain` is first created. These groups serve specific `administrative` purposes and **do not allow group nesting** (only `user accounts` can be added).

Example:

- `Domain Admins` → a `Global security group` that can only contain accounts from its own `domain`.
- To allow an account from `Domain B` to perform admin tasks on a `Domain A` controller, it must be added to the `Administrators` group, which is `Domain Local`.

Although AD is prepopulated with many groups, organizations typically create additional `security` and `distribution` groups for their own needs. New services can also create groups automatically — e.g., installing `Microsoft Exchange` adds multiple privileged `security groups` to the domain, which, if mismanaged, may become a vector for `privilege escalation`.

---

# Nested Group Membership

`Nested group membership` is critical in `Active Directory`. A `Domain Local Group` can be a member of another `Domain Local Group` in the same `domain`. This means a `user` may inherit privileges not assigned directly to their account, but through layers of `group-to-group membership`.

This can create `unintended privilege inheritance` that is hard to detect without tools. `BloodHound` is especially useful for penetration testers and admins to **map nested group relationships** and uncover hidden privilege escalation paths.

### Examining Nested Groups via BloodHound

![[Pasted image 20250929232325.png]]

This image is a `nested group membership diagram` from a tool like BloodHound, showing how `privileges` propagate in Active Directory. Here’s what it represents:

- `DCORNER@INLANEFREIGHT.LOCAL` is a user
- `HELP DESK@INLANEFREIGHT.LOCAL` is a `Domain Local Group`. The line labeled `MemberOf` shows that `DCORNER` is a member of `HELP DESK`
- `HELP DESK LEVEL 1@INLANEFREIGHT.LOCAL` is another `Domain Local Group`. `HELP DESK` is a member of this group (`MemberOf`), so `DCORNER` indirectly inherits its privileges.
- `TIER 1 ADMINS@INLANEFREIGHT.LOCAL` is a group with elevated privileges. The line labeled `GenericWrite` from `HELP DESK LEVEL 1` to `TIER 1 ADMINS` indicates that members of `HELP DESK LEVEL 1` (and therefore `DCORNER` via nesting) can modify `TIER 1 ADMINS` — a form of privilege escalation.

---

# Important Group Attributes

Like users, groups have many [attributes](http://www.selfadsi.org/group-attributes.htm). Some of the most [important group attributes](https://docs.microsoft.com/en-us/windows/win32/ad/group-objects) include:

- `cn`: The `cn` or Common-Name is the name of the group in Active Directory Domain Services.
- `member`: Which user, group, and contact objects are members of the group
- `groupType`: An integer that specifies the group type and scope
- `memberOf`: A listing of any groups that contain the group as a member (nested group membership).
- `objectSid`: This is the security identifier or SID of the group, which is the unique value used to identify the group as a security principal.