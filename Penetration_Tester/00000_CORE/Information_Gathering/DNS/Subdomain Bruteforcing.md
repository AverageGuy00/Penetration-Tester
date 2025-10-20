`Subdomain Brute-Force Enumeration` is a powerful `active` `subdomain discovery` technique that leverages pre-defined lists of potential `subdomain` names. Using carefully crafted `wordlists` significantly increases the efficiency and effectiveness of `subdomain discovery` efforts.

- `Wordlist Selection`: The process begins with selecting a `wordlist` containing potential `subdomain` names. These wordlists can be:
    
- `General-Purpose`: Contains common `subdomain` names (e.g., `dev`, `staging`, `blog`, `mail`, `admin`, `test`). Useful when target naming conventions are unknown.
    
- `Targeted`: Focused on specific industries, technologies, or naming patterns relevant to the target; more efficient and reduces `false positives`.
    
- `Custom`: Built from collected intelligence, keywords, or observed naming patterns.
    
- `Iteration and Querying`: A script or tool iterates the `wordlist`, appending each entry to the base domain to generate candidate `subdomains` (e.g., `dev.example.com`, `staging.example.com`).
    
- `DNS Lookup`: Perform a `DNS` query for each candidate to check resolution to an IP address, typically querying `A` or `AAAA` record types.
    
- `Filtering and Validation`: Add resolving entries to a validated list of `subdomains` and perform additional checks to confirm existence and functionality (for example, HTTP/S requests or service banner interrogation).

There are several `tools` available that excel at `brute-force` enumeration.

|Tool|Description|
|---|---|
|[dnsenum](https://github.com/fwaeytens/dnsenum)|Comprehensive DNS enumeration tool that supports dictionary and brute-force attacks for discovering subdomains.|
|[fierce](https://github.com/mschwager/fierce)|User-friendly tool for recursive subdomain discovery, featuring wildcard detection and an easy-to-use interface.|
|[dnsrecon](https://github.com/darkoperator/dnsrecon)|Versatile tool that combines multiple DNS reconnaissance techniques and offers customisable output formats.|
|[amass](https://github.com/owasp-amass/amass)|Actively maintained tool focused on subdomain discovery, known for its integration with other tools and extensive data sources.|
|[assetfinder](https://github.com/tomnomnom/assetfinder)|Simple yet effective tool for finding subdomains using various techniques, ideal for quick and lightweight scans.|
|[puredns](https://github.com/d3mondev/puredns)|Powerful and flexible DNS brute-forcing tool, capable of resolving and filtering results effectively.|

---

# DNSEnum

`dnsenum` is a versatile and widely-used `command-line tool` written in `Perl`. It is a comprehensive toolkit for `DNS reconnaissance`, providing various functionalities to gather information about a target domain's `DNS infrastructure` and potential `subdomains`.

- `DNS Record Enumeration`: `dnsenum` can retrieve various `DNS` records, including `A`, `AAAA`, `NS`, `MX`, and `TXT` records, providing a comprehensive overview of the target's `DNS configuration`.
    
- `Zone Transfer Attempts`: The tool automatically attempts `zone transfers` from discovered `name servers`; successful transfers can expose the zone file and reveal extensive DNS data.
    
- `Subdomain Brute-Forcing`: `dnsenum` supports brute-force enumeration of `subdomains` using a `wordlist`, systematically testing candidates to identify valid hosts.
    
- `Google Scraping`: The tool can perform `Google` scraping to discover additional `subdomains` or hosts that may not appear in DNS records.
    
- `Reverse Lookup`: `dnsenum` can perform `reverse DNS` lookups (PTR) to map IP addresses back to domain names, aiding in host discovery and correlation.
    
- `WHOIS Lookups`: The tool can query `WHOIS` to gather domain registration and ownership metadata useful for reconnaissance and attribution.

---

```bash
OathBornX@htb[/htb]$ dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 

dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----


Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]


done.
```



