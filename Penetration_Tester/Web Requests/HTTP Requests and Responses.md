**HTTP communications** consist of an **HTTP request** and an **HTTP response**.

An **HTTP request** is made by the **client** (for example, `cURL` or a browser) and processed by the **server** (for example, a web server). The request includes details such as the **resource** (`URL`, `path`, `parameters`), **request data**, **headers**, and other options.

Once the **server** receives the request, it processes it and sends back an **HTTP response**. The response contains a **status code** and may also include the **resource data**, provided the requester has access.

# HTTP Request

![[Pasted image 20250920021637.png]]

| **Field** |     **Example**     |                                                    **Description**                                                    |
| :-------: | :-----------------: | :-------------------------------------------------------------------------------------------------------------------: |
| `Method`  |        `GET`        |                        The HTTP method or verb, which specifies the type of action to perform.                        |
|  `Path`   | `/users/login.html` | The path to the resource being accessed. This field can also be suffixed with a query string (e.g. `?username=user`). |
| `Version` |     `HTTP/1.1`      |                             The third and final field is used to denote the HTTP version.                             |

# HTTP Response

![[Pasted image 20250920101128.png]]

The first line of an HTTP response contains two fields separated by spaces. The first being the `HTTP version` (e.g. `HTTP/1.1`), and the second denotes the `HTTP response code` (e.g. `200 OK`).

Response codes are used to determine the request's status, as will be discussed in a later section. After the first line, the response lists its headers, similar to an HTTP request. Both request and response headers are discussed in the next section.