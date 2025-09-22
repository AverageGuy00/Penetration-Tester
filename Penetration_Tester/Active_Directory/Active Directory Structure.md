[Active Directory (AD)](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) is a directory service for Windows network environments. It is a distributed, hierarchical structure that allows for centralized management of an organization's resources, including users, computers, groups, network devices and file shares, group policies, servers and workstations, and trusts. AD provides authentication and authorization functions within a Windows domain environment. A directory service, such as [Active Directory Domain Services (AD DS)](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) gives an organization ways to store directory data and make it available to both standard users and administrators on the same network.

Active Directory flaws and misconfigurations can often be used to obtain a foothold (internal access), move laterally and vertically within a network, and gain unauthorized access to protected resources such as databases, file shares, source code, and more. AD is essentially a large database accessible to all users within the domain, regardless of their privilege level. A basic AD user account with no added privileges can be used to enumerate the majority of objects contained within AD, including but not limited to:

| Domain Computers         | Domain Users                |
| ------------------------ | --------------------------- |
| Domain Group Information | Organizational Units (OUs)  |
| Default Domain Policy    | Functional Domain Levels    |
| Password Policy          | Group Policy Objects (GPOs) |
| Domain Trusts            | Access Control Lists (ACLs) |

# Active Directory Structure

- `Forest` → top-level security boundary (can have multiple domains)
- `Domain` → contains objects like users, groups, computers; can have child domains
- `Organizational Units (OUs)` → containers inside domains for organizing objects, applying Group Policies.
- `Objects` → users, groups, computers inside OUs.

```
INLANEFREIGHT.LOCAL/             # `Forest` (root)
├── ADMIN.INLANEFREIGHT.LOCAL    # `Domain`
│   ├── GPOs                     # `Group Policies`
│   └── OU                       # `Organizational Unit`
│       └── EMPLOYEES            # `Sub-OU`
│           ├── COMPUTERS        # `OU` for computers
│           │   └── FILE01       # `Object` (computer)
│           ├── GROUPS           # `OU` for groups
│           │   └── HQ Staff     # `Object` (group)
│           └── USERS            # `OU` for users
│               └── barbara.jones# `Object` (user)
├── CORP.INLANEFREIGHT.LOCAL     # Another `Domain`
└── DEV.INLANEFREIGHT.LOCAL      # Another `Domain`
```

`INLANEFREIGHT.LOCAL` is the **root domain**.  
It has three **subdomains**:

- `ADMIN.INLANEFREIGHT.LOCAL`
- `CORP.INLANEFREIGHT.LOCAL`
- `DEV.INLANEFREIGHT.LOCAL`

Each domain (root or child) contains **objects** like `users`, `groups`, and `computers`.

In real companies, you often see multiple `domains` or even multiple `forests`.  
Instead of recreating all users, admins can set up `trust relationships` between them.

`Trust relationships` = allow users in one domain/forest to access resources in another.

But: if trusts are not managed properly, they can create serious **security risks** (e.g., attackers hopping across trusted domains).

```
Forest A                          # First forest (security boundary)
├── ParentDomainA.local           # Root domain of Forest A
│   ├── OU: HR
│   ├── OU: IT
│   └── OU: Finance
│
└── ChildDomainA.local            # Child domain inside Forest A
    ├── OU: HR
    ├── OU: IT
    └── OU: Finance

                TRUST RELATIONSHIP
     ----------------------------------------->
                (Forest A ↔ Forest B)

Forest B                          # Second forest (separate security boundary)
└── DomainB.local                 # Root domain of Forest B
    ├── OU: HR
    ├── OU: IT
    └── OU: Finance
```

- `Forest A` and `Forest B` are **independent security boundaries**.
- A `trust relationship` is like a **bridge** between them → it allows authentication and resource sharing.
- Without the trust, Forest A users couldn’t access Forest B resources (and vice versa).

---

```
Forest A: INLANEFREIGHT.LOCAL                # Root domain (Forest A boundary)
├── CORP.INLANEFREIGHT.LOCAL
│   └── WH.CORP.INLANEFREIGHT.LOCAL         # Child of CORP
├── DEV.INLANEFREIGHT.LOCAL
└── ADMIN.INLANEFREIGHT.LOCAL


      ⬌   BIDIRECTIONAL FOREST TRUST   ⬌    # Top-level trust only


Forest B: FREIGHTLOGISTICS.LOCAL             # Root domain (Forest B boundary)
├── CORP.FREIGHTLOGISTICS.LOCAL
├── DEV.FREIGHTLOGISTICS.LOCAL
│   └── ADMIN.DEV.FREIGHTLOGISTICS.LOCAL    # Child of DEV
└── ADMIN.FREIGHTLOGISTICS.LOCAL
```

### Explanation:

- `INLANEFREIGHT.LOCAL` ↔ `FREIGHTLOGISTICS.LOCAL` have a **two-way trust** at the forest root.
- This means **root-to-root authentication works**: users in either forest can authenticate in the _other forest’s root domain_
- **Child domains inherit trust with their own root**, but **not across forests** by default.
    - Example: `wh.corp.inlanefreight.local` trusts `inlanefreight.local`.
    - Example: `admin.dev.freightlogistics.local` trusts `freightlogistics.local`.
- But there’s **no direct trust** between `wh.corp.inlanefreight.local` and `admin.dev.freightlogistics.local`
    - So a user in `admin.dev.freightlogistics.local` can’t log into a machine in `wh.corp.inlanefreight.local` unless an **explicit domain-to-domain trust** is created.
 
Think of it like:

- **Forest trust** = airports in two countries connected by international flights.  
- **Domain-to-domain trust** = direct city-to-city flights. If no direct flight exists, you must go through the capital first.


