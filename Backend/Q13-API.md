# APIs — Complete Backend Notes

## 1. What is an API?

API stands for **Application Programming Interface**.

An API is a mechanism that allows two different software systems to communicate with each other.

In backend development, an API usually allows a frontend application to communicate with a backend server.

### Basic Flow

    Frontend → API → Backend → Database
                       ↓
                    Response
                       ↓
                    Frontend

For example, a React application wants to display products.

    React Frontend
          ↓
    GET /api/products
          ↓
    Express Backend
          ↓
       MongoDB
          ↓
    Express Backend
          ↓
    JSON Response
          ↓
    React Frontend

The frontend should generally not directly access the database.

---

# 2. Why Do We Need APIs?

APIs provide a controlled way for applications to communicate with backend systems.

Without an API:

    Frontend → Database

This is unsafe because the frontend could potentially access sensitive database operations directly.

With an API:

    Frontend
       ↓
    API
       ↓
    Authentication
       ↓
    Authorization
       ↓
    Validation
       ↓
    Business Logic
       ↓
    Database

The backend controls:

- What data can be accessed
- Who can access it
- Which operations are allowed
- How input is validated
- How business rules are applied
- How errors are returned
- How security is enforced

---

# 3. API Client

The client is the application that makes the API request.

Examples:

- React application
- Next.js application
- Mobile application
- Another backend server
- Browser
- Postman
- CLI application

Example:

    React App
        ↓
      API

or:

    Mobile App
        ↓
      API

or:

    Backend A
        ↓
      API
        ↓
    Backend B

---

# 4. API Server

The API server receives requests and sends responses.

A typical backend flow looks like:

    Client
      ↓
    Route
      ↓
    Middleware
      ↓
    Controller
      ↓
    Service
      ↓
    Database
      ↓
    Service
      ↓
    Controller
      ↓
    Response
      ↓
    Client

The API server can handle:

- Authentication
- Authorization
- Validation
- Business logic
- Database operations
- External API calls
- File uploads
- Error handling
- Logging
- Rate limiting

---

# 5. API Request and Response

The basic communication model is:

    Client → Request → Server
    Client ← Response ← Server

Example request:

    GET /api/users/10

Example response:

    {
      "id": 10,
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

---

# 6. API Endpoint

An API endpoint is a specific API location used to perform an operation.

Examples:

    GET /api/users

    GET /api/users/123

    POST /api/users

    PATCH /api/users/123

    DELETE /api/users/123

The combination of:

    HTTP Method + URL

defines a specific API operation.

For example:

    GET /api/users

means:

    Retrieve users

while:

    POST /api/users

means:

    Create a user

---

# 7. API Route

A route defines how the backend handles a particular request.

Example using Express:

    app.get("/api/users", getUsers);

Here:

    GET /api/users

is the API route/endpoint exposed by the server.

Another example:

    app.post("/api/users", createUser);

---

# 8. HTTP Methods

HTTP methods tell the server what kind of operation the client wants to perform.

| Method | Purpose |
|---|---|
| GET | Retrieve data |
| POST | Create data / submit data |
| PUT | Replace a resource |
| PATCH | Partially update a resource |
| DELETE | Delete data |

Typical CRUD mapping:

    GET     /users       → Read
    POST    /users       → Create
    GET     /users/10    → Read one
    PUT     /users/10    → Replace
    PATCH   /users/10    → Partial update
    DELETE  /users/10    → Delete

---

# 9. GET API

GET is used to retrieve data.

Example:

    GET /api/products

Possible response:

    {
      "success": true,
      "data": [
        {
          "id": 1,
          "name": "iPhone 15",
          "price": 60000
        },
        {
          "id": 2,
          "name": "Samsung S24",
          "price": 70000
        }
      ]
    }

GET should generally not modify server-side data.

---

# 10. POST API

POST is generally used to create a new resource or submit data for processing.

Example:

    POST /api/users

Request body:

    {
      "name": "Lokendra",
      "email": "lokendra@example.com",
      "password": "123456"
    }

Possible response:

    {
      "success": true,
      "message": "User created successfully",
      "data": {
        "id": 101,
        "name": "Lokendra",
        "email": "lokendra@example.com"
      }
    }

---

# 11. PUT API

PUT is generally used to replace the complete representation of a resource.

Example:

    PUT /api/users/101

Request:

    {
      "name": "Lokendra Singh",
      "email": "lokendra@example.com",
      "phone": "9876543210"
    }

Think:

    PUT = Replace the resource

If a resource has several fields, PUT generally represents the complete updated resource.

---

# 12. PATCH API

PATCH is generally used to partially update a resource.

Example:

    PATCH /api/users/101

Request:

    {
      "name": "Lokendra Singh"
    }

Only the specified field needs to be changed.

Think:

    PATCH = Partial update

---

# 13. DELETE API

DELETE is used to delete a resource.

Example:

    DELETE /api/users/101

Possible response:

    {
      "success": true,
      "message": "User deleted successfully"
    }

Another valid response is:

    204 No Content

---

# 14. URL Structure

Consider this URL:

    https://example.com/api/users/123?active=true

Breakdown:

    https://
        ↓
    Protocol

    example.com
        ↓
    Domain

    /api/users/123
        ↓
    Path

    ?active=true
        ↓
    Query Parameter

---

# 15. Path Parameters

Path parameters are used to identify a specific resource.

Example:

    GET /api/users/123

Here:

    123

is the path parameter.

Express:

    app.get("/api/users/:id", (req, res) => {
      console.log(req.params.id);
    });

Request:

    GET /api/users/123

Output:

    123

Use path parameters when identifying a particular resource.

Examples:

    /users/10
    /products/20
    /orders/500

---

# 16. Query Parameters

Query parameters are commonly used for:

- Filtering
- Searching
- Sorting
- Pagination
- Optional parameters

Example:

    GET /api/products?category=mobile&sort=price

Express:

    app.get("/api/products", (req, res) => {
      console.log(req.query.category);
      console.log(req.query.sort);
    });

Output:

    mobile
    price

Another example:

    GET /api/products?minPrice=10000&maxPrice=50000

---

# 17. Path Parameters vs Query Parameters

### Path Parameter

Used to identify a specific resource.

    GET /api/users/123

Meaning:

    Get user 123

### Query Parameter

Used to modify or filter the request.

    GET /api/users?role=admin

Meaning:

    Get users whose role is admin

### Easy Interview Rule

    Path Parameter → Which resource?

    Query Parameter → How do I want the data?

---

# 18. Request Body

The request body contains data sent to the server.

Example:

    POST /api/users

    Content-Type: application/json

Request body:

    {
      "name": "Lokendra",
      "email": "lokendra@example.com",
      "password": "123456"
    }

In Express:

    app.use(express.json());

    app.post("/api/users", (req, res) => {
      console.log(req.body);
    });

---

# 19. HTTP Headers

Headers contain additional information about a request or response.

Example:

    Content-Type: application/json
    Authorization: Bearer <token>

Common request headers:

- Content-Type
- Authorization
- Accept
- Cookie
- User-Agent

Headers can provide metadata about the request.

---

# 20. Content-Type

Content-Type tells the server what type of data is being sent.

Example:

    Content-Type: application/json

This means:

    The request body contains JSON.

Common Content-Type values:

    application/json
    multipart/form-data
    application/x-www-form-urlencoded
    text/plain

For file uploads, we commonly use:

    multipart/form-data

---

# 21. Authorization Header

Authentication tokens are commonly sent using:

    Authorization: Bearer <token>

Example:

    Authorization: Bearer eyJhbGciOiJIUzI1Ni...

The backend can verify the token before allowing access to protected resources.

Example:

    Client
      ↓
    Authorization: Bearer token
      ↓
    Backend
      ↓
    Verify token
      ↓
    Allow / Reject request

---

# 22. API Response

A response generally contains:

- Status code
- Headers
- Response body

Example:

    HTTP/1.1 200 OK
    Content-Type: application/json

Body:

    {
      "success": true,
      "data": {
        "id": 1,
        "name": "Lokendra"
      }
    }

---

# 23. HTTP Status Codes

Status codes tell the client what happened.

### 2xx — Success

    200 OK
    201 Created
    204 No Content

### 4xx — Client Error

    400 Bad Request
    401 Unauthorized
    403 Forbidden
    404 Not Found
    409 Conflict
    422 Unprocessable Entity

### 5xx — Server Error

    500 Internal Server Error
    502 Bad Gateway
    503 Service Unavailable

---

# 24. Important Status Codes

## 200 OK

Request succeeded.

Example:

    GET /api/users/10

Response:

    200 OK

---

## 201 Created

Used when a resource is successfully created.

Example:

    POST /api/users

Response:

    201 Created

---

## 204 No Content

Request succeeded but there is no response body.

Example:

    DELETE /api/users/10

Response:

    204 No Content

---

## 400 Bad Request

The request is invalid.

Example:

    {
      "email": "invalid-email"
    }

The server cannot process the request because the input is invalid.

---

## 401 Unauthorized

Usually means the user is not authenticated or the authentication credentials are invalid/missing.

Example:

    Request without a valid access token

---

## 403 Forbidden

The user is authenticated but does not have permission to perform the operation.

Example:

    Normal User
        ↓
    Admin API
        ↓
    403 Forbidden

---

## 404 Not Found

The requested resource doesn't exist.

Example:

    GET /api/users/999999

If user 999999 doesn't exist:

    404 Not Found

---

## 409 Conflict

The request conflicts with existing data or the current state of the resource.

Example:

    Registering with an email that already exists

Possible response:

    409 Conflict

---

## 500 Internal Server Error

An unexpected error occurred on the server.

Examples:

- Unexpected exception
- Database failure
- Programming error

---

# 25. API Response Structure

A consistent response format makes APIs easier to consume.

Example:

    {
      "success": true,
      "message": "User fetched successfully",
      "data": {
        "id": 1,
        "name": "Lokendra"
      }
    }

Error response:

    {
      "success": false,
      "message": "User not found",
      "error": {
        "code": "USER_NOT_FOUND"
      }
    }

The exact response structure depends on the project's API conventions.

---

# 26. REST API

REST stands for:

    Representational State Transfer

REST is an architectural style for designing APIs.

A RESTful API generally uses:

- Resources
- HTTP methods
- HTTP status codes
- Stateless communication
- Standard HTTP semantics

Example resources:

    /users
    /products
    /orders

---

# 27. RESTful URL Design

Prefer:

    GET     /api/users
    GET     /api/users/123
    POST    /api/users
    PATCH   /api/users/123
    DELETE  /api/users/123

Instead of:

    GET  /api/getUsers
    POST /api/createUser
    GET  /api/getUserById/123
    POST /api/deleteUser

Why?

Because in REST APIs:

    HTTP Method → Action
    URL         → Resource

So:

    GET /users

already tells us:

    Get users

---

# 28. Resources

REST APIs are generally designed around resources rather than actions.

Example resources:

    users
    products
    orders
    payments
    comments

Good:

    GET /api/products

Bad style:

    GET /api/getAllProducts

Good:

    POST /api/orders

Instead of:

    POST /api/createOrder

---

# 29. Statelessness

One important REST principle is that requests should be stateless.

The server should not depend on previous requests to understand the current request.

For example:

    Request 1:
    GET /api/profile
    Authorization: Bearer token

    Request 2:
    GET /api/orders
    Authorization: Bearer token

Each request contains the necessary authentication information.

The server doesn't have to remember the previous request to process the next one.

---

# 30. Idempotency

An operation is idempotent if performing the same request multiple times produces the same intended final state.

Common examples:

    GET
    PUT
    DELETE

Example:

    PUT /users/10

If we send the same request multiple times:

    {
      "name": "Lokendra"
    }

the final user state remains:

    name = Lokendra

Important:

Idempotent does not necessarily mean the response is identical every time.

It means repeated execution has the same intended effect on the resource.

---

# 31. GET vs POST

### GET

Used for retrieving data.

    GET /api/users

Data is commonly passed using:

    Query Parameters
    Path Parameters

### POST

Used for creating/submitting data.

    POST /api/users

Data is commonly sent in:

    Request Body

Simple rule:

    GET  → Read
    POST → Create/Submit

---

# 32. PUT vs PATCH

### PUT

Generally replaces the complete resource.

    PUT /users/10

### PATCH

Generally modifies only part of the resource.

    PATCH /users/10

Example:

Current resource:

    {
      "name": "Lokendra",
      "email": "lokendra@example.com",
      "age": 25
    }

PATCH:

    {
      "age": 26
    }

Only the age is changed.

---

# 33. API Versioning

APIs evolve over time.

Suppose we have:

    /api/v1/users

Later, we introduce breaking changes.

Instead of immediately breaking existing clients, we can create:

    /api/v2/users

Example:

    /api/v1/users
    /api/v2/users

Common versioning approaches:

### URL Versioning

    /api/v1/users

### Header Versioning

    Accept: application/vnd.company.v1+json

### Query Parameter Versioning

    /api/users?version=1

URL versioning is simple and commonly used.

---

# 34. Pagination

Suppose our database contains 1 million products.

We should not return all 1 million products in one API response.

Instead, use pagination.

Example:

    GET /api/products?page=1&limit=20

Meaning:

    Page 1
    20 products

Another request:

    GET /api/products?page=2&limit=20

Response:

    {
      "success": true,
      "data": [...],
      "pagination": {
        "page": 1,
        "limit": 20,
        "total": 1000000,
        "totalPages": 50000
      }
    }

Pagination improves:

- Response size
- Database load
- Network usage
- Frontend performance

---

# 35. Offset Pagination

A common approach is:

    GET /api/products?page=2&limit=20

Internally:

    offset = (page - 1) * limit

For page 2:

    offset = (2 - 1) * 20
           = 20

The database skips the first 20 records and returns the next 20.

---

# 36. Cursor Pagination

Cursor pagination uses a value representing the position in the dataset.

Example:

    GET /api/products?limit=20&cursor=abc123

The server returns:

    {
      "data": [...],
      "nextCursor": "xyz456"
    }

The client then requests:

    GET /api/products?limit=20&cursor=xyz456

Cursor pagination is often better for very large or frequently changing datasets because it avoids some problems associated with large offsets.

---

# 37. Filtering

Filtering allows clients to request only specific data.

Example:

    GET /api/products?category=mobile

Another:

    GET /api/products?minPrice=10000&maxPrice=50000

The backend applies these filters to the database query.

---

# 38. Sorting

Sorting allows clients to control the order of results.

Example:

    GET /api/products?sort=price

Descending:

    GET /api/products?sort=-price

Possible API convention:

    sort=price
    sort=-price

where:

    price  → ascending
    -price → descending

The exact convention depends on the API.

---

# 39. Searching

Searching is commonly implemented using a query parameter.

Example:

    GET /api/products?search=iphone

The backend can search relevant fields such as:

    name
    description
    brand

For large-scale applications, specialized search systems such as Elasticsearch may be used.

---

# 40. Combining Pagination, Filtering and Sorting

Real-world APIs often combine these features.

Example:

    GET /api/products?page=1&limit=20&category=mobile&sort=-price&search=iphone

This means:

    Page       → 1
    Limit      → 20
    Category   → mobile
    Sort       → price descending
    Search     → iphone

---

# 41. Authentication in APIs

Authentication answers:

    "Who are you?"

Example:

    User logs in
       ↓
    Backend verifies credentials
       ↓
    Backend generates access token
       ↓
    Client stores token
       ↓
    Client sends token with future requests

Example:

    Authorization: Bearer <token>

---

# 42. Authorization in APIs

Authorization answers:

    "Are you allowed to perform this action?"

Example:

    User
      ↓
    GET /api/profile
      ↓
    Allowed

But:

    User
      ↓
    DELETE /api/users/10
      ↓
    Admin permission required
      ↓
    403 Forbidden

Authentication:

    Who are you?

Authorization:

    What are you allowed to do?

---

# 43. API Validation

Never blindly trust client input.

Example request:

    POST /api/users

    {
      "name": "",
      "email": "wrong",
      "age": -10
    }

The backend should validate:

- Required fields
- Data types
- String length
- Email format
- Numeric ranges
- Allowed values
- Business rules

Example:

    name → required
    email → valid email
    age → positive number

Validation should happen on the backend even if the frontend already validates the data.

---

# 44. API Error Handling

APIs should return meaningful errors.

Bad:

    500
    Something went wrong

Better:

    {
      "success": false,
      "message": "Email is already registered",
      "code": "EMAIL_EXISTS"
    }

The response should help the client understand what happened without exposing sensitive internal information.

---

# 45. API Security

Important API security practices include:

- HTTPS
- Authentication
- Authorization
- Input validation
- Rate limiting
- Secure headers
- CORS configuration
- Proper error handling
- Password hashing
- Token security
- Protection against injection attacks
- Request size limits

Never trust client input.

---

# 46. Rate Limiting

Rate limiting controls how many requests a client can make in a given time.

Example:

    100 requests / minute / user

If the client exceeds the limit:

    429 Too Many Requests

Example:

    User
      ↓
    100 requests/minute
      ↓
    Request #101
      ↓
    429 Too Many Requests

Rate limiting helps protect APIs from:

- Abuse
- Brute-force attacks
- Accidental traffic spikes
- Excessive resource usage

---

# 47. CORS

CORS stands for:

    Cross-Origin Resource Sharing

It controls whether a browser is allowed to make requests from one origin to another.

Example:

    Frontend:
    https://myapp.com

    Backend:
    https://api.myapp.com

The backend needs appropriate CORS configuration to allow the frontend origin.

Express example:

    import cors from "cors";

    app.use(cors({
      origin: "https://myapp.com"
    }));

CORS is primarily a browser security mechanism.

It does not replace authentication or authorization.

---

# 48. API Gateway

In a microservices architecture, clients may need to communicate with many services.

Instead of:

    Client
      ├── User Service
      ├── Order Service
      ├── Payment Service
      └── Product Service

we can use:

    Client
       ↓
    API Gateway
       ↓
    ├── User Service
    ├── Order Service
    ├── Payment Service
    └── Product Service

An API Gateway can handle:

- Routing
- Authentication
- Rate limiting
- Request transformation
- Logging
- Load balancing integration

---

# 49. API vs Webhook

### API

The client asks the server for something.

    Client → Server

Example:

    GET /api/orders/123

### Webhook

The server sends a notification to another system when an event occurs.

    Server → Client/System

Example:

    Payment completed
         ↓
    Payment provider
         ↓
    POST /api/webhooks/payment
         ↓
    Your Backend

Simple difference:

    API     → "Give me information."

    Webhook → "Something happened."

---

# 50. API vs WebSocket

### REST API

Communication generally follows:

    Client → Request
    Server → Response

Example:

    GET /api/messages

### WebSocket

Creates a persistent connection:

    Client ←→ Server

The server can push data to the client without waiting for a new HTTP request.

Useful for:

- Chat applications
- Live notifications
- Real-time dashboards
- Multiplayer games
- Live tracking

---

# 51. External APIs

A backend can also consume APIs provided by other companies.

Example:

    Your Backend
         ↓
    Payment API
         ↓
    Payment Provider

or:

    Your Backend
         ↓
    Maps API
         ↓
    Location Data

or:

    Your Backend
         ↓
    Email API
         ↓
    Email Provider

Your backend becomes both:

    API Provider

and sometimes:

    API Consumer

---

# 52. API Consumption in Node.js

Node.js can call external APIs using:

- fetch
- Axios
- Other HTTP clients

Example using fetch:

    const response = await fetch(
      "https://example.com/api/users"
    );

    const data = await response.json();

    console.log(data);

The flow is:

    Your Backend
         ↓
    External API
         ↓
    Response
         ↓
    Your Backend
         ↓
    Your Client

---

# 53. API Timeout

External APIs may become slow or unavailable.

Therefore, production systems should consider timeouts.

Example concept:

    Your Backend
         ↓
    External API
         ↓
    No response
         ↓
    Timeout
         ↓
    Handle failure

Without appropriate timeouts, backend requests can remain blocked for too long.

---

# 54. API Retry

Sometimes an external API temporarily fails.

The backend may retry certain requests.

Example:

    Request
       ↓
    External API
       ↓
    Temporary failure
       ↓
    Retry
       ↓
    Success

Retries should be used carefully.

Do not blindly retry every error.

For example:

    500 → May be retryable

while:

    400 → Usually retrying won't fix the request

For operations that create side effects, idempotency is especially important when using retries.

---

# 55. API Caching

Some API responses don't change frequently.

Example:

    GET /api/products/categories

Instead of querying the database every time:

    Client
      ↓
    API
      ↓
    Cache
      ↓
    Response

Caching can improve:

- Response time
- Database performance
- Scalability

Common caching systems include Redis.

---

# 56. API Documentation

API documentation explains how clients should use the API.

Documentation should describe:

- Endpoint
- HTTP method
- Request parameters
- Request body
- Headers
- Authentication
- Response
- Status codes
- Errors

Example:

    GET /api/users/:id

    Path Parameter:
    id → User ID

    Response:

    {
      "id": 1,
      "name": "Lokendra"
    }

OpenAPI/Swagger is commonly used for API documentation.

---

# 57. API Testing

APIs can be tested using tools such as:

- Postman
- Insomnia
- curl
- Automated test frameworks

Example request:

    GET http://localhost:5000/api/users

You can verify:

- Status code
- Response body
- Headers
- Authentication
- Validation
- Error cases
- Performance

---

# 58. API Testing — Important Cases

For a user API:

### Success

    GET /api/users/10

Expected:

    200 OK

### User doesn't exist

    GET /api/users/999

Expected:

    404 Not Found

### Invalid ID

    GET /api/users/abc

Expected:

    400 Bad Request

### Unauthorized request

    GET /api/profile

Without token:

    401 Unauthorized

### Forbidden request

Authenticated normal user trying to access admin functionality:

    403 Forbidden

---

# 59. API Naming Best Practices

Use nouns for resources.

Good:

    /users
    /products
    /orders

Avoid unnecessary verbs:

    /getUsers
    /createUser
    /deleteUser

Use HTTP methods to express actions:

    GET    /users
    POST   /users
    DELETE /users/10

---

# 60. Use Consistent Naming

Good:

    /api/users
    /api/products
    /api/orders

Avoid inconsistent naming:

    /api/getUsers
    /api/product-list
    /api/create_order

Choose a consistent convention across the API.

---

# 61. API Response Should Be Predictable

For example:

    {
      "success": true,
      "message": "Products fetched successfully",
      "data": [...]
    }

For errors:

    {
      "success": false,
      "message": "Product not found",
      "code": "PRODUCT_NOT_FOUND"
    }

Consistency makes frontend development easier.

---

# 62. API Layering in a Real Backend

A clean backend often separates responsibilities.

Example:

    Request
      ↓
    Route
      ↓
    Middleware
      ↓
    Controller
      ↓
    Service
      ↓
    Repository / Data Access
      ↓
    Database

### Route

Defines the endpoint.

### Middleware

Handles cross-cutting concerns such as authentication.

### Controller

Handles HTTP request/response.

### Service

Contains business logic.

### Repository / Data Access

Communicates with the database.

### Database

Stores persistent data.

---

# 63. Example Express API Structure

A project can be organized like:

    src/
    ├── routes/
    │   └── user.routes.js
    │
    ├── controllers/
    │   └── user.controller.js
    │
    ├── services/
    │   └── user.service.js
    │
    ├── models/
    │   └── user.model.js
    │
    ├── middlewares/
    │   └── auth.middleware.js
    │
    └── app.js

This separation keeps the code maintainable.

---

# 64. Simple Node.js + Express API

Example:

    import express from "express";

    const app = express();

    app.use(express.json());

    app.get("/api/users", (req, res) => {
      res.status(200).json({
        success: true,
        data: [
          {
            id: 1,
            name: "Lokendra"
          }
        ]
      });
    });

    app.post("/api/users", (req, res) => {
      const { name, email } = req.body;

      res.status(201).json({
        success: true,
        message: "User created",
        data: {
          name,
          email
        }
      });
    });

    app.listen(5000, () => {
      console.log("Server running on port 5000");
    });

---

# 65. API Request Flow in Express

Suppose the client sends:

    POST /api/users

    {
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

The backend can process it like:

    Client
      ↓
    Express Router
      ↓
    Validation Middleware
      ↓
    Authentication Middleware
      ↓
    Controller
      ↓
    User Service
      ↓
    User Model
      ↓
    Database
      ↓
    Response
      ↓
    Client

---

# 66. Real-World Login API

Example:

    POST /api/auth/login

Request:

    {
      "email": "lokendra@example.com",
      "password": "password123"
    }

Backend:

    1. Validate request
    2. Find user
    3. Compare password hash
    4. Generate access token
    5. Return response

Response:

    {
      "success": true,
      "message": "Login successful",
      "data": {
        "accessToken": "..."
      }
    }

Then the client sends:

    Authorization: Bearer <accessToken>

with protected requests.

---

# 67. Real-World E-Commerce API

### Products

    GET /api/products

### Single Product

    GET /api/products/123

### Create Product

    POST /api/products

### Update Product

    PATCH /api/products/123

### Delete Product

    DELETE /api/products/123

### Create Order

    POST /api/orders

### Get User Orders

    GET /api/users/123/orders

### Get Single Order

    GET /api/orders/500

This creates a predictable API structure.

---

# 68. API Security Checklist

For production APIs:

- Use HTTPS
- Validate all input
- Authenticate protected endpoints
- Authorize users
- Hash passwords
- Protect tokens
- Configure CORS correctly
- Implement rate limiting
- Limit request body size
- Prevent injection attacks
- Avoid exposing stack traces
- Avoid returning sensitive information
- Keep secrets in environment variables
- Log security-relevant events
- Keep dependencies updated

---

# 69. Common API Mistakes

### Mistake 1 — Returning everything

Bad:

    GET /api/users

returns:

    password
    refreshToken
    internal fields
    sensitive information

Never expose sensitive fields.

---

### Mistake 2 — No validation

Bad:

    POST /api/users

Accepting:

    {
      "email": "anything"
    }

without validation.

---

### Mistake 3 — Using incorrect status codes

For example:

    User not found → 200 OK

Better:

    User not found → 404 Not Found

---

### Mistake 4 — Putting business logic in routes

Avoid large route handlers.

Bad architecture:

    app.post("/orders", async (req, res) => {
      // 200 lines of business logic
    });

Better:

    Route
      ↓
    Controller
      ↓
    Service
      ↓
    Database

---

### Mistake 5 — No pagination

Returning thousands or millions of records can hurt performance.

---

# 70. API Performance

API performance depends on several layers:

    Client
      ↓
    Network
      ↓
    API Server
      ↓
    Business Logic
      ↓
    Database
      ↓
    External Services

Ways to improve performance:

- Database indexing
- Caching
- Pagination
- Efficient queries
- Connection pooling
- Compression
- Load balancing
- Async processing
- Avoiding unnecessary external API calls

---

# 71. API Scalability

As traffic increases:

    100 users
       ↓
    10,000 users
       ↓
    1,000,000 users

A single backend server may not be enough.

We can use:

    Load Balancer
         ↓
    ┌─────────────┐
    ↓      ↓      ↓
    API    API    API
    Server Server Server
         ↓
      Database
         ↓
       Cache

Additional techniques:

- Horizontal scaling
- Caching
- Database replication
- Queue systems
- CDN
- Load balancing
- Microservices where appropriate

---

# 72. API Gateway vs Load Balancer

### Load Balancer

Main purpose:

    Distribute traffic across servers.

Example:

    Load Balancer
       ├── Server 1
       ├── Server 2
       └── Server 3

### API Gateway

Provides an entry point for APIs and can handle:

- Routing
- Authentication
- Rate limiting
- Request transformation
- API policies

Example:

    Client
      ↓
    API Gateway
      ↓
    Services

They can be used together.

---

# 73. API and Database

An API should generally not expose the database directly.

Example:

    Client
      ↓
    API
      ↓
    Service
      ↓
    Database

The service can transform database data into a safe API response.

Database document:

    {
      "_id": "...",
      "name": "Lokendra",
      "passwordHash": "...",
      "internalField": "..."
    }

API response:

    {
      "id": "...",
      "name": "Lokendra"
    }

This protects internal information.

---

# 74. API Contract

An API contract defines how the client and server communicate.

It can specify:

    Endpoint
    HTTP Method
    Request Format
    Response Format
    Status Codes
    Authentication
    Error Format

Example:

    GET /api/users/:id

Request:

    GET /api/users/10

Response:

    {
      "id": 10,
      "name": "Lokendra"
    }

Both frontend and backend teams can work according to this contract.

---

# 75. Breaking vs Non-Breaking API Changes

### Breaking Change

A change that can break existing clients.

Example:

Old response:

    {
      "name": "Lokendra"
    }

New response:

    {
      "userName": "Lokendra"
    }

A frontend expecting `name` may break.

### Non-Breaking Change

Adding an optional field:

    {
      "name": "Lokendra",
      "age": 25
    }

Existing clients can continue using `name`.

---

# 76. API Documentation Tools

Common tools include:

- Swagger UI
- OpenAPI
- Postman
- Insomnia

OpenAPI can describe:

- Endpoints
- Parameters
- Request bodies
- Responses
- Authentication
- Schemas

This can also help generate client/server tooling.

---

# 77. API Monitoring

Production APIs should be monitored.

Important metrics:

- Request count
- Response time
- Error rate
- Status code distribution
- CPU usage
- Memory usage
- Database latency
- External API latency

Example:

    GET /api/products

    Average response time: 120 ms
    Error rate: 0.5%

This helps identify production problems.

---

# 78. API Logging

Useful information to log:

    HTTP Method
    URL
    Status Code
    Response Time
    Request ID
    User ID where appropriate
    Error Information

Example:

    GET /api/products
    status=200
    duration=85ms
    requestId=abc123

Avoid logging sensitive information such as:

- Passwords
- Access tokens
- Secret keys
- Sensitive personal data

---

# 79. API Request ID

A request ID uniquely identifies a request.

Example:

    Request-ID: abc123

Flow:

    Client
      ↓
    Request ID: abc123
      ↓
    API Server
      ↓
    Service
      ↓
    Database

If something fails, logs containing `abc123` can help trace the request through different services.

This becomes especially useful in distributed systems.

---

# 80. API Design Mental Model

When designing an API, think:

    1. What is the resource?
    2. What operation is required?
    3. Which HTTP method should be used?
    4. What should the URL look like?
    5. What input is required?
    6. How will authentication work?
    7. How will authorization work?
    8. How will validation work?
    9. What database operation is required?
    10. What response should be returned?
    11. Which status code should be returned?
    12. How should errors be handled?
    13. Does the endpoint need pagination?
    14. Does it need caching?
    15. Does it need rate limiting?
    16. How will it be documented and monitored?

---

# 81. Complete API Request Flow

A production request might look like:

    Client
      ↓
    HTTPS
      ↓
    Load Balancer
      ↓
    API Gateway
      ↓
    Express Router
      ↓
    Rate Limiter
      ↓
    Authentication Middleware
      ↓
    Authorization Middleware
      ↓
    Validation Middleware
      ↓
    Controller
      ↓
    Business Logic / Service
      ↓
    Cache
      ↓
    Database
      ↓
    Service
      ↓
    Controller
      ↓
    HTTP Response
      ↓
    Client

Not every application needs every layer, but this is a useful mental model for production backend systems.

---

# 82. Interview Question: What is an API?

### Interview Answer

An API is an interface that allows different software systems to communicate with each other. In backend development, APIs expose endpoints through which clients can perform operations such as fetching, creating, updating, or deleting resources.

---

# 83. Interview Question: What is a REST API?

### Interview Answer

A REST API is an API designed according to REST principles. It typically uses resources, HTTP methods, standard status codes, and stateless communication.

---

# 84. Interview Question: Difference Between PUT and PATCH?

### Interview Answer

PUT is generally used to replace the complete resource, while PATCH is generally used to partially update a resource.

---

# 85. Interview Question: Difference Between 401 and 403?

### Interview Answer

401 means the request is not properly authenticated, while 403 means the user is authenticated but does not have permission to perform the requested operation.

Simple:

    401 → Who are you?

    403 → I know who you are, but you are not allowed.

---

# 86. Interview Question: Difference Between Path and Query Parameters?

### Interview Answer

Path parameters are generally used to identify a specific resource, while query parameters are generally used for filtering, sorting, searching, pagination, or optional parameters.

Example:

    /users/123

    123 → Path Parameter

    /users?role=admin

    role=admin → Query Parameter

---

# 87. Interview Question: What is API Versioning?

### Interview Answer

API versioning allows us to introduce changes to an API without immediately breaking existing clients. For example, we can maintain `/api/v1/users` while introducing `/api/v2/users` for breaking changes.

---

# 88. Interview Question: What is Idempotency?

### Interview Answer

An idempotent operation produces the same intended final state when the same request is repeated multiple times. PUT is a common example. This is especially important when implementing retries.

---

# 89. Interview Question: Why Do We Need API Validation?

### Interview Answer

Client-side validation can be bypassed, so the backend must always validate incoming data. This protects the application from invalid data, unexpected input, and security issues.

---

# 90. Interview Question: Why Should APIs Use Status Codes?

### Interview Answer

HTTP status codes allow clients to understand the result of a request in a standardized way. For example, 200 indicates success, 201 indicates creation, 400 indicates a bad request, 401 indicates authentication failure, 404 indicates a missing resource, and 500 indicates a server error.

---

# 91. Interview Question: How Do You Secure an API?

### Interview Answer

I would secure an API using HTTPS, authentication, authorization, input validation, rate limiting, secure token handling, proper CORS configuration, password hashing, request limits, protection against injection attacks, and safe error handling.

---

# 92. Interview Scenario: Design User APIs

Suppose we need to design APIs for users.

A good design could be:

    GET     /api/users
    GET     /api/users/:id
    POST    /api/users
    PATCH   /api/users/:id
    DELETE  /api/users/:id

For authentication:

    POST /api/auth/register
    POST /api/auth/login
    POST /api/auth/logout
    POST /api/auth/refresh

---

# 93. Interview Scenario: Design Product APIs

For an e-commerce application:

    GET    /api/products
    GET    /api/products/:id
    POST   /api/products
    PATCH  /api/products/:id
    DELETE /api/products/:id

Filtering:

    GET /api/products?category=mobile

Searching:

    GET /api/products?search=iphone

Sorting:

    GET /api/products?sort=-price

Pagination:

    GET /api/products?page=1&limit=20

---

# 94. Interview Scenario: API is Slow

Suppose:

    GET /api/products

takes 5 seconds.

I would investigate:

    1. Database query
    2. Missing database indexes
    3. Large response size
    4. Missing pagination
    5. N+1 queries
    6. External API calls
    7. Network latency
    8. Server CPU/memory
    9. Cache opportunities
    10. Database connection pool

Possible solutions:

    Add indexes
    Optimize queries
    Add pagination
    Add caching
    Reduce response size
    Avoid unnecessary external calls

---

# 95. Interview Scenario: API Receives Too Much Traffic

Suppose:

    1,000 requests/sec
          ↓
    API Server

Possible architecture:

    Client
      ↓
    Load Balancer
      ↓
    ┌────────┬────────┬────────┐
    ↓        ↓        ↓
    API 1   API 2   API 3
      ↓
    Redis Cache
      ↓
    Database

Additional solutions:

- Horizontal scaling
- Database read replicas
- Caching
- Queue/background processing
- CDN
- Rate limiting
- Database optimization

---

# 96. API Best Practices

Follow these principles:

1. Use meaningful resource-based URLs.
2. Use HTTP methods correctly.
3. Return appropriate HTTP status codes.
4. Validate all incoming data.
5. Authenticate protected endpoints.
6. Authorize sensitive operations.
7. Use HTTPS.
8. Use consistent response formats.
9. Implement proper error handling.
10. Use pagination for large datasets.
11. Avoid exposing sensitive fields.
12. Implement rate limiting where needed.
13. Document APIs.
14. Monitor production APIs.
15. Log requests appropriately.
16. Use API versioning for breaking changes.
17. Keep controllers thin.
18. Keep business logic in services.
19. Optimize database queries.
20. Add caching where it provides value.

---

# 97. API Quick Revision

```text
API
↓
Allows systems to communicate

Endpoint
↓
HTTP Method + URL

GET
↓
Read

POST
↓
Create / Submit

PUT
↓
Replace

PATCH
↓
Partial Update

DELETE
↓
Delete

Path Parameter
↓
Identify resource

Query Parameter
↓
Filter / Search / Sort / Pagination

Request Body
↓
Data sent to server

Headers
↓
Request/response metadata

200
↓
Success

201
↓
Created

204
↓
Success with no body

400
↓
Bad Request

401
↓
Not Authenticated

403
↓
Not Allowed

404
↓
Not Found

409
↓
Conflict

429
↓
Too Many Requests

500
↓
Server Error

REST
↓
Resource-oriented API architecture

Authentication
↓
Who are you?

Authorization
↓
What are you allowed to do?

Validation
↓
Is the input valid?

Rate Limiting
↓
Control request frequency

Pagination
↓
Return data in smaller chunks

Caching
↓
Avoid repeated expensive work

Versioning
↓
Support API evolution

Webhook
↓
Server notifies another system about an event