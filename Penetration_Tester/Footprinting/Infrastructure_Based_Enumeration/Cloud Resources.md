The use of `cloud infrastructure` like `AWS`, `GCP`, and `Azure` has become fundamental for modern organizations. These platforms provide scalable, centralized environments enabling `remote management`, `collaboration`, and `distributed access` across global teams.

However, centralized provider security does not extend to customer-side configurations. Misconfigured `IAM policies`, `public storage endpoints`, or weakly secured `API gateways` frequently expose assets. Common attack surfaces include `AWS S3 buckets`, `Azure Blob Storage`, and `GCP Cloud Storage`. When left `unauthenticated` or `publicly readable`, these can leak internal data, credentials, or source code.

The critical takeaway is that `cloud misconfiguration` remains a dominant risk factor — not due to flaws in the cloud provider itself, but from insecure administrative setups and neglected access control within the tenant environment.

```bash
$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```

Companies often expose cloud storage entries in their DNS to simplify administrative access. During `IP` lookups you may discover hosts like `s3-website-us-west-2.amazonaws.com`, which indicate an `AWS S3` static website or bucket-backed endpoint.

- When a cloud endpoint appears in DNS, treat it as a high-priority lead for data exposure and asset ownership mapping. Record the resolved `IP`, associated `A` records, and the hosting `ASN`.

- Common storage indicators: `s3-website-*`, `*.blob.core.windows.net`, `storage.googleapis.com`, `*.digitaloceanspaces.com`.

- Discovery techniques: - Use passive correlation: `WHOIS`, passive `DNS`, certificate transparency (`crt.sh`), and `ASN` lookups to map provider ownership. - Use search engines and targeted queries (`Google Dorks`) to find content and indexed public buckets or blobs. - Common Google Dorks: `inurl:` and `intext:` combined with the company name or storage hostname to locate exposed files or directories.

- Operational caution: Do not attempt write or destructive actions against third-party-hosted buckets without explicit authorization. Document findings and notify the client for remediation or scoped testing.

Example indicators to search for:

- `inurl:s3-website-us-west-2.amazonaws.com "companyname"`
- `intext:"companyname" site:storage.googleapis.com` 

Record any discovered links, file paths, and accessible objects. Correlate them with internal asset lists and escalate to privileged layers only when in-scope and authorized.

#### Google Search for AWS

![[{6F47C494-169A-4735-9120-5347BE12B5D5}.png]]

#### Google Search for Azure

![[{A95CAF1A-BF97-46AA-8A08-D5DB498D6AC2}.png]]

Here we can already see that the links presented by `Google` contain `PDF` files. When searching for a company you care about you’ll also surface `text documents`, `presentations`, `source code`, and other artifacts.

Those files are often referenced directly in the page `source` (`images`, `JavaScript`, `CSS`) or hosted on external `cloud storage`/CDN endpoints. Offloading assets reduces load on the primary `web server` but increases exposure risk when access controls are misconfigured.

#### Target Website - Source Code

![[{89A4D22B-63C7-43F8-B0D0-F9D688738937}.png]]

`Third-party providers` such as [domain.glass](https://domain.glass) can also tell us a lot about the `company's infrastructure`. As a positive side effect, we can also see that `Cloudflare's` security assessment status has been classified as "Safe". This means we have already found a `security measure` that can be noted for the second layer `(gateway)`.

#### Domain.Glass Results

![[{6FF2F8EB-E386-4B1B-96C1-11EA7106A759}.png]]

`GrayHatWarfare` is a searchable index of public `cloud storage` and CDN endpoints. It lets you enumerate exposed artifacts by provider and filetype without actively interacting with the target.
#### GrayHatWarfare Results

![[{B2488FE3-18EB-4410-8CC8-0DFA5510B5DB}.png]]

#### Private and Public SSH Keys Leaked

![[{AFC51AA7-DC7A-4517-88E6-EC95DD6F7937}.png]]

Sometimes when employees are overworked or under high pressure, mistakes can be catastrophic for an organization. Human error can lead to the accidental exposure of `SSH private keys`, archived backups, or configuration files. A leaked `SSH private key` allows an attacker to authenticate to one or more `machines` without a password

