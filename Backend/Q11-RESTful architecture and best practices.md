# Backend Development — RESTful Architecture and Best Practices

## 1. What is REST?

**REST stands for Representational State Transfer.**

REST is an architectural style for designing networked applications, especially HTTP APIs.

The main idea is:

> **Design APIs around resources and use standard HTTP methods to operate on those resources.**

For example, instead of designing APIs around actions:

    /getUsers
    /createUser
    /deleteUser

A RESTful API focuses on the resource:

    /users

and uses HTTP methods to describe the operation:

    GET    /users
    POST   /users
    DELETE /users/:id

---

# 2. What is a Resource?

A **resource** is something that the API manages or exposes.

Examples:

    Users
    Products
    Orders
    Payments
    Posts
    Comments

Each resource usually has a URL.

Examples:

    /users
    /products
    /orders
    /posts

A specific resource can be identified using an ID:

    /users/101
    /products/501
    /orders/9001

Mental model:

    Resource
    → Something the API manages

    URL
    → Identifies the resource

    HTTP Method
    → Defines what operation to perform

---

# 3. RESTful API Example

Suppose we have a User resource.

A RESTful API could be:

    GET    /users
    → Get all users

    GET    /users/101
    → Get user 101

    POST   /users
    → Create a user

    PUT    /users/101
    → Replace user 101

    PATCH  /users/101
    → Partially update user 101

    DELETE /users/101
    → Delete user 101

Notice that the URL remains:

    /users

The HTTP method changes depending on the operation.

---

# 4. REST and HTTP Methods

REST APIs use standard HTTP methods.

    GET
    → Read resource

    POST
    → Create resource

    PUT
    → Replace resource

    PATCH
    → Partially update resource

    DELETE
    → Delete resource

Mental model:

    GET
    → Give me the resource

    POST
    → Create a new resource

    PUT
    → Replace this resource

    PATCH
    → Modify part of this resource

    DELETE
    → Remove this resource

---

# 5. Resource-Oriented URLs

A good REST API uses URLs to represent resources rather than actions.

Avoid:

    /getUsers
    /createUser
    /updateUser
    /deleteUser

Prefer:

    GET    /users
    POST   /users
    PATCH  /users/:id
    DELETE /users/:id

The HTTP method already communicates the action.

This keeps the API consistent and easier to understand.

---

# 6. Use Nouns, Not Verbs

Resource URLs should generally use nouns.

Avoid:

    /getProducts
    /createOrder
    /deleteUser
    /updateProfile

Prefer:

    /products
    /orders
    /users
    /profile

For example:

    POST /orders

already means:

    Create an order

There is usually no need for:

    POST /createOrder

---

# 7. Collections and Individual Resources

REST APIs commonly distinguish between a collection and an individual resource.

Collection:

    /users

Individual resource:

    /users/101

Examples:

    GET /users
    → Get users

    GET /users/101
    → Get user 101

    DELETE /users/101
    → Delete user 101

Mental model:

    /users
    → Collection

    /users/101
    → Specific resource

---

# 8. Nested Resources

When one resource belongs to another resource, nested URLs can be useful.

Example:

    /users/101/orders

This can mean:

    Orders belonging to user 101

Another example:

    /posts/10/comments

Meaning:

    Comments belonging to post 10

Example:

    GET /users/101/orders
    → Get orders for user 101

    GET /posts/10/comments
    → Get comments for post 10

However, avoid excessive nesting.

Avoid URLs like:

    /companies/1/departments/2/employees/3/projects/4/tasks/5

Deep nesting can make APIs difficult to understand and maintain.

A reasonable guideline is to keep nesting shallow.

---

# 9. HTTP Status Codes

A RESTful API should use HTTP status codes correctly.

Common success codes:

    200 OK
    → Request succeeded

    201 Created
    → Resource successfully created

    204 No Content
    → Request succeeded without a response body

Common client errors:

    400 Bad Request
    → Invalid request

    401 Unauthorized
    → Authentication is required or failed

    403 Forbidden
    → User is authenticated but not allowed

    404 Not Found
    → Resource does not exist

    409 Conflict
    → Request conflicts with current resource state

Common server error:

    500 Internal Server Error
    → Unexpected server-side error

---

# 10. Example of Status Codes

Create:

    POST /users

Success:

    201 Created

Get:

    GET /users/101

Success:

    200 OK

Resource doesn't exist:

    GET /users/999

Response:

    404 Not Found

Delete:

    DELETE /users/101

Success without response body:

    204 No Content

---

# 11. Statelessness

One of the important REST constraints is **statelessness**.

Stateless means:

> **Each request should contain the information necessary for the server to understand and process that request.**

The server should not depend on remembering the state of a previous request in order to understand the next request.

Example:

    Request 1
    GET /users/101

    Request 2
    GET /orders

Request 2 should contain the necessary authentication/context information rather than relying on the server remembering Request 1.

With token-based authentication:

    Authorization: Bearer <token>

the client sends the token with each request.

Mental model:

    Request
       ↓
    Contains required context
       ↓
    Server processes request
       ↓
    Response

---

# 12. Statelessness and Authentication

A common REST API pattern is:

    Client
       ↓
    Authorization: Bearer token
       ↓
    API
       ↓
    Verify token
       ↓
    Process request

The server doesn't need to remember a previous request just to authenticate the current one.

This makes horizontal scaling easier.

For example:

    Client
       ↓
    Load Balancer
       ↓
    Server A
    Server B
    Server C

A request can go to any server as long as each server can validate the authentication information.

---

# 13. Representations

REST stands for **Representational State Transfer**.

The client does not necessarily receive the internal database object directly.

Instead, the server sends a representation of the resource.

For example:

    Database User

may contain:

    id
    name
    email
    passwordHash
    createdAt

The API response might expose:

    {
      "id": 101,
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

The API should not expose sensitive internal fields such as:

    passwordHash

The representation sent to the client should be intentionally designed.

---

# 14. JSON in REST APIs

JSON is commonly used as the representation format for REST APIs.

Example:

    {
      "id": 101,
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

Request:

    POST /users

    {
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

Response:

    201 Created

    {
      "id": 101,
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

---

# 15. Content-Type

The client and server should communicate the representation format using HTTP headers.

For JSON:

    Content-Type: application/json

Example:

    POST /users
    Content-Type: application/json

    {
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

The server can also indicate the response format:

    Content-Type: application/json

---

# 16. Query Parameters

Query parameters are useful for operations on collections.

They are commonly used for:

    Filtering
    Sorting
    Searching
    Pagination

Example:

    GET /products?category=mobile

Filtering:

    GET /users?role=admin

Sorting:

    GET /products?sort=price

Pagination:

    GET /products?page=2&limit=20

Searching:

    GET /products?search=iphone

The URL still represents the same resource collection:

    /products

The query parameters modify how the collection is retrieved.

---

# 17. Filtering

Filtering allows clients to retrieve only resources matching specific conditions.

Example:

    GET /products?category=electronics

Another example:

    GET /orders?status=pending

The backend processes these parameters and queries the database accordingly.

Filtering is especially important for large collections.

---

# 18. Sorting

Sorting allows clients to specify the order of returned resources.

Example:

    GET /products?sort=price

Another possible convention:

    GET /products?sort=-price

where:

    price
    → Ascending

    -price
    → Descending

The exact API convention should be documented and used consistently.

---

# 19. Pagination

APIs should avoid returning huge collections in a single response.

Instead:

    GET /users?page=2&limit=20

The response could contain:

    {
      "data": [
        ...
      ],
      "page": 2,
      "limit": 20,
      "total": 150
    }

Pagination helps reduce:

    Response size
    Database load
    Server memory usage
    Network usage

Common pagination approaches include:

    Offset-based pagination
    Cursor-based pagination

---

# 20. Offset vs Cursor Pagination

### Offset Pagination

Example:

    GET /users?page=2&limit=20

or:

    GET /users?offset=20&limit=20

It is simple and easy to implement.

However, for very large or frequently changing datasets, it can become less efficient or produce inconsistent results when records are inserted or deleted between requests.

### Cursor Pagination

Example:

    GET /users?limit=20&after=abc123

The cursor represents a position in the dataset.

Cursor pagination is often useful for:

    Large datasets
    Infinite scrolling
    Frequently changing data
    High-scale APIs

---

# 21. PUT vs PATCH in REST

This is a common interview question.

### PUT

Generally represents replacement of a resource representation.

Example:

    PUT /users/101

    {
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

### PATCH

Generally represents partial modification.

Example:

    PATCH /users/101

    {
      "name": "Lokendra Singh"
    }

Mental model:

    PUT
    → Replace

    PATCH
    → Modify part

---

# 22. Idempotency

An operation is **idempotent** when repeating the same request produces the same intended resource state.

Generally:

    GET
    → Idempotent

    PUT
    → Idempotent

    DELETE
    → Idempotent

    POST
    → Usually not idempotent

Example:

    PUT /users/101

    {
      "name": "Lokendra"
    }

Sending the same request multiple times should leave the resource with the same name.

But:

    POST /orders

could create multiple orders if sent multiple times.

For important operations such as payments and order creation, APIs can use idempotency keys.

Example:

    Idempotency-Key: abc-123

The server can use the key to prevent accidental duplicate processing.

---

# 23. API Versioning

APIs evolve over time.

A change that breaks existing clients should be handled carefully.

One common approach is URL versioning:

    /api/v1/users

    /api/v2/users

Example:

    GET /api/v1/users

Later:

    GET /api/v2/users

Other versioning approaches include:

    Header-based versioning
    Media-type versioning

The important thing is to have a consistent versioning strategy.

---

# 24. Backward Compatibility

When changing an API, avoid unnecessarily breaking existing clients.

For example, if version 1 returns:

    {
      "id": 101,
      "name": "Lokendra"
    }

Changing:

    name

to:

    fullName

could break clients that depend on `name`.

Possible approaches:

    Add new fields
    Deprecate old fields
    Introduce a new API version
    Maintain compatibility during migration

API design should consider existing consumers.

---

# 25. Consistent Response Structure

REST APIs should have predictable responses.

Example:

    {
      "data": {
        "id": 101,
        "name": "Lokendra"
      }
    }

For collections:

    {
      "data": [
        {
          "id": 101,
          "name": "Lokendra"
        },
        {
          "id": 102,
          "name": "Rahul"
        }
      ]
    }

The exact response structure is a design choice.

The important principle is:

> **Be consistent across endpoints.**

---

# 26. Consistent Error Responses

Errors should also follow a predictable structure.

Example:

    {
      "error": {
        "code": "USER_NOT_FOUND",
        "message": "User not found"
      }
    }

Another example:

    {
      "error": {
        "code": "VALIDATION_ERROR",
        "message": "Invalid request",
        "details": [
          {
            "field": "email",
            "message": "Invalid email"
          }
        ]
      }
    }

Consistency makes APIs easier to consume and debug.

---

# 27. RESTful URL Naming

Good URLs should be:

    Simple
    Consistent
    Resource-oriented
    Predictable

Prefer:

    /users
    /users/101
    /users/101/orders

Avoid inconsistent naming such as:

    /getUsers
    /userList
    /fetch-user
    /deleteUserById

Choose one convention and follow it consistently.

---

# 28. Singular vs Plural Resource Names

A common REST convention is to use plural nouns for collections.

Prefer:

    /users
    /products
    /orders

Then:

    /users/101
    /products/501
    /orders/9001

This makes the relationship between collection and individual resource clear.

---

# 29. Avoid Deeply Nested URLs

Nested resources can be useful:

    /users/101/orders

But too much nesting becomes difficult to maintain.

Avoid:

    /companies/1/departments/2/employees/3/orders/4/items/5

Instead, consider flatter resource URLs where appropriate:

    /orders/4
    /order-items/5

The best choice depends on the relationship and API requirements.

---

# 30. Authentication and Authorization

REST APIs should protect sensitive resources.

Authentication:

    "Who is making the request?"

Authorization:

    "Is this user allowed to perform this operation?"

Example:

    DELETE /users/101

Flow:

    Request
       ↓
    Authentication
       ↓
    User identified
       ↓
    Authorization
       ↓
    Permission checked
       ↓
    Delete operation

Never rely on the frontend alone for authorization.

---

# 31. HTTPS

REST APIs should use HTTPS in production.

HTTPS provides encrypted communication between the client and server.

Without HTTPS, sensitive information could be exposed during transmission.

Protect:

    Access tokens
    Passwords
    Personal information
    Payment information
    Other sensitive data

Mental model:

    Client
       ↓
    HTTPS
       ↓
    API

---

# 32. Input Validation

Never trust client input.

Validate:

    Request body
    Query parameters
    Route parameters
    Headers where appropriate

Example:

    POST /users

    {
      "email": "invalid"
    }

The backend should validate the email before processing it.

Validation helps prevent:

    Invalid data
    Unexpected application behavior
    Database errors
    Security issues

---

# 33. Rate Limiting

Public APIs can receive excessive requests.

Example:

    Client
       ↓
    10,000 requests
       ↓
    API

Rate limiting controls how many requests a client can make within a period.

Example concept:

    100 requests / minute

If the limit is exceeded, the API may respond with:

    429 Too Many Requests

Rate limiting helps protect:

    Server resources
    Database
    External APIs
    Authentication endpoints

---

# 34. Caching

Some GET responses can be cached to reduce unnecessary processing.

Example:

    GET /products

If the data doesn't change frequently, a cache can reduce database queries.

Possible caching layers:

    Browser
    CDN
    Reverse Proxy
    Redis
    Application Cache

HTTP also provides caching mechanisms through headers such as:

    Cache-Control
    ETag
    Last-Modified

Caching strategy should depend on how frequently the resource changes and how fresh the data must be.

---

# 35. ETag and Conditional Requests

An API can use an ETag to identify a particular representation of a resource.

Example:

    ETag: "abc123"

The client can later send:

    If-None-Match: "abc123"

If the resource hasn't changed, the server can respond:

    304 Not Modified

This can reduce unnecessary data transfer.

---

# 36. Security Best Practices

Important REST API security practices include:

    Use HTTPS
    Validate input
    Authenticate users
    Authorize operations
    Rate limit sensitive endpoints
    Avoid exposing sensitive fields
    Use secure token handling
    Apply appropriate CORS policies
    Protect against injection attacks
    Log security-relevant events
    Keep dependencies updated

Never return sensitive fields unnecessarily.

For example, avoid returning:

    passwordHash
    secretKey
    internal credentials

in normal API responses.

---

# 37. CORS

CORS stands for **Cross-Origin Resource Sharing**.

It controls whether a browser-based application from one origin can access resources from another origin.

Example:

    Frontend
    https://example.com

    API
    https://api.example.com

The API needs an appropriate CORS configuration if browser requests cross origins.

CORS is a browser security mechanism.

It is not a replacement for authentication or authorization.

---

# 38. REST and Stateless Servers

Stateless APIs work well with horizontal scaling.

Example:

    Client
       ↓
    Load Balancer
       ↓
    Server A
    Server B
    Server C

Any request can be routed to any server as long as the required request state is available through the request or shared infrastructure.

This makes scaling easier.

For example:

    1 Server
       ↓
    Traffic increases
       ↓
    3 Servers
       ↓
    Load Balancer distributes requests

---

# 39. REST and Caching

Statelessness and standard HTTP semantics also make caching easier.

For example:

    GET /products/101

If the resource can be cached safely, intermediaries such as CDNs can serve the response without contacting the application server every time.

This can improve:

    Performance
    Scalability
    Latency

However, sensitive or rapidly changing data needs careful cache-control.

---

# 40. HATEOAS

HATEOAS stands for:

    Hypermedia As The Engine Of Application State

The idea is that an API response can include links that tell the client what actions or related resources are available.

Example:

    {
      "id": 101,
      "name": "Lokendra",
      "_links": {
        "self": "/users/101",
        "orders": "/users/101/orders"
      }
    }

HATEOAS is part of the formal REST constraints, but many APIs commonly called REST APIs do not fully implement it.

For practical backend development, focus first on:

    Resources
    HTTP methods
    Statelessness
    Status codes
    Consistent API design

---

# 41. REST Constraints

The REST architectural style is commonly described using several constraints.

### 1. Client-Server

The client and server have separate responsibilities.

    Client
    → User interface

    Server
    → Data and application logic

### 2. Stateless

Each request contains the information necessary to process it.

### 3. Cacheable

Responses should indicate whether they can be cached.

### 4. Uniform Interface

Resources and interactions follow consistent conventions.

### 5. Layered System

A client may communicate through intermediaries such as:

    Client
       ↓
    CDN
       ↓
    Load Balancer
       ↓
    API Gateway
       ↓
    Application Server

The client does not necessarily need to know about every layer.

### 6. Code-On-Demand

The server may optionally send executable code to the client.

This constraint is optional.

---

# 42. RESTful Architecture Example

A typical backend architecture can look like:

    Client
       ↓
    HTTPS
       ↓
    Load Balancer
       ↓
    API Gateway
       ↓
    Middleware
       ↓
    Routes
       ↓
    Controllers
       ↓
    Services
       ↓
    Repositories
       ↓
    Database

REST mainly describes how the API communicates over the network.

The internal architecture can still use:

    Controllers
    Services
    Repositories
    Databases
    Caches
    Message Queues

REST does not require one specific internal code structure.

---

# 43. REST vs CRUD

CRUD and REST are related but different.

### CRUD

Describes operations on data:

    Create
    Read
    Update
    Delete

### REST

Describes an architectural style for designing network APIs around resources and standard interactions.

For example:

    CRUD:

    Create User
    Read User
    Update User
    Delete User

    REST API:

    POST   /users
    GET    /users/:id
    PATCH  /users/:id
    DELETE /users/:id

Mental model:

    CRUD
    → What operations are performed on data?

    REST
    → How should resources and HTTP interactions be designed?

---

# 44. REST vs RPC

RPC-style APIs focus more on actions.

Example:

    POST /createUser
    POST /sendEmail
    POST /calculatePrice
    POST /cancelOrder

REST focuses on resources:

    POST /users
    POST /emails
    POST /orders/101/cancellation

Neither approach is universally correct.

REST is often a good choice for resource-oriented APIs.

RPC can be useful when the API is naturally action-oriented or when using systems such as gRPC.

---

# 45. RESTful Best Practices

Follow these principles:

    1. Design around resources.

    2. Use nouns in URLs.

    3. Use standard HTTP methods.

    4. Use meaningful HTTP status codes.

    5. Keep APIs stateless where appropriate.

    6. Use plural resource names consistently.

    7. Use query parameters for filtering, sorting, searching, and pagination.

    8. Keep URL nesting shallow.

    9. Validate all client input.

    10. Authenticate protected endpoints.

    11. Authorize sensitive operations.

    12. Use HTTPS.

    13. Return consistent response structures.

    14. Return consistent error structures.

    15. Use pagination for large collections.

    16. Consider caching for suitable GET requests.

    17. Use rate limiting where appropriate.

    18. Protect sensitive information.

    19. Version APIs when breaking changes are necessary.

    20. Keep backward compatibility in mind.

    21. Use idempotency for critical operations when necessary.

    22. Document the API clearly.

---

# 46. Bad vs Good REST API

### Bad

    GET /getUsers

    POST /createUser

    POST /updateUser

    POST /deleteUser

    GET /getUserById/101

Problems:

    Action-based URLs
    Inconsistent HTTP methods
    Less predictable API design

### Better

    GET    /users

    POST   /users

    PATCH  /users/101

    DELETE /users/101

    GET    /users/101

Advantages:

    Resource-oriented
    Predictable
    Uses HTTP semantics
    Easier to understand
    Easier to document

---

# 47. Complete REST API Example

Suppose we are building an e-commerce API.

Resources:

    Users
    Products
    Orders

Endpoints:

    GET    /users
    GET    /users/:id
    POST   /users
    PATCH  /users/:id
    DELETE /users/:id

    GET    /products
    GET    /products/:id
    POST   /products
    PATCH  /products/:id
    DELETE /products/:id

    GET    /orders
    GET    /orders/:id
    POST   /orders
    PATCH  /orders/:id
    DELETE /orders/:id

Related resources:

    GET /users/:id/orders

    GET /orders/:id/items

Query parameters:

    GET /products?category=mobile

    GET /products?sort=price

    GET /products?page=2&limit=20

The API remains resource-oriented and predictable.

---

# 48. REST Request Lifecycle

A complete REST API request might look like:

    Client
       ↓
    HTTPS Request
       ↓
    Load Balancer
       ↓
    API Gateway
       ↓
    Authentication
       ↓
    Authorization
       ↓
    Validation
       ↓
    Routing
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
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

REST defines important conventions around the HTTP API layer, while the internal application architecture can vary.

---

# 49. Interview Questions

## Q1. What is REST?

> **REST is an architectural style for designing networked applications around resources and standard HTTP interactions.**

## Q2. What is a RESTful API?

> **A RESTful API is an API designed according to REST principles, typically using resource-oriented URLs, standard HTTP methods, stateless requests, meaningful status codes, and consistent representations.**

## Q3. What does REST stand for?

> **Representational State Transfer.**

## Q4. What is a resource in REST?

> **A resource is an entity or concept that an API exposes or manages, such as a user, product, order, or post.**

## Q5. Why should REST URLs use nouns instead of verbs?

> **Because the resource should be represented by the URL while the HTTP method communicates the operation being performed.**

## Q6. What does stateless mean in REST?

> **It means each request should contain the information necessary for the server to process it, without depending on stored state from previous requests.**

## Q7. What is the difference between PUT and PATCH?

> **PUT generally represents replacing a resource representation, while PATCH represents a partial modification.**

## Q8. What is idempotency?

> **An operation is idempotent when repeating the same request results in the same intended resource state.**

## Q9. What is the difference between REST and CRUD?

> **CRUD describes the basic data operations Create, Read, Update, and Delete, while REST is an architectural style for designing network APIs around resources and standard HTTP interactions.**

## Q10. What is HATEOAS?

> **HATEOAS is a REST constraint where representations can include hypermedia links that guide the client toward related resources or available actions.**

## Q11. Why is statelessness useful?

> **Statelessness makes APIs easier to scale horizontally because requests can generally be handled by any available server without relying on server-local session state.**

## Q12. How would you design a REST API for users?

> **I would use resource-oriented endpoints such as `GET /users`, `GET /users/:id`, `POST /users`, `PATCH /users/:id`, and `DELETE /users/:id`, along with authentication, authorization, validation, pagination, consistent status codes, and consistent error responses.**

---

# 50. Interview Scenario

### Interviewer:

> "What makes an API RESTful?"

A strong answer:

> **"A RESTful API is designed around resources and uses standard HTTP methods to interact with those resources. It follows principles such as stateless communication, a uniform interface, appropriate HTTP status codes, cacheability where applicable, and resource-oriented URLs. In practice, I would also focus on consistent responses, validation, authentication, authorization, pagination, versioning, and proper error handling."**

Example:

    GET    /users
    → Read users

    POST   /users
    → Create user

    PATCH  /users/101
    → Update user

    DELETE /users/101
    → Delete user

---

# 51. Quick Revision

    REST
    → Representational State Transfer

    REST
    → Architectural style

    Resource
    → User / Product / Order / Post

    URL
    → Identifies the resource

    GET
    → Read

    POST
    → Create

    PUT
    → Replace

    PATCH
    → Partial update

    DELETE
    → Delete

    RESTful URL
    → /users/101

    Avoid
    → /getUser/101

    Stateless
    → Each request contains required information

    200
    → OK

    201
    → Created

    204
    → No Content

    400
    → Bad Request

    401
    → Unauthorized / Authentication required

    403
    → Forbidden

    404
    → Not Found

    409
    → Conflict

    429
    → Too Many Requests

    500
    → Internal Server Error

    Query Parameters
    → Filtering
    → Sorting
    → Searching
    → Pagination

    Best Practices
    → Resource-oriented URLs
    → Nouns instead of verbs
    → Standard HTTP methods
    → Stateless design
    → Correct status codes
    → Validation
    → Authentication
    → Authorization
    → HTTPS
    → Pagination
    → Consistent responses
    → Error handling
    → Rate limiting
    → Caching
    → Versioning
    → Idempotency

---

# 52. One-Line Interview Summary

> **RESTful architecture is an approach to designing APIs around resources, using standard HTTP methods and status codes with stateless communication, consistent representations, and predictable resource-oriented URLs.**