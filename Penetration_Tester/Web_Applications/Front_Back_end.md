# Front End

The front end of a web application contains the user's components directly through their web browser (client-side). These components make up the source code of the web page we view when visiting a web application and usually include `HTML`, `CSS`, and `JavaScript`, which is then interpreted in real-time by our browsers.

# Back End

The back end of a web application drives all of the core web application functionalities, all of which is executed at the back end server, which processes everything required for the web application to run correctly. It is the part we may never see or directly interact with, but a website is just a collection of static web pages without a back end.

There are four main back end components for web applications:

|      **Component**       |                                                                                                       **Description**                                                                                                        |
| :----------------------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|    `Back end Servers`    |                                  The hardware and operating system that hosts all other components and are usually run on operating systems like `Linux`, `Windows`, or using `Containers`.                                  |
|      `Web Servers`       |                                                              Web servers handle HTTP requests and connections. Some examples are `Apache`, `NGINX`, and `IIS`.                                                               |
|       `Databases`        | Databases (`DBs`) store and retrieve the web application data. Some examples of relational databases are `MySQL`, `MSSQL`, `Oracle`, `PostgreSQL`, while examples of non-relational databases include `NoSQL` and `MongoDB`. |
| `Development Frameworks` |  Development Frameworks are used to develop the core Web Application. Some well-known frameworks include `Laravel` (`PHP`), `ASP.NET` (`C#`), `Spring` (`Java`), `Django` (`Python`), and `Express` (`NodeJS JavaScript`).   |

There are many popular combinations of "stacks" for back-end servers, which contain a specific set of back end components. Some common examples include:

|Combinations|Components|
|---|---|
|[LAMP](https://en.wikipedia.org/wiki/LAMP_\(software_bundle\))|`Linux`, `Apache`, `MySQL`, and `PHP`.|
|[WAMP](https://en.wikipedia.org/wiki/LAMP_\(software_bundle\)#WAMP)|`Windows`, `Apache`, `MySQL`, and `PHP`.|
|[WINS](https://en.wikipedia.org/wiki/Solution_stack)|`Windows`, `IIS`, `.NET`, and `SQL Server`|
|[MAMP](https://en.wikipedia.org/wiki/MAMP)|`macOS`, `Apache`, `MySQL`, and `PHP`.|
|[XAMPP](https://en.wikipedia.org/wiki/XAMPP)|Cross-Platform, `Apache`, `MySQL`, and `PHP/PERL`.|

Some of the main jobs performed by back end components include:

- Develop the main logic and services of the back end of the web application
- Develop the main code and functionalities of the web application
- Develop and maintain the back end database
- Develop and implement libraries to be used by the web application
- Implement technical/business needs for the web application
- Implement the main [APIs](https://en.wikipedia.org/wiki/API) for front end component communications
- Integrate remote servers and cloud services into the web application

The `top 20` most common mistakes web developers make that are essential for us as penetration testers are:

| **No.** |                    **Mistake**                     |
| :-----: | :------------------------------------------------: |
|  `1.`   |   Permitting Invalid Data to Enter the Database    |
|  `2.`   |         Focusing on the System as a Whole          |
|  `3.`   | Establishing Personally Developed Security Methods |
|  `4.`   |       Treating Security to be Your Last Step       |
|  `5.`   |       Developing Plain Text Password Storage       |
|  `6.`   |              Creating Weak Passwords               |
|  `7.`   |      Storing Unencrypted Data in the Database      |
|  `8.`   |      Depending Excessively on the Client Side      |
|  `9.`   |                Being Too Optimistic                |
|  `10.`  |     Permitting Variables via the URL Path Name     |
|  `11.`  |             Trusting third-party code              |
|  `12.`  |           Hard-coding backdoor accounts            |
|  `13.`  |             Unverified SQL injections              |
|  `14.`  |               Remote file inclusions               |
|  `15.`  |               Insecure data handling               |
|  `16.`  |          Failing to encrypt data properly          |
|  `17.`  |      Not using a secure cryptographic system       |
|  `18.`  |                  Ignoring layer 8                  |
|  `19.`  |                Review user actions                 |
|  `20.`  |     Web Application Firewall misconfigurations     |

These mistakes lead to the [OWASP Top 10](https://owasp.org/www-project-top-ten/) vulnerabilities for web applications, which we will discuss in other modules:

| **No.** | **Vulnerability**                          |
| ------- | ------------------------------------------ |
| `1.`    | Broken Access Control                      |
| `2.`    | Cryptographic Failures                     |
| `3.`    | Injection                                  |
| `4.`    | Insecure Design                            |
| `5.`    | Security Misconfiguration                  |
| `6.`    | Vulnerable and Outdated Components         |
| `7.`    | Identification and Authentication Failures |
| `8.`    | Software and Data Integrity Failures       |
| `9.`    | Security Logging and Monitoring Failures   |
| `10.`   | Server-Side Request Forgery (SSRF)         |
