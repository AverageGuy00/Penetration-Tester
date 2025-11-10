As we discussed, `Metasploit modules` are prewritten scripts with specific purposes and built-in functions. The `exploit` category contains `proof-of-concept (POC)` code that automates attempts to trigger known `vulnerabilities`. A failed `exploit` run only shows the `Metasploit` module didn't succeed — it does **not** prove the `vulnerability` is absent. Many `exploits` require `customization` for the target environment or `target hosts` to work reliably. Treat `automated tools` like the `Metasploit Framework` as force multipliers, not replacements for `manual skills`.

- `Exploit` modules are often `POCs` and may need manual tuning to match the target.
- A failed module run ≠ no `vulnerability`; it means the module didn’t work against that target as-is.
- Always validate findings with manual testing and environment-specific adjustments.
- Use `automated tools` for speed and coverage; rely on `manual skills` for accuracy and exploitation creativity

#### Syntax

```bash
<No.> <type>/<os>/<service>/<name>
```
#### Example

```bash
794   exploit/windows/ftp/scriptftp_list
```
#### Index No.

The `No.` tag will be displayed to select the exploit we want afterward during our searches. We will see how helpful the `No.` tag can be to select specific Metasploit modules later.

#### Type

The `Type` tag is the first-level classification for Metasploit modules — it tells you what the module **does** and how you interact with it.

|**Type**|**Description**|
|---|---|
|`Auxiliary`|Scanning, fuzzing, sniffing, and admin capabilities. Offer extra assistance and functionality.|
|`Encoders`|Ensure that payloads are intact to their destination.|
|`Exploits`|Defined as modules that exploit a vulnerability that will allow for the payload delivery.|
|`NOPs`|(No Operation code) Keep the payload sizes consistent across exploit attempts.|
|`Payloads`|Code runs remotely and calls back to the attacker machine to establish a connection (or shell).|
|`Plugins`|Additional scripts can be integrated within an assessment with `msfconsole` and coexist.|
|`Post`|Wide array of modules to gather information, pivot deeper, etc.|

Note that when selecting a module to use for payload delivery, the `use <no.>` command can only be used with the following modules that can be used as `initiators` (or interactable modules):

| **Type**    | **Description**                                                                                |
| ----------- | ---------------------------------------------------------------------------------------------- |
| `Auxiliary` | Scanning, fuzzing, sniffing, and admin capabilities. Offer extra assistance and functionality. |
| `Exploits`  | Defined as modules that exploit a vulnerability that will allow for the payload delivery.      |
| `Post`      | Wide array of modules to gather information, pivot deeper, etc.                                |
#### OS

The `OS` tag specifies which operating system and architecture the module was created for. Naturally, different operating systems require different code to be run to get the desired results.
#### Service

The `Service` tag refers to the vulnerable service that is running on the target machine. For some modules, such as the `auxiliary` or `post` ones, this tag can refer to a more general activity such as `gather`, referring to the gathering of credentials, for example.
#### Name

Finally, the `Name` tag explains the actual action that can be performed using this module created for a specific purpose.

---
#### MSF - Search Function

```bash
msf6 > help search

Usage: search [<options>] [<keywords>:<value>]
```
#### MSF - Searching for EternalRomance

```bash
msf6 > search eternalromance

Matching Modules
================

   #  Name                                  Disclosure Date  Rank    Check  Description
   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   1  auxiliary/admin/smb/ms17_010_command  2017-03-14       normal  No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
```

We can also make our search a bit more coarse and reduce it to one category of services. For example, for the CVE, we could specify the year (`cve:<year>`), the platform Windows (`platform:<os>`), the type of module we want to find (`type:<auxiliary/exploit/post>`), the reliability rank (`rank:<rank>`), and the search name (`<pattern>`). This would reduce our results to only those that match all of the above.
#### MSF - Specific Search

```bash
msf6 > search type:exploit platform:windows cve:2021 rank:excellent microsoft

Matching Modules
================

   #  Name                                            Disclosure Date  Rank       Check  Description
   -  ----                                            ---------------  ----       -----  -----------
   0  exploit/windows/http/exchange_proxylogon_rce    2021-03-02       excellent  Yes    Microsoft Exchange ProxyLogon RCE
   1  exploit/windows/http/exchange_proxyshell_rce    2021-04-06       excellent  Yes    Microsoft Exchange ProxyShell RCE
   2  exploit/windows/http/sharepoint_unsafe_control  2021-05-11       excellent  Yes    Microsoft SharePoint Unsafe Control and ViewState RCE
```
#### MSF - Module Information

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > info

       Name: MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
     Module: exploit/windows/smb/ms17_010_psexec
   Platform: Windows
       Arch: x86, x64
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Normal
  Disclosed: 2017-03-14

Provided by:
  sleepya
  zerosum0x0
  Shadow Brokers
  Equation Group
```


