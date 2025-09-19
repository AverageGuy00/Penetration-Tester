HTTP communication consists of a client and a server, where the client requests the server for a resource. The server processes the requests and returns the requested resource. The default port for HTTP communication is port 80, though this can be changed to any other port, depending on the web server configuration. The same requests are utilized when we use the internet to visit different websites. We enter a Fully Qualified Domain Name (FQDN) as a Uniform Resource Locator (URL) to reach the desired website

`http://admin:password@inlanefreight.com:80/dashboard.php?login=true#status`

Here is what each component stands for:

| **Component**  |     **Example**      |                                                                                    **Description**                                                                                    |
| :------------: | :------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|    `Scheme`    | `http://` `https://` |                                 This is used to identify the protocol being accessed by the client, and ends with a colon and a double slash (`://`)                                  |
|  `User Info`   |  `admin:password@`   |     This is an optional component that contains the credentials (separated by a colon `:`) used to authenticate to the host, and is separated from the host with an at sign (`@`)     |
|     `Host`     | `inlanefreight.com`  |                                                   The host signifies the resource location. This can be a hostname or an IP address                                                   |
|     `Port`     |        `:80`         |               The `Port` is separated from the `Host` by a colon (`:`). If no port is specified, `http` schemes default to port `80` and `https` default to port `443`                |
|     `Path`     |   `/dashboard.php`   |         This points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. `index.html`).         |
| `Query String` |    `?login=true`     | The query string starts with a question mark (`?`), and consists of a parameter (e.g. `login`) and a value (e.g. `true`). Multiple parameters can be separated by an ampersand (`&`). |
|  `Fragments`   |      `#status`       |                   Fragments are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page).                   |

# HTTP Flow 

![[Pasted image 20250920001403.png]]

The diagram above presents the anatomy of an HTTP request at a very high level. The first time a user enters the URL (`inlanefreight.com`) into the browser, it sends a request to a DNS (Domain Name System) server to resolve the domain and get its IP. The DNS server looks up the IP address for `inlanefreight.com` and returns it. All domain names need to be resolved this way, as a server can't communicate without an IP address.

---

# HTTPS Flow

![[Pasted image 20250920014557.png]]

