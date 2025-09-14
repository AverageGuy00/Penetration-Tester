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

The **Registry** is a **central hierarchical database** in Windows that stores configuration and settings for both the operating system and applications. It contains system-wide and user-specific data, making it critical for OS functionality.

- **Structure:**
    - Organized in a **tree-like hierarchy** of **root keys**.
    - Each root key contains **subkeys**, which in turn hold **values** (data entries).
- **Root keys examples:**
    - `HKEY_LOCAL_MACHINE (HKLM)` → system-wide configuration and hardware settings.
    - `HKEY_CURRENT_USER (HKCU)` → user-specific settings.
- **Values:** Entries in subkeys store information such as strings, numbers, binary data, and other types. There are **11 value types** (e.g., `REG_SZ`, `REG_DWORD`, `REG_BINARY`).
- **Access:** Opened using **Registry Editor (regedit.exe)**, which allows viewing, editing, and adding keys and values.

The Registry is essential for system and application behavior. Modifying it improperly can destabilize the system, but it is also a key target for **forensics, security analysis, and privilege escalation**.

|        **Value**        |                                                                                                                                                                                                       **Type**                                                                                                                                                                                                        |
| :---------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|       REG_BINARY        |                                                                                                                                                                                               Binary data in any form.                                                                                                                                                                                                |
|        REG_DWORD        |                                                                                                                                                                                                   A 32-bit number.                                                                                                                                                                                                    |
| REG_DWORD_LITTLE_ENDIAN |                                                                                                                A 32-bit number in little-endian format. Windows is designed to run on little-endian computer architectures. Therefore, this value is defined as REG_DWORD in the Windows header files.                                                                                                                |
|  REG_DWORD_BIG_ENDIAN   |                                                                                                                                                               A 32-bit number in big-endian format. Some UNIX systems support big-endian architectures.                                                                                                                                                               |
|      REG_EXPAND_SZ      | A null-terminated string that contains unexpanded references to environment variables (for example, "%PATH%"). It will be a Unicode or ANSI string depending on whether you use the Unicode or ANSI functions. To expand the environment variable references, use the [**ExpandEnvironmentStrings**](https://docs.microsoft.com/en-us/windows/win32/api/processenv/nf-processenv-expandenvironmentstringsa) function. |
|        REG_LINK         |                                                                                A null-terminated Unicode string containing the target path of a symbolic link created by calling the [**RegCreateKeyEx**](https://docs.microsoft.com/en-us/windows/desktop/api/Winreg/nf-winreg-regcreatekeyexa) function with REG_OPTION_CREATE_LINK.                                                                                |
|      REG_MULTI_SZ       |                  A sequence of null-terminated strings, terminated by an empty string (\0). The following is an example: _String1_\0_String2_\0_String3_\0_LastString_\0\0 The first \0 terminates the first string, the second to the last \0 terminates the last string, and the final \0 terminates the sequence. Note that the final terminator must be factored into the length of the string.                   |
|        REG_NONE         |                                                                                                                                                                                                No defined value type.                                                                                                                                                                                                 |
|        REG_QWORD        |                                                                                                                                                                                                   A 64-bit number.                                                                                                                                                                                                    |
| REG_QWORD_LITTLE_ENDIAN |                                                                                                                A 64-bit number in little-endian format. Windows is designed to run on little-endian computer architectures. Therefore, this value is defined as REG_QWORD in the Windows header files.                                                                                                                |
|         REG_SZ          |                                                                                                                                        A null-terminated string. This will be either a Unicode or an ANSI string, depending on whether you use the Unicode or ANSI functions.                                                                                                                                         |

---

# Run and RunOnce Registry Keys

The **Run** and **RunOnce** registry keys are special locations in the Windows Registry that control **programs executed automatically when a user logs in** or when the system starts. They are part of **registry hives**, which are logical groups of keys, subkeys, and values that maintain system access and configuration.

- **Run Keys:** Programs listed here will **run every time the user logs in** or the system starts.
- **RunOnce Keys:** Programs listed here will **run only once** after login or system start, then the entry is removed automatically.
- **Locations:**
    - `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run` → system-wide programs (all users)
    - `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run` → programs for the current user only
    - `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce` → system-wide, executed once
    - `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce` → current user, executed once
- **Purpose:** Useful for **automatic startup applications**, but also commonly used in **malware persistence**, making these keys a key target in **security and forensic investigations**.

**Examples:**

- System-wide startup: `SecurityHealthSystray.exe`, `Greenshot.exe`
- User-specific startup: `OneDrive.exe`, `OpenVPN-GUI.exe`, `Docker Desktop.exe`

---

# Application Whitelisting

- **Definition:** A security approach where only approved software/applications are allowed to run on a system.
- **Purpose:** Protects against malware and unapproved software that may not align with business needs.
- **Implementation:**
    - Start in **audit mode** to ensure legitimate applications are not blocked.
    - Gradually move to enforced mode once confidence in the whitelist is established.
- **Comparison to Blacklisting:**
    - Blacklisting blocks known harmful software; everything else is allowed.
    - Whitelisting blocks everything by default except explicitly approved applications (**zero trust principle**).
    - Maintenance overhead is usually lower with whitelisting than blacklisting.
- **Recommendation:** Endorsed by NIST and recommended for high-security environments.

--- 

# AppLocker

- **Definition:** Microsoft’s application whitelisting tool, introduced in Windows 7.
- **Function:** Controls which applications and files users can execute.
- **Scope:** Can manage:
    - Executables
    - Scripts 
    - Windows installer files 
    - DLLs
    - Packaged apps and app installers 
- **Rule Creation:** Rules can be based on:
    - Publisher (from digital signature)
    - Product name 
    - File name
    - File version 
    - File path  
    - File hash 
- **Application:** Rules can target:
    - Security groups
    - Individual users 
- **Deployment:** Can be set in **audit mode** first to evaluate impact before enforcing rules.

---

# Local Group Policy

Definition: A management tool that allows administrators to set, configure, and adjust settings on a Windows machine.

Scope: Can be applied locally or via Domain Controller in a domain environment using Group Policy Objects (GPOs).

Purpose:

- Configure system, network, and graphical settings not accessible via the Control Panel.
- Enforce security policies on individual machines, such as restricting application execution or enforcing strong user account password rules.

Structure: Local Group Policy Editor is divided into:
- **Computer Configuration** – Settings that apply to the entire machine.
- **User Configuration** – Settings that apply to individual user accounts.

Access: Open via Start Menu → type `gpedit.msc`.

---

