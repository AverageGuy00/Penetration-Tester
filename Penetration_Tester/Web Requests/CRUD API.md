CRUD APIs are commonly implemented over **REST** or **HTTP** protocols, where each operation maps to an HTTP method. They are the foundation for interacting with databases or resources in most modern web services.

| Operation | HTTP Method |                    Description                     |
| :-------: | :---------: | :------------------------------------------------: |
| `Create`  |   `POST`    |   Adds the specified data to the database table    |
|  `Read`   |    `GET`    | Reads the specified entity from the database table |
| `Update`  |    `PUT`    |  Updates the data of the specified database table  |
| `Delete`  |  `DELETE`   | Removes the specified row from the database table  |
neve
# Read 

The first thing we will do when interacting with an API is reading data. As mentioned earlier, we can simply specify the table name after the API (e.g. `/city`) and then specify our search term (e.g. `/london`), as follows:

```shell-session
OathBornX@htb[/htb]$ curl http://<SERVER_IP>:<PORT>/api.php/city/london

[{"city_name":"London","country_name":"(UK)"}]
```

We see that the result is sent as a JSON string. To have it properly formatted in JSON format, we can pipe the output to the `jq` utility, which will format it properly. We will also silent any unneeded cURL output with `-s`, as follows:

```shell-session
OathBornX@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq

[
  {
    "city_name": "London",
    "country_name": "(UK)"
  }
]
```

---

# Create

To add a new entry, we can use an HTTP POST request, which is quite similar to what we have performed in the previous section. We can simply POST our JSON data, and it will be added to the table. As this API is using JSON data, we will also set the `Content-Type` header to JSON, as follows:

```bash
OathBornX@htb[/htb]$ curl -X POST http://<SERVER_IP>:<PORT>/api.php/city/ -d '{"city_name":"HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```

Now, we can read the content of the city we added (`HTB_City`), to see if it was successfully added:

```bash
OathBornX@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/HTB_City | jq

[
  {
    "city_name": "HTB_City",
    "country_name": "HTB"
  }
```

As we can see, a new city was created, which did not exist before.

---

# Update

`PUT` is used to update API entries and modify their details.

Using `PUT` is quite similar to `POST` in this case, with the only difference being that we have to specify the name of the entity we want to edit in the URL, otherwise the API will not know which entity to edit. So, all we have to do is specify the `city` name in the URL, change the request method to `PUT`, and provide the JSON data like we did with POST, as follows:

```bash
OathBornX@htb[/htb]$ curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json
```

We see in the example above that we first specified `/city/london` as our city, and passed a JSON string that contained `"city_name":"New_HTB_City"` in the request data. So, the london city should no longer exist, and a new city with the name `New_HTB_City` should exist. Let's try reading both to confirm:

```bash
OathBornX@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq

[
  {
    "city_name": "New_HTB_City",
    "country_name": "HTB"
  }
]
```

---

# Delete

Finally, let's try to delete a city, which is as easy as reading a city. We simply specify the city name for the API and use the HTTP `DELETE` method, and it would delete the entry, as follows:

```bash
OathBornX@htb[/htb]$ curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City
```

