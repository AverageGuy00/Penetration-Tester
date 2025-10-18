
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


