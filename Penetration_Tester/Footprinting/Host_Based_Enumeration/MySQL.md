`MySQL` is an open-source `SQL` relational `database management system` developed and maintained by `Oracle`. A `database` is a structured collection of data designed for efficient organization, access, and retrieval.

`MySQL` can process large volumes of data with high performance while minimizing storage usage. Data within the `database` is stored in a compact, optimized format.

The system is managed using the `SQL` language, which allows users to define, manipulate, and query data efficiently.

--- 
#### MySQL Databases

`MySQL` is ideal for applications such as dynamic websites that require efficient syntax and high response speed. It is commonly paired with `Linux` OS, `PHP`, and an `Apache` web server—collectively known as `LAMP` (`Linux`, `Apache`, `MySQL`, `PHP`). When `Nginx` is used instead of `Apache`, the stack is referred to as `LEMP`.

In `web hosting` environments using a `MySQL database`, it serves as the central instance where content needed by `PHP scripts` is stored. Among these are:

| Headers                 | Texts            | Meta tags         | Forms      |
| ----------------------- | ---------------- | ----------------- | ---------- |
| Customers               | Usernames        | Administrators    | Moderators |
| Email addresses         | User information | Permissions       | Passwords  |
| External/Internal links | Links to Files   | Specific contents | Values     |

---
#### MySQL Commands

A `MySQL database` internally translates commands into executable code and executes the requested operations. When an error occurs, the `web application` reports it to the user—an event that `SQL injection` attacks can deliberately trigger. These error messages often reveal sensitive details and confirm that the `web application` communicates with the `database` in unintended ways.

If the `web application` processes the data successfully, it sends the generated output back to the `client`. This can include table data, record extracts, or information used for `login` and `search` functionalities. `SQL commands` can display, modify, insert, or delete table rows. They can also alter table structures, manage `relationships` and `indexes`, and handle `user` permissions.

`MariaDB` is closely related to `MySQL`—it is a fork of the original `MySQL` code. After `Oracle` acquired MySQL AB, the original lead developer created `MariaDB` as an open-source `SQL database management system` based on the same source code.

---

# Default Configurations

The management and configuration of `SQL databases` is an extensive discipline, forming the primary focus of professions like `database administrators` who specialize in `database` architecture and performance. These systems can scale rapidly, making their design and maintenance complex.

Effective `database management` is a fundamental skill not only for `software developers` but also for `information security analysts`, as secure and efficient data handling is critical to both `application security` and `incident response` operations.

```bash
OathBornX@htb[/htb]$ sudo apt install mysql-server -y
OathBornX@htb[/htb]$ cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'

[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock

[mysqld_safe]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
skip-host-cache
skip-name-resolve
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /var/lib/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
explicit_defaults_for_timestamp

symbolic-links=0

!includedir /etc/mysql/conf.d/
```

---

# Dangerous Settings

|**Settings**|**Description**|
|---|---|
|`user`|Sets which user the MySQL service will run as.|
|`password`|Sets the password for the MySQL user.|
|`admin_address`|The IP address on which to listen for TCP/IP connections on the administrative network interface.|
|`debug`|This variable indicates the current debugging settings|
|`sql_warnings`|This variable controls whether single-row INSERT statements produce an information string if warnings occur.|
|`secure_file_priv`|This variable is used to limit the effect of data import and export operations.|
A `MySQL` `configuration file` often contains `user`, `password`, and `admin_address` entries in plaintext; these are `security-relevant`. Incorrect `file permissions` on the `MySQL server` config allow an attacker who can `read files` or obtain a `shell` to exfiltrate the `username` and `password` and thereby access the entire `database`. If no additional `access controls` or `network segmentation` exist, an attacker can view and modify `customer` records, including `email addresses`, `passwords`, and other `personal data`.

The `debug` and `sql_warnings` settings enable `verbose` error output useful for administrators but dangerous when exposed. Error messages commonly leak implementation details that attackers can use to enumerate attack vectors by `trial and error`. These error outputs are often rendered by the `web application`, making them reachable via crafted requests. Consequently, `SQL injection` vectors can be amplified by verbose errors — up to and including remote execution of `system commands` on the `MySQL` host under certain conditions.

---

# Footprinting the Service

`MySQL server` instances are sometimes reachable from an `external network` despite this being against `best practices`. Such `exposure` commonly stems from `temporary` configuration changes left by `administrators` or ad-hoc `workarounds` for technical problems. The default listening port is `TCP port 3306`; reachable instances can be discovered by scanning that port with `Nmap` to enumerate service and version details. Any external accessibility significantly increases the `attack surface` and risk of unauthorized data access or modification.

```bash
OathBornX@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 00:53 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE     VERSION
3306/tcp open  nagios-nsca Nagios NSCA
| mysql-brute: 
|   Accounts: 
|     root:<empty> - Valid credentials
|_  Statistics: Performed 45010 guesses in 5 seconds, average tps: 9002.0
|_mysql-databases: ERROR: Script execution failed (use -d to debug)
|_mysql-dump-hashes: ERROR: Script execution failed (use -d to debug)
| mysql-empty-password: 
|_  root account has empty password
| mysql-enum: 
|   Valid usernames: 
|     root:<empty> - Valid credentials
|     netadmin:<empty> - Valid credentials
|     guest:<empty> - Valid credentials
|     user:<empty> - Valid credentials
|     web:<empty> - Valid credentials
|     sysadmin:<empty> - Valid credentials
|     administrator:<empty> - Valid credentials
|     webadmin:<empty> - Valid credentials
|     admin:<empty> - Valid credentials
|     test:<empty> - Valid credentials
|_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.26-0ubuntu0.20.04.1
|   Thread ID: 13
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, SupportsTransactions, Speaks41ProtocolOld, LongPassword, DontAllowDatabaseTableColumn, Support41Auth, IgnoreSigpipes, SwitchToSSLAfterHandshake, FoundRows, InteractiveClient, Speaks41ProtocolNew, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, LongColumnFlag, SupportsCompression, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: YTSgMfqvx\x0F\x7F\x16\&\x1EAeK>0
|_  Auth Plugin Name: caching_sha2_password
|_mysql-users: ERROR: Script execution failed (use -d to debug)
|_mysql-variables: ERROR: Script execution failed (use -d to debug)
|_mysql-vuln-cve2012-2122: ERROR: Script execution failed (use -d to debug)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.21 seconds
```

As with all `scans`, validate results manually because some findings are `false-positive`. This `scan` is a textbook case: the target `MySQL server` does not use an empty `root` `password` — it uses a fixed one. Do not trust a single scan; confirm `authentication` and `configuration` directly before pivoting.
#### Interaction with the MySQL Server

```bash
OathBornX@htb[/htb]$ mysql -u root -h 10.129.14.132

ERROR 1045 (28000): Access denied for user 'root'@'10.129.14.1' (using password: NO)
```

For example, if we use a password that we have guessed or found through our research, we will be able to log in to the MySQL server and execute some commands.

```bash
OathBornX@htb[/htb]$ mysql -u root -pP4SSw0rd -h 10.129.14.128

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 150165
Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)                                                         
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.                                     
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.                           
      
MySQL [(none)]> show databases;                                                                          
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.006 sec)
```

If we look at the existing databases, we will see several already exist. The most important databases for the MySQL server are the `system schema` (`sys`) and `information schema` (`information_schema`).

```bash
mysql> use sys;
mysql> show tables;  

+-----------------------------------------------+
| Tables_in_sys                                 |
+-----------------------------------------------+
| host_summary                                  |
| host_summary_by_file_io                       |
| host_summary_by_file_io_type                  |
| host_summary_by_stages                        |
| host_summary_by_statement_latency             |
| host_summary_by_statement_type                |
| innodb_buffer_stats_by_schema                 |
| innodb_buffer_stats_by_table                  |
| innodb_lock_waits                             |
| io_by_thread_by_latency                       |
...SNIP...
| x$waits_global_by_latency                     |
+-----------------------------------------------+


mysql> select host, unique_users from host_summary;

+-------------+--------------+                   
| host        | unique_users |                   
+-------------+--------------+                   
| 10.129.14.1 |            1 |                   
| localhost   |            2 |                   
+-------------+--------------+                   
2 rows in set (0,01 sec)  
```

The `information schema` is also a database that contains metadata. However, this metadata is mainly retrieved from the `system schema` database. The reason for the existence of these two is the ANSI/ISO standard that has been established. `System schema` is a Microsoft system catalog for SQL servers and contains much more information than the `information schema`.

|**Command**|**Description**|
|---|---|
|`mysql -u <user> -p<password> -h <IP address>`|Connect to the MySQL server. There should **not** be a space between the '-p' flag, and the password.|
|`show databases;`|Show all databases.|
|`use <database>;`|Select one of the existing databases.|
|`show tables;`|Show all available tables in the selected database.|
|`show columns from <table>;`|Show all columns in the selected table.|
|`select * from <table>;`|Show everything in the desired table.|
|`select * from <table> where <column> = "<string>";`|Search for needed `string` in the desired table.|
