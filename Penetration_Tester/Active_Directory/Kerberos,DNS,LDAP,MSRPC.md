# Kerberos 

`Kerberos` has been the default authentication protocol for domain accounts since Windows 2000, allowing interoperability with other systems using the same standard. When a user logs in, `Kerberos` performs `mutual authentication`, letting both the user and server verify identities. It uses `tickets` instead of sending `passwords` over the network, making it a stateless authentication method. Domain Controllers in Active Directory run a `Key Distribution Center (KDC)` that issues tickets. During login, the client requests a ticket from the `KDC`, encrypting the request with the user’s `password`. If the `KDC` can decrypt it, it issues a `Ticket Granting Ticket (TGT)`. The `TGT` is then used to request a `Ticket Granting Service (TGS)` ticket for a specific `service`, encrypted with the service's `password hash`. The client presents the `TGS` to access the service, which validates it and grants access. This process separates `user credentials` from service requests, keeping passwords off the network. The `KDC` does not track transactions; the validity of the `TGS` depends on a valid `TGT`, which proves the user’s identity.

![[Pasted image 20250923134237.png]]

The `Kerberos` protocol uses `port 88` (`TCP` and `UDP`). When enumerating an `Active Directory` environment, we can often locate `Domain Controllers` by performing `port scans` looking for open `port 88` using a tool such as `Nmap`.

---

# DNS

`Active Directory Domain Services (AD DS)` uses `DNS` to allow `clients` (workstations, servers, and other systems) to locate `Domain Controllers` and for `Domain Controllers` to communicate with each other. `DNS` resolves `hostnames` to `IP addresses` and is widely used across internal networks and the internet. Internal networks use `Active Directory DNS namespaces` to facilitate communication between `servers`, `clients`, and `peers`. AD maintains a database of services as `service records (SRV)`, which let `clients` locate needed services like `file servers`, `printers`, or `Domain Controllers`. `Dynamic DNS` automatically updates the DNS database if a system’s `IP address` changes, avoiding manual errors. When a `client` joins the network, it queries `DNS` for an `SRV` record to find the `Domain Controller`, then uses the hostname to get the `IP address`. `DNS` uses `TCP` and `UDP port 53`, with `UDP` as default and `TCP` for messages larger than 512 bytes or when communication fails.

```
Client
   |
   | Query for Domain Controller (SRV record)
   v
DNS Server (resolves hostname to IP)
   |
   | Responds with Domain Controller hostname/IP
   v
Client
   |
   | Connects to Domain Controller
   v
Domain Controller
   |
   | Provides authentication/service info
   v
Client
   |
   | Accesses internal service (e.g., file server, printer, web app)
   v
Service
```

## Forward DNS Lookup

Forward DNS Lookup lets you query a `domain name` to get the `IP addresses` of `Domain Controllers`.

```nginx
PS C:\htb> nslookup INLANEFREIGHT.LOCAL
Server:  172.16.6.5
Address:  172.16.6.5
Name:    INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```

# Reverse DNS Lookup

Reverse DNS Lookup lets you query an `IP address` to get the `DNS name` of a host.

```nginx
PS C:\htb> nslookup 172.16.6.5
Server:  172.16.6.5
Address:  172.16.6.5
Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```

# Finding IP Address of a Host

Finding the `IP address` of a host can be done in reverse, using the `hostname` or `FQDN`.

```
PS C:\htb> nslookup ACADEMY-EA-DC01
Server:   172.16.6.5
Address:  172.16.6.5
Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```


---

# LDAP

Active Directory supports `Lightweight Directory Access Protocol (LDAP)` for directory lookups. `LDAP` is an open-source, cross-platform protocol used for authentication against directory services like `AD`. The latest version is `LDAPv3` (RFC 4511). Understanding `LDAP` in an AD environment is important for both attackers and defenders. `LDAP` uses `port 389`, and `LDAPS` (LDAP over SSL) uses `port 636`.

`AD` stores `user account` and `security information` (like `passwords`) and shares it across the network. `LDAP` is the protocol applications use to communicate with directory servers, essentially letting systems "speak" to `AD`. An `LDAP session` starts by connecting to an `LDAP server` (Directory System Agent). The `Domain Controller` listens for `LDAP requests`, including `authentication` queries.

![[Pasted image 20250923175510.png]]

The relationship between `AD` and `LDAP` is like `Apache` and `HTTP`: just as `Apache` is a web server that uses the `HTTP` protocol, `Active Directory` is a directory server that uses the `LDAP` protocol.

## LDAP Authentication

`LDAP` authenticates credentials against `AD` using a `BIND` operation to establish the authentication state for an `LDAP session`. There are two main types of `LDAP authentication`:

`1.` `Simple Authentication` includes anonymous, unauthenticated, or `username/password` authentication. A `BIND` request with the username and password is sent to the `LDAP server` to authenticate.

`2.` `SASL Authentication` (Simple Authentication and Security Layer) uses an external authentication service, like `Kerberos`, to bind to the `LDAP server`. The `LDAP server` communicates with the authorization service via a series of challenge/response messages to determine successful or failed authentication. `SASL` enhances security by separating the authentication method from the application protocol.