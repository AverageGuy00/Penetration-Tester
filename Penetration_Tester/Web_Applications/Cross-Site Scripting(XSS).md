**Cross-Site Scripting (XSS)** is a web security vulnerability that allows attackers to inject malicious scripts into webpages viewed by other users, enabling actions like stealing cookies, session tokens, or manipulating site content.

|      Type       |                                                                Description                                                                |
| :-------------: | :---------------------------------------------------------------------------------------------------------------------------------------: |
| `Reflected XSS` |                 Occurs when user input is displayed on the page after processing (e.g., search result or error message).                  |
|  `Stored XSS`   |          Occurs when user input is stored in the back end database and then displayed upon retrieval (e.g., posts or comments).           |
|    `DOM XSS`    | Occurs when user input is directly shown in the browser and is written to an `HTML` DOM object (e.g., vulnerable username or page title). |
