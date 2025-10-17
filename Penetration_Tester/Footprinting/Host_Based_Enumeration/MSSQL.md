`Microsoft SQL` (`MSSQL`) is `Microsoft`'s `SQL-based relational database management system`. Unlike `MySQL`, `MSSQL` is `closed source` and was originally developed for `Windows`. It has tight, native support for the `.NET framework`, which drives its popularity with `database administrators` and `developers` building `.NET` applications. While some `MSSQL` versions run on `Linux` and `macOS`, most `MSSQL instances` you encounter in the wild will be on `Windows` hosts.

#### MSSQL Clients

`SQL Server Management Studio` (`SSMS`) can be installed with the `MSSQL` package or separately. It’s often placed on the server for initial configuration and long-term `database` administration, but as a `client-side` tool it may also be installed on any admin or developer workstation. Hosts with `SSMS` and `saved credentials` present an attack vector: compromised or vulnerable systems can expose `database` access. The screenshot shows `SSMS` in use.

| [mssql-cli](https://docs.microsoft.com/en-us/sql/tools/mssql-cli?view=sql-server-ver15) | [SQL Server PowerShell](https://docs.microsoft.com/en-us/sql/powershell/sql-server-powershell?view=sql-server-ver15) | [HeidiSQL](https://www.heidisql.com) | [SQLPro](https://www.macsqlclient.com) | [Impacket's mssqlclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py) |
| --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | -------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
Of the `MSSQL clients` listed above, `pentesters` may find Impacket's `mssqlclient.py` to be the most useful due to SecureAuthCorp's Impacket project being present on many `pentesting` distributions at install. To find if and where the `client` is located on our host, we can use the following command:

```bash
OathBornX@htb[/htb]$ locate mssqlclient

/usr/bin/impacket-mssqlclient
/usr/share/doc/python3-impacket/examples/mssqlclient.py
```

#### MSSQL Databases

`MSSQL` has default `system databases` that can help us understand the structure of all the `databases` that may be hosted on a `target server`. Here are the `default databases` and a brief description of each:

| System Database |                                                                                              Description                                                                                               |
| :-------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|    `master`     |                                                                        Tracks all system information for an SQL server instance                                                                        |
|     `model`     | Template database that acts as a structure for every new database created. Any setting changed in the model database will be reflected in any new database created after changes to the model database |
|     `msdb`      |                                                                   The SQL Server Agent uses this database to schedule jobs & alerts                                                                    |
|    `tempdb`     |                                                                                        Stores temporary objects                                                                                        |
|   `resource`    |                                                                 Read-only database containing system objects included with SQL server                                                                  |

---

# Default Configuration

When an admin enables network access the `SQL` service commonly runs as `NT SERVICE\MSSQLSERVER`. Client connections typically use `Windows Authentication` and, by default, `encryption` is not enforced for those connections, leaving traffic vulnerable unless `SSL/TLS` or server-side `Force Encryption` is configured.

When `Windows Authentication` is enabled, the `Windows OS` handles `login` requests through the local `SAM database` or the `domain controller` hosting `Active Directory`. This integration simplifies `auditing` and `access control` in Windows environments, but if an `account` is compromised, it can lead to `privilege escalation` and `lateral movement` across the `domain`.

To fully understand default configurations and potential `misconfigurations`, it is recommended to deploy `MSSQL` in a `virtual machine` for controlled installation and configuration testing.

---

# Dangerous Settings 

`Place ourselves in the perspective of an IT administrator` to anticipate `misconfigurations` that expose `servers` and `services`. High workload, tight deadlines, and numerous simultaneous projects make it easy for an admin to accidentally introduce a single `misconfiguration` that compromises a critical `server` or `service` on the `network`. This applies to any role, including `MSSQL` administration.

Testing installations in a `virtual machine` from install to configuration reveals default settings and common admin mistakes, improving detection of insecure deployments and reducing `attack surface`.

- `MSSQL clients` not using `encryption` to connect to the `MSSQL server`
- Use of `self-signed certificates` when `encryption` is enabled (possible to spoof)
- Use of `named pipes` for connectivity
- Weak or default `sa` credentials left enabled

---

# Footprinting the Service

Using `Nmap` against `TCP port 1433` revealed the target `hostname`, `database instance name`, and `MSSQL` `software version` the scan also shows `named pipes` are enabled. Record these discoveries in the engagement `notes` and treat them as unconfirmed until manually validated.

```bash
OathBornX@htb[/htb]$ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248

Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-08 09:40 EST
Nmap scan report for 10.129.201.248
Host is up (0.15s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: SQL-01
|   NetBIOS_Domain_Name: SQL-01
|   NetBIOS_Computer_Name: SQL-01
|   DNS_Domain_Name: SQL-01
|   DNS_Computer_Name: SQL-01
|_  Product_Version: 10.0.17763

Host script results:
| ms-sql-dac: 
|_  Instance: MSSQLSERVER; DAC port: 1434 (connection failed)
| ms-sql-info: 
|   Windows server name: SQL-01
|   10.129.201.248\MSSQLSERVER: 
|     Instance name: MSSQLSERVER
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|     TCP port: 1433
|     Named pipe: \\10.129.201.248\pipe\sql\query
|_    Clustered: false

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.52 seconds
```

We can also use `Metasploit` to run an `auxiliary scanner` called `mssql_ping` that will scan the `MSSQL` service and provide helpful information in our `footprinting process`.

```bash
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248

rhosts => 10.129.201.248


msf6 auxiliary(scanner/mssql/mssql_ping) > run

[*] 10.129.201.248:       - SQL Server information for 10.129.201.248:
[+] 10.129.201.248:       -    ServerName      = SQL-01
[+] 10.129.201.248:       -    InstanceName    = MSSQLSERVER
[+] 10.129.201.248:       -    IsClustered     = No
[+] 10.129.201.248:       -    Version         = 15.0.2000.5
[+] 10.129.201.248:       -    tcp             = 1433
[+] 10.129.201.248:       -    np              = \\SQL-01\pipe\sql\query
[*] 10.129.201.248:       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

#### Connecting with Mssqlclient.py

If we can guess or obtain `credentials` we can remotely authenticate to the `MSSQL server` and operate against databases using `T-SQL` (Transact-SQL). Successful authentication grants access to the `SQL Database Engine` and lets an attacker enumerate and manipulate data from `Pwnbox` or a dedicated host using tools like `Impacket`'s `mssqlclient.py`. After connecting, the first step is to enumerate the `databases` to get a lay of the land for further discovery and exploitation.

```bash
OathBornX@htb[/htb]$ python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(SQL-01): Line 1: Changed database context to 'master'.
[*] INFO(SQL-01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
[!] Press help for extra shell commands

SQL> select name from sys.databases

name                                                                                                                               

--------------------------------------------------------------------------------------

master                                                                        
tempdb                                                                                                        
model                                                                               
msdb                                                                                                           
Transactions
```

