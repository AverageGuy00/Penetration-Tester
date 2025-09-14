# Security Identifier (SID)

**Security Identifier (SID)**

- A **SID** is a **unique, immutable identifier** assigned to every user, group, and computer account in Windows.
- SIDs are used **internally by Windows** to manage permissions rather than using human-readable names.
- Every **access token** contains the SID of the user and the groups they belong to.
- When a user or process tries to access a securable object, Windows checks the **ACLs (via ACEs)** against the SID in the access token to determine **allowed or denied actions**.
- SIDs are central to **authorization, auditing, and privilege management**, and are crucial to understand when performing **privilege escalation** or analyzing Windows security.

**Example:**

- A user named `Alice` might have a SID like `S-1-5-21-3623811015-3361044348-30300820-1013`. 
- Even if the username changes, the SID remains the same, so all permissions remain linked correctly.

|           **Number**            |     **Meaning**      |                                                                                          **Description**                                                                                           |
| :-----------------------------: | :------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                S                |         SID          |                                                                                  Identifies the string as a SID.                                                                                   |
|                1                |    Revision Level    |                                                                      To date, this has never changed and has always been `1`.                                                                      |
|                5                | Identifier-authority |                                                   A 48-bit string that identifies the authority (the computer or network) that created the SID.                                                    |
|               21                |    Subauthority1     | This is a variable number that identifies the user's relation or group described by the SID to the authority that created it. It tells us in what order this authority created the user's account. |
| 674899381-4069889467-2080702030 |    Subauthority2     |                                                                       Tells us which computer (or domain) created the number                                                                       |
|              1002               |    Subauthority3     |                      The RID that distinguishes one account from another. Tells us whether this user is a normal user, a guest, an administrator, or part of some other group                      |

---

# Security Accounts Manager (SAM) and Access Control Entries (ACE)

**Security Accounts Manager (SAM)**

- SAM is a **Windows database that stores user accounts and security information** for the local system.
- It governs **what rights and privileges a user or process has** on a machine.
- SAM is central to **authentication and authorization**, controlling who can log in and what processes they can execute.

**Access Control Entries (ACE) and Access Control Lists (ACLs)**

- **ACLs** are lists that define **permissions for securable objects** like files, folders, or processes.
- Each ACL contains **ACEs**, which specify: 
    - Which **users, groups, or processes** are allowed or denied access.
    - What **type of access** is granted, e.g., read, write, execute. 
- There are two main types of ACLs:
    - **Discretionary ACL (DACL):** Determines **who can access an object** and with what permissions.
    - **System ACL (SACL):** Used for **auditing**, logging access attempts to an object.

**Access Tokens and Authorization**

- Every process or thread runs under an **access token**, which contains the user’s **Security Identifier (SID)** and other security information.
- The **Local Security Authority (LSA)** validates access tokens during authorization, ensuring processes adhere to the defined ACLs.
- Understanding SAM, ACEs, ACLs, and access tokens is critical for **privilege escalation and security assessments**, as they govern **who can do what** on a system.

---

# User Account Control (UAC)

- **Purpose:** Prevent unauthorized system changes and malware execution.
- **Admin Approval Mode:** Users, even administrators, run with standard privileges by default. Elevation triggers a consent prompt.
- **Consent Prompt:**
    - Admin user → confirm execution.
    - Standard user → enter administrator credentials.
- **Triggers:** Executables flagged for elevation, system settings changes, software installations.
- **Effect:** Interrupts potentially harmful scripts or binaries until explicit approval.
- **Security Role:** Protects critical system areas and enforces privilege separation.

---

# Registry

