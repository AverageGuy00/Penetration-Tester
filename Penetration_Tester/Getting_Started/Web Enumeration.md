# Banner Grabbing / Web Server Headers

`Web server headers` give a quick inventory of what's hosted: the `application framework`, supported `authentication options`, whether essential `security headers` (for example `HSTS` or `X-Frame-Options`) are present, and signals that a server is `misconfigured`. You can retrieve these headers from the command line using `cURL`; learning `cURL` options to show response headers, follow redirects, and change the `User-Agent` is a useful skill for your `penetration testing toolkit`.

```js
OathBornX@htb[/htb]$ curl -IL https://www.inlanefreight.com

HTTP/1.1 200 OK
Date: Fri, 18 Dec 2020 22:24:05 GMT
Server: Apache/2.4.29 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8
```

# Whatweb

You can extract server `versions`, supporting `frameworks`, and `applications` with the command-line tool `WhatWeb`. This fingerprinting reveals the underlying `technologies` and helps narrow searches for known `vulnerabilities` and `misconfigurations` — useful early reconnaissance for a `penetration test`.

```js
OathBornX@htb[/htb]$ whatweb 10.10.10.121

http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]
```

`Whatweb` is a handy tool and contains much functionality to automate web application enumeration across a network.

```js
OathBornX@htb[/htb]$ whatweb --no-errors 10.10.10.0/24

http://10.10.10.11 [200 OK] Country[RESERVED][ZZ], HTTPServer[nginx/1.14.1], IP[10.10.10.11], PoweredBy[Red,nginx], Title[Test Page for the Nginx HTTP Server on Red Hat Enterprise Linux], nginx[1.14.1]
http://10.10.10.100 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.100], Title[File Sharing Service]
http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]
http://10.10.10.247 [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[contact@cross-fit.htb], Frame, HTML5, HTTPServer[OpenBSD httpd], IP[10.10.10.247], JQuery[3.3.1], PHP[7.4.12], Script, Title[Fine Wines], X-Powered-By[PHP/7.4.12], X-UA-Compatible[ie=edge]
```

# Certificates

`SSL/TLS certificates` are another valuable source of information when `HTTPS` is used. Inspecting the `certificate` can reveal fields like the `subject` (including `company name`), `email address`, `issuer`, and `validity` dates, which help map infrastructure and spot misconfigurations (expired or self‑signed certs). Exposed `email address`es or organization details can be leveraged for targeted `phishing` or social‑engineering — only pursue those vectors when they fall inside the authorized `scope of assessment`.

# Robots.txt

Websites often have a `robots.txt` file that tells search engine crawlers (like `Googlebot`) which `resources` to allow or block from indexing. This file can reveal sensitive information, such as the location of `private files` or `admin pages`. In this example, the `robots.txt` file lists two `disallowed` paths that may be worth reviewing during an authorized `security assessment`.

![[Pasted image 20251002103609.png]]
