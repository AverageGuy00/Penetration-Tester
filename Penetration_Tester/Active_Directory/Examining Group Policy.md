
`Group Policy` provides administrators with advanced controls for both users and computers in a `Windows` environment. In a `domain` context, it is one of the most effective tools for managing configuration and enforcing `security` across the enterprise. Since `Active Directory` is not secure by default, properly applied `Group Policy` is a key part of a `defense-in-depth` strategy.

However, attackers can also abuse `Group Policy`. Gaining control of a `Group Policy Object (GPO)` may enable `lateral movement`, `privilege escalation`, persistence, or even full `domain compromise`. Understanding how `Group Policy` operates helps defenders strengthen posture and gives penetration testers the ability to spot subtle misconfigurations others might overlook.

---

#  Group Policy Objects (GPOs)

A `Group Policy Object (GPO)` is a collection of policy settings that control the configuration and behavior of `user accounts` and `computers` within an `Active Directory` environment. These policies can enforce security rules (e.g., `screen lock timeouts`, `password complexity`), restrict hardware usage (e.g., disabling `USB ports`), install and manage `software`, customize `remote access`, and more.

Each `GPO` has a unique name and a globally unique identifier (`GUID`). A `GPO` can be linked to multiple containers such as `Organizational Units (OUs)`, `domains`, or `sites`, and any container can have multiple `GPOs` applied. Through `OU linking`, `GPOs` can target specific `users`, `groups`, or `computers`.

Inside each `GPO`, one or more `policy settings` exist, which may apply locally at the `machine level` or across the `domain` via `Active Directory`.

## Example GPOs

Some examples of what can be done with `Group Policy Objects (GPOs)` include:

- Establishing different `password policies` for `service accounts`, `admin accounts`, and `standard user accounts` using separate `GPOs`
- Preventing the use of `removable media devices` such as `USB devices`
- Enforcing a `screensaver` with a `password`
- Restricting access to applications like `cmd.exe` and `PowerShell` for `standard users`
- Enforcing `audit` and `logging policies`
- Blocking users from running specific `programs` or `scripts`
- Deploying `software` across the `domain`
- Blocking users from installing `unapproved software`
- Displaying a `logon banner` whenever a user signs in
- Disallowing `LM hash` usage in the `domain`
- Running `scripts` during `computer startup/shutdown` or `user login/logout`

----

# RDP GPO Settings

![[Pasted image 20250930161038.png]]

`GPO` settings are processed using the hierarchical structure of `AD` and are applied using the `Order of Precedence` rule as seen in the table below:

#### Order of Precedence

|                 **Level**                  |                                                                                                                                                                                                                                                                                                                                                                 **Description**                                                                                                                                                                                                                                                                                                                                                                 |
| :----------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|            `Local Group Policy`            |                                                                                                                                                                                                                                                                                        The policies are defined directly to the host locally outside the domain. Any setting here will be overwritten if a similar setting is defined at a higher level.                                                                                                                                                                                                                                                                                        |
|               `Site Policy`                | Any policies specific to the Enterprise Site that the host resides in. Remember that enterprise environments can span large campuses and even across countries. So it stands to reason that a site might have its own policies to follow that could differentiate it from the rest of the organization. Access Control policies are a great example of this. Say a specific building or `site` performs secret or restricted research and requires a higher level of authorization for access to resources. You could specify those settings at the site level and ensure they are linked so as not to be overwritten by domain policy. This is also a great way to perform actions like printer and share mapping for users in specific sites. |
|            `Domain-wide Policy`            |                                                                                                                                                                                                                                          Any settings you wish to have applied across the domain as a whole. For example, setting the password policy complexity level, configuring a Desktop background for all users, and setting a Notice of Use and Consent to Monitor banner at the login screen.                                                                                                                                                                                                                                          |
|         `Organizational Unit` (OU)         |                                                                                                                                                                                          These settings would affect users and computers who belong to specific OUs. You would want to place any unique settings here that are role-specific. For example, the mapping of a particular share drive that can only be accessed by HR, access to specific resources like printers, or the ability for IT admins to utilize PowerShell and command-prompt.                                                                                                                                                                                          |
| `Any OU Policies nested within other OU's` |                                                                                                                                                                                                                                                        Settings at this level would reflect special permissions for objects within nested OUs. For example, providing Security Analysts a specific set of Applocker policy settings that differ from the standard IT Applocker settings.                                                                                                                                                                                                                                                        |

We can manage `Group Policy` using the `Group Policy Management Console (GPMC)` found under `Administrative Tools` in the `Start Menu` on a `Domain Controller`. It can also be managed through `custom applications` or the `PowerShell GroupPolicy module` via command line.

The `Default Domain Policy` is the automatically created `GPO` linked to the `domain`. It has the **highest precedence** of all `GPOs` and applies by default to **all users and computers**. Best practice is to use this `GPO` to manage **default settings** that apply domain-wide.

The `Default Domain Controllers Policy` is also created automatically and sets **baseline security** and **auditing settings** for all `Domain Controllers` in a domain. Like any `GPO`, it can be customized as needed.

---

# GPO Order of Precedence

`Group Policy Objects (GPOs)` follow a top-down processing order, starting from the `domain level` and moving down through `child Organizational Units (OUs)` until they reach the `OU containing the user or computer`. Because of this order, the `GPO closest to the object` is applied last and therefore takes `precedence`, overriding any conflicting settings from `higher-level GPOs`. For example, if a `domain-level GPO` requires a `12-character password`, but the `OU-level GPO` enforces a `15-character minimum`, the `15-character rule` will win because it’s applied last. The same logic applies across other settings, meaning the `closest GPO has the final say`. One important exception is that `Computer policies override User policies` when both configure the same setting.


![[Pasted image 20250930162407.png]]

### Here’s a simple visualization of how GPO `precedence` works when policies **conflict**:

```
[Domain GPO]       Disable force restart = True
        ↓
[Child OU GPO]     Disable force restart = False
        ↓
[Target Object]    Effective = False  (OU wins because it's closer)
```

### And when there’s **no conflict**:

```
[Domain GPO]       Enforce 12-char password
        ↓
[Child OU GPO]     Disable USB ports
        ↓
[Target Object]    Effective = Both (12-char + USB disabled)

```

---
# Enforced GPO Policy Precedence

`Enforced` can be applied to a `GPO` to prevent lower-level `OUs` from overriding its settings. For example, if a `domain-level GPO` is set to Enforced, its rules will always apply to all `OUs` beneath it, no matter what those `OU-level GPOs` configure. This was previously called `No Override`. An example is an Enforced `Logon Banner GPO`, which keeps its settings in place even if lower `OU policies` attempt to change them.

![[Pasted image 20250930164637.png]]

# Default Domain Policy Override

Regardless of which GPO is set to enforced, if the `Default Domain Policy` GPO is enforced, it will take precedence over all GPOs at all levels.

![[Pasted image 20250930165624.png]]

It is possible to set the `Block inheritance` option on an `OU`. When enabled, `GPOs` from higher-level containers (like the `domain`) will **not** apply to this `OU`. If both `Block inheritance` and `Enforced` (formerly `No Override`) are set, the `Enforced` setting takes precedence. For example, the `Computers OU` can inherit `GPOs` from the `Corp OU` unless `Block inheritance` or `Enforced` changes how they apply.

![[Pasted image 20250930165712.png]]

If the `Block Inheritance` option is chosen, we can see that the 3 GPOs applied higher up to the `Corp` OU are no longer enforced on the `Computers` OU.

### Block Inheritance

![[Pasted image 20250930165738.png]]

---

# Group Policy Refresh Frequency

When a new `GPO` is created, its settings are **not applied immediately**. Windows performs periodic `Group Policy` updates: by default, every `90 minutes` with a randomized offset of `+/- 30 minutes` for `users` and `computers`. For `Domain Controllers`, the update period is `5 minutes` by default. Because of this, a newly linked `GPO` could take up to `120 minutes` to take effect. The random offset helps prevent all clients from requesting `Group Policy` simultaneously, avoiding overload on the `Domain Controllers`.

It is possible to change the default refresh interval in `Group Policy` itself. We can also use the command `gpupdate /force` to immediately apply updates. This command compares the `GPOs` currently applied on the machine with those on the `Domain Controller` and updates only the settings that have changed.

The refresh interval can be modified via `Group Policy`: navigate to `Computer Configuration --> Policies --> Administrative Templates --> System --> Group Policy` and select `Set Group Policy refresh interval for computers`. While it can be changed, setting it too frequently may cause network congestion and replication issues.

![[Pasted image 20250930204925.png]]

---

# Security Considerations of GPOs

`GPO` abuse can be powerful. An attacker who can modify a `GPO` that applies to an OU containing a `user` or `computer` they control can: add rights to that account, add a `local administrator`, create an immediate `scheduled task` to run a malicious command (e.g., spawn a `reverse shell`), modify `group membership`, add an admin account, or push targeted `malware` across the `domain`.

These attacks often stem from misconfigured `permissions` (including `nested group membership`) that let low-privilege groups edit `GPOs`. Tools like `BloodHound` can map these paths — for example, showing that `Domain Users` can modify the `Disconnect Idle RDP` `GPO`. From there, an attacker would identify which `OUs` that `GPO` affects and try to leverage those rights to control a high-value `user` (admin or `Domain Admin`) or `computer` (server, `DC`, or other critical host) to enable `lateral movement` and `privilege escalation`.

![[Pasted image 20250930205123.png]]

