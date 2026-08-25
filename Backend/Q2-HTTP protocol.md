# Backend Development — HTTP Protocol

> **Goal:** Understand how clients and servers communicate over the web using HTTP.

---

# 1. What is HTTP?

HTTP stands for:

> **HyperText Transfer Protocol**

HTTP is a **communication protocol** used mainly for communication between a client and a server over a network.

In simple terms:

> **HTTP defines how a client sends a request to a server and how the server sends a response back.**

The basic communication looks like this:

```text
Client
   │
   │ HTTP Request
   ▼
Server
   │
   │ HTTP Response
   ▼
Client
```

For example, when you open:

```text
https://example.com/users
```

your browser acts as the **client** and communicates with the server using HTTP.

---

# 2. Why Do We Need HTTP?

Imagine two applications want to communicate.

The client needs to tell the server:

```text
What do I want?
Where do I want it?
What data am I sending?
What type of data am I sending?
```

The server needs to tell the client:

```text
Did the request succeed?
What data should I return?
What type of data am I returning?
Did something go wrong?
```

HTTP provides a standardized way to communicate this information.

---

# 3. Client and Server

The two main participants are:

```text
Client              Server
   │                   │
   │──── Request ─────►│
   │                   │
   │◄──── Response ────│
   │                   │
```

### Client

The client is the application that sends the request.

Examples:

* Web browser
* React application
* Next.js application
* Mobile application
* Postman
* Another backend server

### Server

The server receives the request, processes it, and sends a response.

Examples:

* Node.js server
* Java Spring Boot server
* Python Django server
* Go server

---

# 4. HTTP Request

An HTTP request is a message sent by the client to the server.

A simplified request looks like:

```http
GET /users HTTP/1.1
Host: example.com
Accept: application/json
Authorization: Bearer token
```

An HTTP request mainly contains:

```text
Method
URL / Path
Headers
Body
```

Let's understand each one.

---

# 5. HTTP Methods

The HTTP method tells the server **what operation the client wants to perform**.

The most commonly used methods are:

| Method | Common Purpose                     |
| ------ | ---------------------------------- |
| GET    | Retrieve data                      |
| POST   | Create data / perform an operation |
| PUT    | Replace an existing resource       |
| PATCH  | Partially update a resource        |
| DELETE | Delete a resource                  |

---

## GET

Used to retrieve data.

Example:

```http
GET /users
```

Meaning:

> Give me the users.

Another example:

```http
GET /users/101
```

Meaning:

> Give me the user whose ID is 101.

---

## POST

Usually used to create a new resource or trigger an operation.

Example:

```http
POST /users
```

Request body:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com"
}
```

Meaning:

> Create a new user.

---

## PUT

Usually used to **replace the complete representation** of a resource.

Example:

```http
PUT /users/101
```

```json
{
  "name": "Lokendra",
  "email": "new@example.com",
  "age": 23
}
```

Think:

> Replace the user with this representation.

---

## PATCH

Used for a **partial update**.

Example:

```http
PATCH /users/101
```

```json
{
  "name": "Lokendra Singh"
}
```

Only the name needs to change.

---

## DELETE

Used to delete a resource.

```http
DELETE /users/101
```

Meaning:

> Delete user 101.

---

# 6. HTTP Request Structure

A request can be visualized as:

```text
┌───────────────────────────────┐
│ Method + Path + HTTP Version  │
├───────────────────────────────┤
│ Headers                       │
├───────────────────────────────┤
│                               │
│ Body                          │
│                               │
└───────────────────────────────┘
```

Example:

```http
POST /users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer abc123

{
  "name": "Lokendra",
  "email": "lokendra@example.com"
}
```

---

# 7. URL

A URL identifies the resource or endpoint the client wants to communicate with.

Example:

```text
https://api.example.com/users/101?active=true
```

Let's break it down:

```text
https://
   │
   └── Protocol

api.example.com
   │
   └── Host / Domain

/users/101
   │
   └── Path

?active=true
   │
   └── Query Parameter
```

---

# 8. Path Parameters

Path parameters identify a specific resource.

Example:

```http
GET /users/101
```

Here:

```text
101
```

is the user ID.

Another example:

```http
GET /products/500/reviews
```

Here:

```text
500
```

could represent the product ID.

In Express:

```js
app.get("/users/:id", (req, res) => {
  console.log(req.params.id);
});
```

Request:

```text
GET /users/101
```

Then:

```js
req.params.id
```

will contain:

```text
101
```

---

# 9. Query Parameters

Query parameters are commonly used for filtering, searching, sorting, and pagination.

Example:

```http
GET /products?category=mobile&sort=price
```

Here:

```text
category=mobile
sort=price
```

are query parameters.

Another example:

```http
GET /products?page=2&limit=20
```

Meaning:

> Give me page 2 with 20 products.

In Express:

```js
app.get("/products", (req, res) => {
  console.log(req.query);
});
```

The result could be:

```js
{
  page: "2",
  limit: "20"
}
```

---

# 10. Request Headers

Headers provide additional information about the request.

Example:

```http
Content-Type: application/json
Authorization: Bearer abc123
Accept: application/json
```

Common headers include:

### Content-Type

Tells the server what type of data is being sent.

```http
Content-Type: application/json
```

### Accept

Tells the server what type of response the client prefers.

```http
Accept: application/json
```

### Authorization

Used to send authentication credentials or tokens.

```http
Authorization: Bearer <token>
```

### User-Agent

Provides information about the client.

---

# 11. Request Body

The body contains data sent to the server.

For example:

```http
POST /users
Content-Type: application/json

{
  "name": "Lokendra",
  "email": "lokendra@example.com"
}
```

The JSON object is the request body.

In Express:

```js
app.use(express.json());

app.post("/users", (req, res) => {
  console.log(req.body);

  res.json({
    message: "User received"
  });
});
```

---

# 12. HTTP Response

After processing the request, the server sends a response.

Example:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "User found"
}
```

A response mainly contains:

```text
Status Code
Headers
Body
```

---

# 13. HTTP Status Codes

Status codes tell the client what happened with the request.

They are divided into categories:

```text
1xx → Informational
2xx → Success
3xx → Redirection
4xx → Client Error
5xx → Server Error
```

---

# 14. Important 2xx Status Codes

## 200 OK

The request was successful.

```http
GET /users/101
```

Response:

```http
200 OK
```

---

## 201 Created

A new resource was successfully created.

Example:

```http
POST /users
```

Response:

```http
201 Created
```

Use this commonly after successful resource creation.

---

## 204 No Content

The request succeeded but there is no response body.

Common example:

```http
DELETE /users/101
```

Response:

```http
204 No Content
```

---

# 15. Important 4xx Status Codes

4xx generally means the problem is related to the request/client side.

## 400 Bad Request

The request is invalid.

Example:

```json
{
  "age": "hello"
}
```

when the API expects a number.

---

## 401 Unauthorized

The client has not provided valid authentication credentials.

Example:

```text
No valid authentication token
```

Important:

> **401 generally means authentication is missing or invalid.**

---

## 403 Forbidden

The server understands the request, but the client does not have permission.

Example:

```text
Normal User
    ↓
DELETE /admin/users/101
    ↓
403 Forbidden
```

Important distinction:

```text
401 → Who are you?
403 → I know who you are, but you're not allowed.
```

---

## 404 Not Found

The requested resource does not exist.

```http
GET /users/999999
```

If that user doesn't exist:

```http
404 Not Found
```

---

## 409 Conflict

The request conflicts with the current state of the resource.

Example:

```text
Creating a user with an email
that already exists.
```

Response:

```http
409 Conflict
```

---

## 422 Unprocessable Content

The request format may be valid, but the provided data fails semantic validation.

Example:

```json
{
  "email": "not-an-email"
}
```

The exact use of `422` depends on the API's conventions.

---

# 16. Important 5xx Status Codes

5xx generally indicates a server-side problem.

## 500 Internal Server Error

Something unexpected happened on the server.

Example:

```text
Unhandled exception
Database failure
Unexpected application error
```

---

## 502 Bad Gateway

A server acting as a gateway/proxy received an invalid response from an upstream server.

---

## 503 Service Unavailable

The service is temporarily unable to handle the request.

Possible reasons:

```text
Server overload
Maintenance
Dependency unavailable
```

---

## 504 Gateway Timeout

A gateway/proxy did not receive a response from an upstream service within the expected time.

---

# 17. Important Status Code Cheat Sheet

```text
200 → Success
201 → Created
204 → Success, no content

400 → Bad request
401 → Authentication required/invalid
403 → Forbidden
404 → Not found
409 → Conflict
422 → Validation/semantic error

500 → Internal server error
502 → Bad gateway
503 → Service unavailable
504 → Gateway timeout
```

---

# 18. Complete HTTP Request/Response Flow

Consider:

```http
POST /users
```

Request:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com"
}
```

The complete flow is:

```text
Client
  │
  │ POST /users
  │
  │ Request Headers
  │
  │ Request Body
  ▼
Backend Server
  │
  ▼
Router
  │
  ▼
Middleware
  │
  ▼
Validation
  │
  ▼
Controller
  │
  ▼
Service
  │
  ▼
Database
  │
  ▼
Response
  │
  │ 201 Created
  │
  ▼
Client
```

This flow will become extremely important when we study backend architecture.

---

# 19. HTTP Example Using Node.js

Let's create a simple server using Express.

```js
const express = require("express");

const app = express();

app.use(express.json());

app.get("/users/:id", (req, res) => {
  const userId = req.params.id;

  res.status(200).json({
    id: userId,
    name: "Lokendra"
  });
});

app.post("/users", (req, res) => {
  const { name, email } = req.body;

  res.status(201).json({
    message: "User created",
    user: {
      name,
      email
    }
  });
});

app.listen(5000, () => {
  console.log("Server running on port 5000");
});
```

---

# 20. Testing the GET Request

Request:

```http
GET http://localhost:5000/users/101
```

Express receives:

```js
req.params.id
```

Value:

```text
101
```

Response:

```json
{
  "id": "101",
  "name": "Lokendra"
}
```

---

# 21. Testing the POST Request

Request:

```http
POST http://localhost:5000/users
Content-Type: application/json
```

Body:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com"
}
```

The server receives the body through:

```js
req.body
```

Response:

```json
{
  "message": "User created",
  "user": {
    "name": "Lokendra",
    "email": "lokendra@example.com"
  }
}
```

Status:

```text
201 Created
```

---

# 22. HTTP vs HTTPS

HTTP sends communication without encryption at the HTTP layer.

HTTPS means:

> **HTTP Secure**

HTTPS uses **TLS** to protect communication between the client and server.

Simplified:

```text
HTTP

Client ─────────────── Server
        Data


HTTPS

Client ═══════════════ Server
        Encrypted
        Communication
```

HTTPS helps provide:

* Confidentiality
* Integrity
* Server authentication

For modern production web applications:

> **HTTPS should be the default.**

---

# 23. Is HTTP Stateless?

Yes.

HTTP is fundamentally **stateless**.

That means:

> Each HTTP request is independent, and HTTP itself does not automatically remember previous requests.

For example:

```text
Request 1
GET /profile

Request 2
GET /orders
```

HTTP doesn't inherently remember that both requests came from the same user.

Applications implement state using mechanisms such as:

```text
Cookies
Sessions
Tokens
Databases
Caches
```

This becomes especially important when learning authentication.

---

# 24. HTTP Versions

You may encounter:

```text
HTTP/1.0
HTTP/1.1
HTTP/2
HTTP/3
```

### HTTP/1.1

Widely used and introduced persistent connections and other improvements over HTTP/1.0.

### HTTP/2

Introduced improvements such as:

* Multiplexing
* Header compression
* Binary framing

Multiple streams can share a connection.

### HTTP/3

Uses **QUIC**, which runs over UDP instead of TCP.

It improves connection establishment and can handle packet loss more efficiently in many scenarios.

For backend interviews, you should at least know the basic differences.

---

# 25. HTTP vs TCP

These are different layers/concepts.

A simplified model:

```text
Application Layer
       ↓
      HTTP
       ↓
   Transport
       ↓
 TCP / UDP
       ↓
     IP
       ↓
    Network
```

HTTP defines the rules for web/application communication.

TCP provides reliable transport for HTTP/1.1 and HTTP/2.

HTTP/3 uses QUIC, which runs over UDP.

Don't confuse:

```text
HTTP ≠ TCP
```

---

# 26. Important HTTP Concepts

Before moving forward, you should know these terms:

```text
HTTP
HTTPS
Client
Server
Request
Response
Method
URL
Path
Headers
Body
Query Parameters
Path Parameters
Status Codes
Cookies
Statelessness
HTTP/1.1
HTTP/2
HTTP/3
```

---

# 27. Interview-Friendly Answers

## Q1. What is HTTP?

> HTTP is an application-layer communication protocol used for communication between clients and servers. A client sends an HTTP request containing information such as the method, URL, headers, and optionally a body, and the server processes it and returns an HTTP response containing a status code, headers, and optionally a body.

### Short answer

> HTTP is a protocol that defines how clients and servers communicate using requests and responses.

---

## Q2. What is the difference between HTTP and HTTPS?

> HTTP is the standard application-layer protocol for web communication, while HTTPS is HTTP secured using TLS. HTTPS provides encryption, integrity, and server authentication, making it suitable for secure communication.

---

## Q3. What is the difference between 401 and 403?

> 401 generally means the client has not provided valid authentication credentials, while 403 means the client is authenticated or otherwise identified but does not have permission to access the requested resource.

Easy way to remember:

```text
401 → Authentication problem
403 → Authorization problem
```

---

## Q4. What is the difference between PUT and PATCH?

> PUT is generally used to replace the complete representation of a resource, while PATCH is used for partial updates.

Example:

```text
PUT /users/101
→ Replace the user

PATCH /users/101
→ Change only selected fields
```

---

## Q5. Is HTTP stateful or stateless?

> HTTP is fundamentally stateless. Each request is independent, and HTTP itself doesn't maintain client state between requests. Applications can maintain state using mechanisms such as cookies, sessions, tokens, databases, or caches.

---

## Q6. What are HTTP headers?

> HTTP headers are key-value metadata sent with requests and responses. They provide additional information such as content type, authentication credentials, caching instructions, and client information.

---

# 28. Common Beginner Mistakes

### Mistake 1

Thinking:

```text
GET = Always safe
POST = Always unsafe
```

HTTP methods have defined semantics, but the actual behavior depends on the application.

---

### Mistake 2

Confusing:

```text
401
403
```

Remember:

```text
401 → Authentication
403 → Authorization
```

---

### Mistake 3

Thinking:

```text
404 = Server is down
```

Not necessarily.

404 means the requested resource was not found.

---

### Mistake 4

Thinking HTTP and HTTPS are completely different protocols.

HTTPS is essentially HTTP secured with TLS.

---

### Mistake 5

Thinking the frontend can be trusted.

The backend must independently validate important data and enforce authorization.

---

# 29. Real-World Mental Model

Think about an online shopping application.

User clicks:

```text
"Buy Now"
```

Frontend sends:

```http
POST /orders
Content-Type: application/json
Authorization: Bearer token
```

Body:

```json
{
  "productId": 101,
  "quantity": 2
}
```

Backend:

```text
Receive Request
      ↓
Authenticate User
      ↓
Authorize User
      ↓
Validate Data
      ↓
Check Product
      ↓
Check Stock
      ↓
Calculate Price
      ↓
Create Order
      ↓
Save to Database
      ↓
Return Response
```

Response:

```http
201 Created
```

```json
{
  "orderId": "ORD123",
  "status": "created"
}
```

This is the foundation of almost every API-driven application.

---

# 30. Quick Revision

Remember this model:

```text
                 HTTP
                  │
        ┌─────────┴─────────┐
        │                   │
     REQUEST             RESPONSE
        │                   │
   ┌────┼────┐         ┌────┼────┐
   │    │    │         │    │    │
Method URL Headers   Status Headers Body
        │
       Body
```

And the most important flow:

```text
Client
  ↓
HTTP Request
  ↓
Server
  ↓
Process Request
  ↓
HTTP Response
  ↓
Client
```

---

# 31. What You Should Know Before Moving On

* [ ] What HTTP is
* [ ] Client vs server
* [ ] HTTP request
* [ ] HTTP response
* [ ] HTTP methods
* [ ] GET / POST / PUT / PATCH / DELETE
* [ ] URL
* [ ] Path parameters
* [ ] Query parameters
* [ ] Headers
* [ ] Request body
* [ ] Status codes
* [ ] 2xx / 4xx / 5xx
* [ ] 401 vs 403
* [ ] HTTP vs HTTPS
* [ ] HTTP statelessness
* [ ] Basic HTTP versions
* [ ] HTTP vs TCP
* [ ] Basic Node.js/Express HTTP implementation

---

# One-Line Interview Summary

> **HTTP is a stateless application-layer protocol that defines how clients and servers communicate through requests and responses, including methods, URLs, headers, bodies, and status codes.**
