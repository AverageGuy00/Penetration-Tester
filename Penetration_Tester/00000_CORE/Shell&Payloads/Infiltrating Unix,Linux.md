As you may very well have noticed by now, gaining a `shell` session with a system can be done in various ways, one common way is through a vulnerability in an `application`. We will identify a `vulnerability` and discover an `exploit` that we can use to gain a `shell` by delivering a `payload`.

#### Common Considerations

- What distribution of Linux is the system running?
- What shell & programming languages exist on the system?
- What function is the system serving for the network environment it is on?
- What application is the system hosting?
- Are there any known vulnerabilities?

#### Discovering vulnerability in a service used by the web page

![[{6A49360F-F757-4913-97D8-A36D08147FF2}.png]]

#### Search For an Exploit Module

![[{7C079107-7541-4B02-9720-333B2A617117}.png]]

One detail often overlooked when relying on `MSF` for exploit modules is the version of `MSF` itself. Useful exploit modules may not be installed or might not appear in search results. Rapid7 maintains their exploit modules’ source code on `GitHub`. A targeted search like `rConfig 3.9.6 exploit metasploit github` can lead to modules such as `rconfig_vendors_auth_file_upload_rce.rb`—an exploit for gaining a shell on Linux hosts running `rConfig 3.9.6`. If missing locally, you can download the module’s source code from the repo and place it in your local `MSF` modules directory to use it.

#### Execute the Exploit

![[{A2044D4A-70AD-40AA-ABFB-9E052F58535C}.png]]

#### Interact With the Shell

![[{FD0904D0-2C8A-496D-84FD-CCDC971A61D0}.png]]

We can see from the steps outlined in the exploitation process that this exploit:

- Checks for the vulnerable version of rConfig
- Authenticates with the rConfig web login
- Uploads a PHP-based payload for a reverse shell connection
- Deletes the payload
- Leaves us with a Meterpreter shell session