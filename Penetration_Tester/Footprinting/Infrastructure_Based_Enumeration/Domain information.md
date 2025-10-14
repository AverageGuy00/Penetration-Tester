# Internet Presence

## SSL Certificates

The first point of presence on the Internet may be the `SSL certificate` from the company’s main website. Often, such a `certificate` includes more than one `subdomain`, meaning the certificate covers multiple `domains` that are most likely still active.

Example:

- Certificate validity from May 18, 2021, to April 6, 2022
- DNS names: `inlanefreight.htb`, `www.inlanefreight.htb`, `support.inlanefreight.htb`

Another valuable source for identifying additional `subdomains` is `crt.sh` — a public interface for `Certificate Transparency` logs.

`Certificate Transparency` (RFC 6962) enables verification of all `digital certificates` issued for encrypted Internet connections. It ensures all certificates issued by a `Certificate Authority` are logged in an `audit-proof` system. This allows detection of `false` or `maliciously issued` certificates for a domain.

`SSL certificate` providers such as `Let's Encrypt` share these logs with web interfaces like `crt.sh`, which stores and exposes new entries for public access and investigation.

```bash
$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .

[
  {
    "issuer_ca_id": 23451835427,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 50815783237226155,
    "entry_timestamp": "2021-08-21T06:00:17.173",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe9017d6de5eda90"
  },
  {
    "issuer_ca_id": 6864563267,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 5081529377,
    "entry_timestamp": "2021-08-21T06:00:16.932",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe90104e271c98a90"
  }
```

```bash
$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u

account.ttn.inlanefreight.com
blog.inlanefreight.com
bots.inlanefreight.com
console.ttn.inlanefreight.com
ct.inlanefreight.com
data.ttn.inlanefreight.com
*.inlanefreight.com
inlanefreight.com
```

Next, we can identify the `hosts` directly accessible from the Internet and not hosted by `third-party providers`. This is because we are not allowed to test the hosts `without the permission` of `third-party providers`.
#### Company Hosted Servers

```bash
$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```

Once we see which hosts can be investigated further, we can generate a list of `IP addresses` with a minor adjustment to the `cut` command and run them through `Shodan`.

## Shodan

`Shodan` is a search engine for Internet-connected devices and systems, often used to find `IoT` devices and services that are permanently reachable from the Internet. It scans the Internet for open `TCP/IP` ports and indexes responses, allowing searches filtered by service, port, banner, and other criteria.

- `What it scans`: open `HTTP`/`HTTPS` ports and other server ports such as `FTP`, `SSH`, `SNMP`, `Telnet`, `RTSP`, `SIP`. - `What you can find`: surveillance cameras, servers, `smart home` devices, industrial controllers, traffic controllers, and various network components. - `Why it matters`: exposed services often reveal default credentials, outdated firmware, misconfigurations, or sensitive banners that leak software versions and device types. - `Usage constraints`: only query and investigate targets you are authorized to test. Avoid interacting with or attempting to exploit devices owned or operated by third parties without permission.

- `How to use responsibly`: document findings, notify asset owners through approved channels, and include `Shodan` results as one data source among many during `external` reconnaissance.

#### Shodan - IP List

```bash
$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
$ for i in $(cat ip-addresses.txt);do shodan host $i;done

10.129.24.93
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T09:02:11.370085
Number of open ports:    2

Ports:
     80/tcp nginx 
    443/tcp nginx 
	
10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx 
    443/tcp nginx 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
				
10.129.27.22
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T15:39:55.446281
Number of open ports:    8

Ports:
     25/tcp  
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
     53/tcp  
     53/udp  
     80/tcp Apache httpd 
     81/tcp Apache httpd 
    110/tcp  
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2
    111/tcp  
    443/tcp Apache httpd 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
                Fingerprint:   RFC3526/Oakley Group 14
    444/tcp  
		
10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx 
    443/tcp nginx 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
```

We remember the IP `10.129.127.22` (`matomo.inlanefreight.com`) for later active investigations we want to perform. Now, we can display all the available `DNS records` where we might find more hosts.

## DNS Records

```bash
$ dig any inlanefreight.com

; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52058
;; flags: qr rd ra; QUERY: 1, ANSWER: 17, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;inlanefreight.com.             IN      ANY

;; ANSWER SECTION:
inlanefreight.com.      300     IN      A       10.129.27.33
inlanefreight.com.      300     IN      A       10.129.95.250
inlanefreight.com.      3600    IN      MX      1 aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      10 aspmx2.googlemail.com.
inlanefreight.com.      3600    IN      MX      10 aspmx3.googlemail.com.
inlanefreight.com.      3600    IN      MX      5 alt1.aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      5 alt2.aspmx.l.google.com.
inlanefreight.com.      21600   IN      NS      ns.inwx.net.
inlanefreight.com.      21600   IN      NS      ns2.inwx.net.
inlanefreight.com.      21600   IN      NS      ns3.inwx.eu.
inlanefreight.com.      3600    IN      TXT     "MS=ms92346782372"
inlanefreight.com.      21600   IN      TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
inlanefreight.com.      300     IN      TXT     "google-site-verification=bow47-er9LdgoUeah"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=gZsCG-BINLopf4hr2"
inlanefreight.com.      3600    IN      TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
inlanefreight.com.      300     IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
inlanefreight.com.      21600   IN      SOA     ns.inwx.net. hostmaster.inwx.net. 2021072600 10800 3600 604800 3600

;; Query time: 332 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Mi Sep 01 18:27:22 CEST 2021
;; MSG SIZE  rcvd: 940
```

- `A records` map `domain` names to `IPv4 addresses` and show which `subdomain` points to which `IP address`.
- `MX records` specify which `mail server` manages emails for a `domain`. If it’s handled by `Google` or another `third-party provider`, it’s usually noted but excluded from testing.
- `NS records` list the `name servers` used to resolve the `FQDN` to `IP addresses`. They help identify the `hosting provider`.
- `TXT records` contain `verification keys` and `security settings` like `SPF`, `DMARC`, and `DKIM` that verify the source of outgoing emails and can reveal useful details.
---

What we can see so far are entries on the `DNS` server that look unremarkable at first (aside from extra `IP addresses`). Those records do not directly reveal the `third-party providers` behind them; you must correlate with `WHOIS`, passive `DNS`, `certificates`, and `ASN`/netblock data to uncover provider ownership. Treat the visible records as leads, not full context.

- `Atlassian`
- `Google Gmail`
- `LogMeIn`
- `Mailgun`
- `Outlook`
- `INWX` (`ID/Username` marker)
- `10.129.24.8` `10.129.27.2` `10.72.82.106`

`Atlassian` is a `software development` and `collaboration` platform commonly used for project management and issue tracking. Its presence indicates reliance on cloud-hosted tools like `Jira` or `Confluence`. Enumerating exposed instances or metadata can reveal internal structures, public projects, or credentials in misconfigured repositories.

`Google Gmail` signals the organization uses `Google Workspace` for `email management`. This can also imply linked `Google Drive` resources. Misconfigured sharing settings or open `GDrive` links could expose internal documents, credentials, or sensitive assets.

`LogMeIn` serves as a `remote access management` platform. It centralizes access control across multiple endpoints, making it a critical asset. If `administrator credentials` or `API tokens` are compromised, full access to connected systems can be achieved.

`Mailgun` provides `email APIs`, `SMTP relays`, and `webhooks`. Its presence implies potential exposure of `API endpoints`. These should be reviewed for `IDOR`, `SSRF`, and improper `POST` or `PUT` request handling, as they often lead to privilege escalation or data leakage.

`Outlook` indicates use of `Office 365` or `Microsoft Exchange Online` for `document management` and `communication`. It often ties to `OneDrive`, `Azure Blob Storage`, or `Azure File Storage` — the latter operating over the `SMB protocol`. Improperly secured shares can lead to direct data exfiltration or lateral movement.

`INWX` is a `domain registrar` and `hosting provider`. The presence of an `MS TXT record` usually indicates `domain verification` or `ownership confirmation`. These records may contain identifiers or patterns resembling `usernames` used for administrative access to the provider’s portal.


