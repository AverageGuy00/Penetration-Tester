# Web Application Databases

Web apps rely on back end databases to store content, assets, and user data (e.g., posts, images, usernames, passwords). Databases enable fast storage/retrieval and support dynamic, user-specific content.

# **Database Types and Considerations**  

Different databases are designed for different use cases. Developers typically evaluate them based on speed, storage capacity, scalability as the app grows, and cost efficiency.

---

# Relational (SQL) Databases

Store data in structured tables with rows and columns. Use keys to link tables and define relationships. Example: a `users` table with `id`, `username`, `first_name`, `last_name`; a `posts` table with `id`, `user_id`, `date`, `content`. The `user_id` key connects posts to their authors.

The relationship between tables within a database is called a Schema. 

This way, by using relational databases, it becomes very quick and easy to retrieve all data about a certain element from all databases.

|                            Type                             |                                                                                          Description                                                                                          |
| :---------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|        [MySQL](https://en.wikipedia.org/wiki/MySQL)         |                                 The most commonly used database around the internet. It is an open-source database and can be used completely free of charge                                  |
| [MSSQL](https://en.wikipedia.org/wiki/Microsoft_SQL_Server) |                                           Microsoft's implementation of a relational database. Widely used with Windows Servers and IIS web servers                                           |
|   [Oracle](https://en.wikipedia.org/wiki/Oracle_Database)   |   A very reliable database for big businesses, and is frequently updated with innovative database solutions to make it faster and more reliable. It can be costly, even for big businesses    |
|   [PostgreSQL](https://en.wikipedia.org/wiki/PostgreSQL)    | Another free and open-source relational database. It is designed to be easily extensible, enabling adding advanced new features without needing a major change to the initial database design |
Other common SQL databases include: `SQLite`, `MariaDB`, `Amazon Aurora`, and `Azure SQL`.

---

# Non-relational (NoSQL)

Do not use tables, rows, columns, keys, or strict schemas. Data is stored in flexible models depending on type. Highly scalable and adaptable for unstructured or semi-structured datasets.

**Common NoSQL Models**

Key-Value → stores data as key-value pairs (fast lookups).  
Document-Based → stores data in JSON/XML-like documents.  
Wide-Column → stores data in dynamic columns (good for large datasets).  
Graph → stores nodes/edges to represent relationships (good for networks).

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

It looks similar to a dictionary/map/key-value pair in languages like `Python` or `PHP` 'i.e. `{'key':'value'}`', where the `key` is usually a string, the `value` can be a string, dictionary, or any class object.

|                                Type                                |                                                                                           Description                                                                                            |
| :----------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|          [MongoDB](https://en.wikipedia.org/wiki/MongoDB)          |                                 The most common `NoSQL` database. It is free and open-source, uses the `Document-Based` model, and stores data in `JSON` objects                                 |
|    [ElasticSearch](https://en.wikipedia.org/wiki/Elasticsearch)    | Another free and open-source `NoSQL` database. It is optimized for storing and analyzing huge datasets. As its name suggests, searching for data within this database is very fast and efficient |
| [Apache Cassandra](https://en.wikipedia.org/wiki/Apache_Cassandra) |                                              Also free and open-source. It is very scalable and is optimized for gracefully handling faulty values                                               |
Other common `NoSQL` databases include: `Redis`, `Neo4j`, `CouchDB`, and `Amazon DynamoDB`.

---

# Use in Web Applications

Most modern web development languages and frameworks make it easy to integrate, store, and retrieve data from various database types. But first, the database has to be installed and set up on the back end server, and once it is up and running, the web applications can start utilizing it to store and retrieve data.

For example, within a `PHP` web application, once `MySQL` is up and running, we can connect to the database server with:

Code: php

```php
$conn = new mysqli("localhost", "user", "pass");
```

Then, we can create a new database with:

Code: php

```php
$sql = "CREATE DATABASE database1";
$conn->query($sql)
```

After that, we can connect to our new database, and start using the `MySQL` database through `MySQL` syntax, right within `PHP`, as follows:

Code: php

```php
$conn = new mysqli("localhost", "user", "pass", "database1");
$query = "select * from table_1";
$result = $conn->query($query);
```

Web applications usually use user-input when retrieving data. For example, when a user uses the search function to search for other users, their search input is passed to the web application, which uses the input to search within the database(s).

Code: php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
$result = $conn->query($query);
```

Finally, the web application sends the result back to the user:

Code: php

```php
while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
}
```

