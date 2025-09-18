**Cross-Site Scripting (XSS)** is a web security vulnerability that allows attackers to inject malicious scripts into webpages viewed by other users, enabling actions like stealing cookies, session tokens, or manipulating site content.

|      Type       |                                                                Description                                                                |
| :-------------: | :---------------------------------------------------------------------------------------------------------------------------------------: |
| `Reflected XSS` |                 Occurs when user input is displayed on the page after processing (e.g., search result or error message).                  |
|  `Stored XSS`   |          Occurs when user input is stored in the back end database and then displayed upon retrieval (e.g., posts or comments).           |
|    `DOM XSS`    | Occurs when user input is directly shown in the browser and is written to an `HTML` DOM object (e.g., vulnerable username or page title). |
In the example we saw for `HTML Injection`, there was no input sanitization whatsoever. Therefore, it may be possible for the same page to be vulnerable to `XSS` attacks. We can try to inject the following `DOM XSS` `JavaScript` code as a payload, which should show us the cookie value for the current user:


```javascript
#"><img src=/ onerror=alert(document.cookie)>
```

Once we input our payload and hit `ok`, we see that an alert window pops up with the cookie value in it.

This payload is accessing the `HTML` document tree and retrieving the `cookie` object's value. When the browser processes our input, it will be considered a new `DOM`, and our `JavaScript` will be executed, displaying the cookie value back to us in a popup.