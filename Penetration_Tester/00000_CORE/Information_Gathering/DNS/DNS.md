The `Domain Name System` (`DNS`) acts as the internet's GPS, guiding your online journey from memorable landmarks (domain names) to precise numerical coordinates (IP addresses).

--- 

# How DNS works

![[Pasted image 20251019170210.png]]

`Your computer` sends a `DNS query` when you enter a domain name, first checking its `cache` for the IP address. If it’s not found, it contacts a `DNS resolver` provided by your `ISP`.

The `DNS resolver` performs a `recursive lookup` if its own cache doesn’t have the answer, starting by querying a `root name server`.

The `root name server` doesn’t know the exact IP but directs the resolver to the appropriate `Top-Level Domain (TLD) name server` (like .com or .org).

The `TLD name server` points the resolver to the correct `authoritative name server` responsible for that specific domain.

The `authoritative name server` returns the actual `IP address` of the domain to the resolver.

The `DNS resolver` sends the IP address back to your computer and stores it temporarily in its `cache`.

Your computer`then uses that IP address to connect directly to the`web server`` hosting the website.

---

# Key DNS Concepts

In the `Domain Name System (DNS)`, a `zone` is a distinct part of the `domain namespace` managed by a specific administrator. It acts as a container for related domain names. For example, `example.com` and its subdomains like `mail.example.com` or `blog.example.com` typically belong to the same `DNS zone`.

The `zone file` is a text file stored on a `DNS server` that defines `resource records` within the zone, containing key data for translating `domain names` into `IP addresses`.

```bash
$TTL 3600 ; Default Time-To-Live (1 hour)
@       IN SOA   ns1.example.com. admin.example.com. (
                2024060401 ; Serial number (YYYYMMDDNN)
                3600       ; Refresh interval
                900        ; Retry interval
                604800     ; Expire time
                86400 )    ; Minimum TTL

@       IN NS    ns1.example.com.
@       IN NS    ns2.example.com.
@       IN MX 10 mail.example.com.
www     IN A     192.0.2.1
mail    IN A     198.51.100.1
ftp     IN CNAME www.example.com.
```

This file defines the `authoritative name servers` (`NS` records), mail server (`MX` record), and IP addresses (`A` records) for various hosts within the `example.com` domain.

| DNS Concept                 | Description                                                                      | Example                                                                                                                                 |
| --------------------------- | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `Domain Name`               | A human-readable label for a website or other internet resource.                 | `www.example.com`                                                                                                                       |
| `IP Address`                | A unique numerical identifier assigned to each device connected to the internet. | `192.0.2.1`                                                                                                                             |
| `DNS Resolver`              | A server that translates domain names into IP addresses.                         | Your ISP's DNS server or public resolvers like Google DNS (`8.8.8.8`)                                                                   |
| `Root Name Server`          | The top-level servers in the DNS hierarchy.                                      | There are 13 root servers worldwide, named A-M: `a.root-servers.net`                                                                    |
| `TLD Name Server`           | Servers responsible for specific top-level domains (e.g., .com, .org).           | [Verisign](https://en.wikipedia.org/wiki/Verisign) for `.com`, [PIR](https://en.wikipedia.org/wiki/Public_Interest_Registry) for `.org` |
| `Authoritative Name Server` | The server that holds the actual IP address for a domain.                        | Often managed by hosting providers or domain registrars.                                                                                |
| `DNS Record Types`          | Different types of information stored in DNS.                                    | A, AAAA, CNAME, MX, NS, TXT, etc.                                                                                                       |
Now that we've covered the basics of `DNS`, we can examine its core components — the `record types`. These records store various kinds of data linked to `domain names`, with each `DNS record` serving a specific function in name resolution and domain management.

| Record Type | Full Name                 | Description                                                                                                                                 | Zone File Example                                                                              |
| ----------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `A`         | Address Record            | Maps a hostname to its IPv4 address.                                                                                                        | `www.example.com.` IN A `192.0.2.1`                                                            |
| `AAAA`      | IPv6 Address Record       | Maps a hostname to its IPv6 address.                                                                                                        | `www.example.com.` IN AAAA `2001:db8:85a3::8a2e:370:7334`                                      |
| `CNAME`     | Canonical Name Record     | Creates an alias for a hostname, pointing it to another hostname.                                                                           | `blog.example.com.` IN CNAME `webserver.example.net.`                                          |
| `MX`        | Mail Exchange Record      | Specifies the mail server(s) responsible for handling email for the domain.                                                                 | `example.com.` IN MX 10 `mail.example.com.`                                                    |
| `NS`        | Name Server Record        | Delegates a DNS zone to a specific authoritative name server.                                                                               | `example.com.` IN NS `ns1.example.com.`                                                        |
| `TXT`       | Text Record               | Stores arbitrary text information, often used for domain verification or security policies.                                                 | `example.com.` IN TXT `"v=spf1 mx -all"` (SPF record)                                          |
| `SOA`       | Start of Authority Record | Specifies administrative information about a DNS zone, including the primary name server, responsible person's email, and other parameters. | `example.com.` IN SOA `ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400` |
| `SRV`       | Service Record            | Defines the hostname and port number for specific services.                                                                                 | `_sip._udp.example.com.` IN SRV 10 5 5060 `sipserver.example.com.`                             |
| `PTR`       | Pointer Record            | Used for reverse DNS lookups, mapping an IP address to a hostname.                                                                          | `1.2.0.192.in-addr.arpa.` IN PTR `www.example.com.`                                            |
The `IN` in `DNS records` stands for `Internet` and represents the `class field` that defines the protocol family. Most domains use `IN` because it corresponds to the `Internet protocol suite (IP)`. Other classes like `CH` for `Chaosnet` and `HS` for `Hesiod` exist but are rarely seen in modern `DNS configurations`.

---

# Why DNS Matters for Web Recon

`DNS` is not merely a technical protocol for translating `domain names`; it's a critical component of a target's `infrastructure` that can be leveraged to uncover vulnerabilities and gain access during a `penetration test`.

- `Uncovering Assets: `DNS records` can reveal a wealth of information, including `subdomains`, `mail servers`, and `name server records`. For instance, a `CNAME` record pointing to an outdated server (`dev.example.com` `CNAME` `oldserver.example.net`) could lead to a vulnerable system.

- `Mapping the Network Infrastructure`: You can create a comprehensive map of the target's network by analysing `DNS data`. For example, identifying the `name servers` (`NS records`) for a domain can reveal the `hosting provider` used, while an `A record` for `loadbalancer.example.com` can pinpoint a `load balancer`. This helps you understand how different systems are connected, identify traffic flow, and pinpoint potential choke points or weaknesses that could be exploited during a `penetration test`.

- `Monitoring for Changes`: Continuously monitoring `DNS records` can reveal changes in the target's `infrastructure` over time. For example, the sudden appearance of a new `subdomain` (`vpn.example.com`) might indicate a new entry point into the network, while a `TXT record` containing a value like _1password=... strongly suggests the organization is using 1Password, which could be leveraged for `social engineering` attacks or targeted phishing campaigns.


