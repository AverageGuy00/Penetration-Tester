HTTP supports multiple methods for accessing a resource. In the HTTP protocol, several request methods allow the browser to send information, forms, or files to the server. These methods are used, among other things, to tell the server how to process the request we send and how to reply.

# Request Methods

| **Method** |                                                                                                                                                           **Description**                                                                                                                                                            |
| :--------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|   `GET`    |                                                                                                    Requests a specific resource. Additional data can be passed to the server via query strings in the URL (e.g. `?param=value`).                                                                                                     |
|   `POST`   | Sends data to the server. It can handle multiple types of input, such as text, PDFs, and other forms of binary data. This data is appended in the request body present after the headers. The POST method is commonly used when sending information (e.g. forms/logins) or uploading data to a website, such as images or documents. |
|   `HEAD`   |                                                                Requests the headers that would be returned if a GET request was made to the server. It doesn't return the request body and is usually made to check the response length before downloading resources.                                                                |
|   `PUT`    |                                                                                                     Creates new resources on the server. Allowing this method without proper controls can lead to uploading malicious resources.                                                                                                     |
|  `DELETE`  |                                                                                      Deletes an existing resource on the webserver. If not properly secured, can lead to Denial of Service (DoS) by deleting critical files on the web server.                                                                                       |
| `OPTIONS`  |                                                                                                                              Returns information about the server, such as the methods accepted by it.                                                                                                                               |
|  `PATCH`   |                                                                                                                               Applies partial modifications to the resource at the specified location.                                                                                                                               |
For a full list of HTTP methods, you can visit this [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods).

# Status Codes

HTTP status codes are used to tell the client the status of their request. An HTTP server can return five classes of status codes:

| **Class** |                                                         **Description**                                                          |
| :-------: | :------------------------------------------------------------------------------------------------------------------------------: |
|   `1xx`   |                             Provides information and does not affect the processing of the request.                              |
|   `2xx`   |                                                Returned when a request succeeds.                                                 |
|   `3xx`   |                                          Returned when the server redirects the client.                                          |
|   `4xx`   | Signifies improper requests `from the client`. For example, requesting a resource that doesn't exist or requesting a bad format. |
|   `5xx`   |                                Returned when there is some problem `with the HTTP server` itself.                                |

The following are some of the commonly seen examples from each of the above HTTP status code classes:

|          **Code**           |                                                                      **Description**                                                                      |
| :-------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------: |
|          `200 OK`           |                             Returned on a successful request, and the response body usually contains the requested resource.                              |
|         `302 Found`         |                    Redirects the client to another URL. For example, redirecting the user to their dashboard after a successful login.                    |
|      `400 Bad Request`      |                                Returned on encountering malformed requests such as requests with missing line terminators.                                |
|       `403 Forbidden`       | Signifies that the client doesn't have appropriate access to the resource. It can also be returned when the server detects malicious input from the user. |
|       `404 Not Found`       |                                      Returned when the client requests a resource that doesn't exist on the server.                                       |
| `500 Internal Server Error` |                                                   Returned when the server cannot process the request.                                                    |
For a full list of standard HTTP status codes, you can visit this [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status). Apart from the standard HTTP codes, various servers and providers such as [Cloudflare](https://support.cloudflare.com/hc/en-us/articles/115003014432-HTTP-Status-Codes) or [AWS](https://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/APIError.html) implement their own codes.

---

# GET

Whenever we visit any URL, our browsers default to a GET request to obtain the remote resources hosted at that URL. Once the browser receives the initial page it is requesting; it may send other requests using various HTTP methods. This can be observed through the Network tab in the browser devtools, as seen in the previous section.

---

Accessing the page with cURL, and we'll add -i to view the response headers:


```bash
OathBornX@htb[/htb]$ curl -i http://<SERVER_IP>:<PORT>/
HTTP/1.1 401 Authorization Required
Date: Mon, 21 Feb 2022 13:11:46 GMT
Server: Apache/2.4.41 (Ubuntu)
Cache-Control: no-cache, must-revalidate, max-age=0
WWW-Authenticate: Basic realm="Access denied"
Content-Length: 13
Content-Type: text/html; charset=UTF-8

Access denied
```

As we can see, we get `Access denied` in the response body, and we also get `Basic realm="Access denied"` in the `WWW-Authenticate` header, which confirms that this page indeed uses `basic HTTP auth`, as discussed in the Headers section. To provide the credentials through cURL, we can use the `-u` flag, as follows:

```bash
OathBornX@htb[/htb]$ curl -u admin:admin http://<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

# HTTP Authorization header

If we add the `-v` flag to either of our earlier cURL commands:

```bash
OathBornX@htb[/htb]$ curl -v http://admin:admin@<SERVER_IP>:<PORT>/

*   Trying <SERVER_IP>:<PORT>...
* Connected to <SERVER_IP> (<SERVER_IP>) port PORT (#0)
* Server auth using Basic with user 'admin'
> GET / HTTP/1.1
> Host: <SERVER_IP>
> Authorization: Basic YWRtaW46YWRtaW4=
> User-Agent: curl/7.77.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Mon, 21 Feb 2022 13:19:57 GMT
< Server: Apache/2.4.41 (Ubuntu)
< Cache-Control: no-store, no-cache, must-revalidate
< Expires: Thu, 19 Nov 1981 08:52:00 GMT
< Pragma: no-cache
< Vary: Accept-Encoding
< Content-Length: 1453
< Content-Type: text/html; charset=UTF-8
< 

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

As we are using `basic HTTP auth`, we see that our HTTP request sets the `Authorization` header to `Basic YWRtaW46YWRtaW4=`, which is the base64 encoded value of `admin:admin`. If we were using a modern method of authentication (e.g. `JWT`), the `Authorization` would be of type `Bearer` and would contain a longer encrypted token.

Let's try to manually set the `Authorization`, without supplying the credentials, to see if it does allow us access to the page. We can set the header with the `-H` flag, and will use the same value from the above HTTP request. We can add the `-H` flag multiple times to specify multiple headers:

```bash
OathBornX@htb[/htb]$ curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/

<!DOCTYPE html
<html lang="en">

<head>
...SNIP...
```

When we click on the request, it gets sent to `search.php` with the GET parameter `search=le` used in the URL. This helps us understand that the search function requests another page for the results.

```bash
OathBornX@htb[/htb]$ curl 'http://<SERVER_IP>:<PORT>/search.php?search=le' -H 'Authorization: Basic YWRtaW46YWRtaW4='

Leeds (UK)
Leicester (UK)
```

---

# POST

When web applications need to transfer files or move the user parameters from the URL, they utilize `POST` requests. Unlike HTTP `GET`, which places user parameters within the URL, HTTP `POST` places user parameters within the HTTP Request body. This has three main benefits:

- `Lack of Logging`: As POST requests may transfer large files (e.g. file upload), it would not be efficient for the server to log all uploaded files as part of the requested URL, as would be the case with a file uploaded through a GET request.
- `Less Encoding Requirements`: URLs are designed to be shared, which means they need to conform to characters that can be converted to letters. The POST request places data in the body which can accept binary data. The only characters that need to be encoded are those that are used to separate parameters.
- `More data can be sent`: The maximum URL Length varies between browsers (Chrome/Firefox/IE), web servers (IIS, Apache, nginx), Content Delivery Networks (Fastly, Cloudfront, Cloudflare), and even URL Shorteners (bit.ly, amzn.to). Generally speaking, a URL's lengths should be kept to below 2,000 characters, and so they cannot handle a lot of data.

---

# Login Forms

If we try to login with `admin`:`admin`, we get in and see a similar search function to the one we saw earlier in the GET section:

![Search icon with instruction: 'Type a city name and hit Enter'.](https://academy.hackthebox.com/storage/modules/35/web_requests_login_search.jpg)

If we clear the Network tab in our browser devtools and try to log in again, we will see many requests being sent. We can filter the requests by our server IP, so it would only show requests going to the web application's web server (i.e. filter out external requests), and we will notice the following POST request being sent:

![[Pasted image 20250920115411.png]]

We can click on the request, click on the `Request` tab (which shows the request body), and then click on the `Raw` button to show the raw request data. We see the following data is being sent as the POST request data:

```bash
username=admin&password=admin
```

We will use the `-X POST` flag to send a `POST` request. Then, to add our POST data, we can use the `-d` flag and add the above data after it, as follows:

```bash
OathBornX@htb[/htb]$ curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/

...SNIP...
        <em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

# Authenticated Cookies

If we were successfully authenticated, we should have received a cookie so our browsers can persist our authentication, and we don't need to login every time we visit the page. We can use the `-v` or `-i` flags to view the response, which should contain the `Set-Cookie` header with our authenticated cookie:

```bash
OathBornX@htb[/htb]$ curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/ -i

HTTP/1.1 200 OK
Date: 
Server: Apache/2.4.41 (Ubuntu)
Set-Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1; path=/

...SNIP...
        <em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

With our authenticated cookie, we should now be able to interact with the web application without needing to provide our credentials every time. To test this, we can set the above cookie with the `-b` flag in cURL, as follows:

```bash
OathBornX@htb[/htb]$ curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/

...SNIP...
        <em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

As we can see, we were indeed authenticated and got to the search function. It is also possible to specify the cookie as a header, as follows:

```bash
curl -H 'Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

As we can see, the search form sends a POST request to `search.php`, with the following data:

```json
{"search":"london"}
```

The POST data appear to be in JSON format, so our request must have specified the `Content-Type` header to be `application/json`. We can confirm this by right-clicking on the request, and selecting `Copy>Copy Request Headers`:

```bash
POST /search.php HTTP/1.1
Host: server_ip
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:97.0) Gecko/20100101 Firefox/97.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://server_ip/index.php
Content-Type: application/json
Origin: http://server_ip
Content-Length: 19
DNT: 1
Connection: keep-alive
Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1
```

Indeed, we do have `Content-Type: application/json`. Let's try to replicate this request as we did earlier, but include both the cookie and content-type headers, and send our request to `search.php`:

```bash
OathBornX@htb[/htb]$ curl -X POST -d '{"search":"london"}' -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php
["London (UK)"]
```
