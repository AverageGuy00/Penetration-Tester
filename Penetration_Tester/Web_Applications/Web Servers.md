A [web server](https://en.wikipedia.org/wiki/Web_server) is an application that runs on the back end server, which handles all of the HTTP traffic from the client-side browser, routes it to the requested pages, and finally responds to the client-side browser. Web servers usually run on TCP [ports](https://en.wikipedia.org/wiki/Port_\(computer_networking\)) `80` or `443`, and are responsible for connecting end-users to various parts of the web application, in addition to handling their various responses.

The following are some of the most common [HTTP response codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status):

|            Code             |                                                     Description                                                     |
| :-------------------------: | :-----------------------------------------------------------------------------------------------------------------: |
|  **Successful responses**   |                                                                                                                     |
|          `200 OK`           |                                              The request has succeeded                                              |
|                             |                                                                                                                     |
|  **Redirection messages**   |                                                                                                                     |
|   `301 Moved Permanently`   |                           The URL of the requested resource has been changed permanently                            |
|         `302 Found`         |                           The URL of the requested resource has been changed temporarily                            |
|                             |                                                                                                                     |
| **Client error responses**  |                                                                                                                     |
|      `400 Bad Request`      |                          The server could not understand the request due to invalid syntax                          |
|     `401 Unauthorized`      |                                       Unauthenticated attempt to access page                                        |
|       `403 Forbidden`       |                                The client does not have access rights to the content                                |
|       `404 Not Found`       |                                   The server can not find the requested resource                                    |
|  `405 Method Not Allowed`   |                 The request method is known by the server but has been disabled and cannot be used                  |
|    `408 Request Timeout`    |    This response is sent on an idle connection by some servers, even without any previous request by the client     |
|                             |                                                                                                                     |
| **Server error responses**  |                                                                                                                     |
| `500 Internal Server Error` |                        The server has encountered a situation it doesn't know how to handle                         |
|      `502 Bad Gateway`      | The server, while working as a gateway to get a response needed to handle the request, received an invalid response |
|    `504 Gateway Timeout`    |                         The server is acting as a gateway and cannot get a response in time                         |

---

# Apache (httpd)

Apache is the most common web server, powering over 40% of websites. It often comes preinstalled on Linux and is also available for Windows/macOS. Commonly paired with PHP but supports other languages (Python, Perl, .NET, Bash via CGI). Functionality extended with modules like `mod_php`. Open-source, well-maintained, patched frequently, and heavily documented. Popular among smaller companies and some large ones (Apple, Adobe, Baidu).

---

# NGINX

Second most common web server, hosting about 30% of websites. Uses an asynchronous architecture, making it efficient for high concurrency with low resource use. Popular for high-traffic sites — around 60% of the top 100k websites use it. Open-source, secure, reliable. Used by companies like Google, Facebook, Twitter, Cisco, Intel, Netflix, HackTheBox.

---

# IIS (Internet Information Services)

Third most common web server (~15%). Developed by Microsoft for Windows Server. Optimized for .NET applications but can also host PHP and other services like FTP. Integrates tightly with Active Directory, supporting Windows Authentication for single sign-on. Not the most popular, but widely used in enterprises relying on Microsoft infrastructure. Used by Microsoft, Office365, Skype, Stack Overflow, Dell.