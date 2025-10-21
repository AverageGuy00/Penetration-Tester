At the core of `virtual hosting` is the ability of `web servers` to distinguish between multiple `websites` or `applications` sharing the same `IP address`. This is achieved through the `HTTP Host header`, included in every `HTTP request` from a browser.

The distinction between `VHosts` and `subdomains` lies in how they relate to `DNS` and the `web server` configuration.

- `Subdomains`: Extensions of a main `domain` (e.g., `blog.example.com` under `example.com`). Each `subdomain` typically has its own `DNS record` pointing to the same or a different `IP address`. Commonly used to separate or organise services and sections of a site.
    
- `Virtual Hosts (VHosts)`: `Web server` configurations that allow hosting multiple `websites` or `applications` on a single server. A `VHost` can correspond to a top-level `domain` (e.g., `example.com`) or a `subdomain` (e.g., `dev.example.com`). Each `virtual host` has its own configuration, defining how the `server` responds to specific `HTTP Host headers`.

`hosts file` lets you map a `domain name` to an `IP address` locally, bypassing `DNS` so you can reach a `virtual host` that has no public `DNS record`.

Many `subdomains` are internal-only and won’t appear in public `DNS records`; those hosts are reachable only from inside the network or by using a local `hosts file` entry that points the name to the target `IP address`.

`VHost fuzzing` tests many candidate `Host` header values against a known `IP address` to discover public and non-public `virtual hosts`. It relies on sending HTTP requests with varied `Host header` values (or using the `hosts file` to resolve names locally) and observing distinct responses that indicate different `VHosts` or applications.

Virtual hosts can also be configured to use different domains, not just subdomains. For example:

```html
# Example of name-based virtual host configuration in Apache
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

Here, `example1.com`, `example2.org`, and `another-example.net` are distinct domains hosted on the same server. The web server uses the `Host` header to serve the appropriate content based on the requested domain name.

### Server VHost Lookup

The following illustrates the process of how a web server determines the correct content to serve based on the `Host` header:

![[Pasted image 20251021165658.png]]

- `Browser` sends an `HTTP request` to the target `IP address` derived from DNS resolution.
    
- The `Host header` in that request contains the domain name (the label the `web server` uses to pick a site).
    
- The `web server` inspects the `Host header` and matches it to a configured `virtual host` entry.
    
- The matched `virtual host` points to a `document root`; the `web server` returns files from that location as the HTTP response.

---

# Types of Virtual Hosting

- `Name-Based Virtual Hosting`: Uses the `HTTP Host header` to differentiate websites sharing the same `IP address`. It’s the most common and flexible method, requiring no extra IPs. Cost-efficient and supported by most `web servers`. Limitation: not ideal for older `SSL/TLS` setups because encryption starts before the `Host header` is read.
    
- `IP-Based Virtual Hosting`: Each `website` gets its own `IP address`. The `web server` selects which site to serve based on the `IP address` requested. Doesn’t depend on the `Host header` and works with all protocols, offering better isolation. Downside: needs multiple IPs, making it costly and less scalable.
    
- `Port-Based Virtual Hosting`: Multiple `websites` share the same `IP address` but use different `ports` (like `port 80` for one and `port 8080` for another). Useful when `IP addresses` are limited. Drawback: not user-friendly, as users must include the port number in the `URL`.
---

# Virtual Host Discovery Tools

Several tools are available to aid in the discovery of virtual hosts:

|Tool|Description|Features|
|---|---|---|
|[gobuster](https://github.com/OJ/gobuster)|A multi-purpose tool often used for directory/file brute-forcing, but also effective for virtual host discovery.|Fast, supports multiple HTTP methods, can use custom wordlists.|
|[Feroxbuster](https://github.com/epi052/feroxbuster)|Similar to Gobuster, but with a Rust-based implementation, known for its speed and flexibility.|Supports recursion, wildcard discovery, and various filters.|
|[ffuf](https://github.com/ffuf/ffuf)|Another fast web fuzzer that can be used for virtual host discovery by fuzzing the `Host` header.|Customizable wordlist input and filtering options.|

