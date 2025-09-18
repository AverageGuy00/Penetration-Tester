Web frameworks provide tools and structure to simplify building modern web applications. They handle common functionality (routing, templates, database access, authentication) so developers don’t have to build everything from scratch. Frameworks make development faster, more secure, and more maintainable, especially as applications grow in complexity.

As most web applications share common functionality -such as user registration-, web development frameworks make it easy to quickly implement this functionality and link them to the front end components, making a fully functional web application. Some of the most common web development frameworks include:

- [Laravel](https://laravel.com/) (`PHP`): usually used by startups and smaller companies, as it is powerful yet easy to develop for.
- [Express](https://expressjs.com/) (`Node.JS`): used by `PayPal`, `Yahoo`, `Uber`, `IBM`, and `MySpace`.
- [Django](https://www.djangoproject.com/) (`Python`): used by `Google`, `YouTube`, `Instagram`, `Mozilla`, and `Pinterest`.
- [Rails](https://rubyonrails.org) (`Ruby`): used by `GitHub`, `Hulu`, `Twitch`, `Airbnb`, and even `Twitter` in the past.

```
It must be noted that popular websites usually utilize a variety of frameworks and web servers, rather than just one.
```

---

# APIs

An important aspect of **back end web application development** is the use of **Web APIs** and **HTTP request parameters**. These allow the **front end** and **back end** to exchange data and perform different functions within the application.

The **front end component** interacts with the **back end** by sending requests through APIs, asking for specific tasks with defined input. The **back end** processes these requests, executes the required operations, and sends a response back. The **front end** then renders this response on the **client side**, providing the final output to the end user.

---

# Query Parameters

**GET vs. POST

- **<u>GET</u>** is typically used when you’re **retrieving data**.  
  Example: `/search.php?item=apples` — the query string is visible in the URL.

- Used for simple and lightweight queries
- Parameters are passed in the URL 
- Easy to bookmark, cache, and share 
- Limited by URL length restrictions
- Example: `/search.php?item=apples&color=red`


- <u>POST</u> is usually used when you’re **sending data that changes state** or when the input might be sensitive.  
  Example: submitting login credentials, posting a comment, uploading a file. The data is in the request body, not the URL.

- Used for complex queries or larger data payloads
- Parameters are passed in the request body, not visible in the URL
- Better for sending structured data (like JSON) 
- No URL length restrictions

```pgsql
POST /search.php HTTP/1.1
Content-Type: application/json

{
  "item": "apples",
  "color": "red",
  "price": { "min": 10, "max": 100 },
  "in_stock": true,
  "sort": "latest",
  "categories": ["fruits", "organic", "seasonal"]
}
```


**Key Difference**

- GET → simple, short, “read-only” queries
- POST → complex, large, or structured data where parameters should not be exposed in the URL

--- 

# Web APIs

A **Web API** is a type of API exposed over the **HTTP/HTTPS protocol** and is usually managed by a **web server**. It allows **front end applications, external systems, or other services** to interact with the **back end functionality** of a web application.

Web APIs are not limited to web apps — they exist across software — but in the web context, they act as the “bridge” for sending **requests** and receiving **responses** in standard formats.

**Examples**

- A **weather API** can return weather data for a given city in JSON.
- **Twitter’s API** provides Tweets in XML/JSON and allows posting (if authenticated).

---

# SOAP (Simple Object Access Protocol)

SOAP is a **web API standard** that transfers data using **XML** format. Both the **request** and **response** are encoded in XML. It is designed for **structured and complex data exchange** between front end and back end components.

**How it Works**

- Request sent in XML over HTTP (sometimes SMTP or other protocols).
- Response returned in XML.
- Front end must **parse XML** to extract data.

```xml
<?xml version="1.0"?>
<soap:Envelope
 xmlns:soap="http://www.example.com/soap/soap/"
 soap:encodingStyle="http://www.w3.org/soap/soap-encoding">
  <soap:Header>
  </soap:Header>
  <soap:Body>
    <soap:Fault>
    </soap:Fault>
  </soap:Body>
</soap:Envelope>
```


---

# REST

REST is a **web API standard** that transfers data through **URL paths** instead of XML envelopes. It is designed to be **lightweight, modular, and scalable**, making it easier for developers to build and maintain modern applications.

**How it Works**

- Uses standard **HTTP methods**: `GET`, `POST`, `PUT`, `DELETE`.
- Input is often passed in the **URL path** (e.g., `search/users/1`).
- Output is typically **JSON** (but can also be XML, form-encoded, or raw data).
- Designed to break web app functionality into **smaller APIs** for flexibility.

Example Request

```bash
GET /category/posts/
```

Example JSON Response

```json
{
  "post_id": 1,
  "title": "Web Security Basics",
  "author": "Haz",
  "date": "2025-09-19"
}
```

`REST` uses various HTTP methods to perform different actions on the web application:

- `GET` request to retrieve data
- `POST` request to create data (non-idempotent)
- `PUT` request to create or replace existing data (idempotent)
- `DELETE` request to remove data