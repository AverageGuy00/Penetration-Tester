Web application layouts consist of many different layers that can be summarized with the following three main categories:

|           **Category**           |                                                                                                                           **Description**                                                                                                                            |
| :------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| `Web Application Infrastructure` | Describes the structure of required components, such as the database, needed for the web application to function as intended. Since the web application can be set up to run on a separate server, it is essential to know which database server it needs to access. |
|   `Web Application Components`   |                          The components that make up a web application represent all the components that the web application interacts with. These are divided into the following three areas: `UI/UX`, `Client`, and `Server` components.                           |
|  `Web Application Architecture`  |                                                                                     Architecture comprises all the relationships between the various web application components.                                                                                     |

---

# Web Application Infrastracture

## Client-Server

- Basic model where the **client** (browser, app) sends requests and the **server** responds with data or resources.
- Most common architecture in web apps.
- Example: A browser requesting a web page from a web server.

## One Server

- All components (application logic, database, files) run on a **single server**.
- Easy to set up and manage, but limited in scalability and resilience.
- Common for small projects, prototypes, or internal tools.

## Many Servers – One Database

- Application logic is **distributed across multiple servers** (load balancing).
- All servers connect to a **single shared database**.
- Improves scalability and fault tolerance of the application layer, but the database can become a **bottleneck**.

## Many Servers – Many Databases

- Both application logic and database are **distributed**.
- Databases may be **replicated** (copies of same data for availability) or **sharded** (different data segments stored in different DBs).
- High scalability and resilience; used in large-scale systems (e.g., big web apps, e-commerce platforms).

---

# Web Application Components

Each web application can have a different number of components. Nevertheless, all of the components of the models mentioned previously can be broken down to:

1. `Client`
2. `Server`
    - Webserver
    - Web Application Logic
    - Database
3. `Services` (Microservices)
    - 3rd Party Integrations
    - Web Application Integrations
4. `Functions` (Serverless)

---

# Web Application Architecture

The components of a web application are divided into three different layers (AKA Three Tier Architecture).

|      **Layer**       |                                                                                                   **Description**                                                                                                   |
| :------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| `Presentation Layer` | Consists of UI process components that enable communication with the application and the system. These can be accessed by the client via the web browser and are returned in the form of HTML, JavaScript, and CSS. |
| `Application Layer`  |               This layer ensures that all client requests (web requests) are correctly processed. Various criteria are checked, such as authorization, privileges, and data passed on to the client.                |
|     `Data Layer`     |                                         The data layer works closely with the application layer to determine exactly where the required data is stored and can be accessed.                                         |

---

#### Microservices

We can think of microservices as independent components of the web application, which in most cases are programmed for one task only. For example, for an online store, we can decompose core tasks into the following components:

- Registration
- Search
- Payments
- Ratings
- Reviews

## Characteristics

- **Stateless communication** – each request/response is independent.
- **Data stored separately** from microservices. 
- **Polyglot programming** – microservices can be written in different languages but still interact.
- **Interdependence** – services rely on each other to form the complete application.

## Benefits

- **Agility** – enables faster development cycles.
- **Flexible scaling** – scale only the services under heavy load.
- **Easy deployment** – deploy updates without impacting the entire system.
- **Reusable code** – services can be reused across different applications. 
- **Resilience** – failure in one service does not necessarily crash the entire system.