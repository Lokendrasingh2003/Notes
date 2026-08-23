# Backend Development — Routing

> **Goal:** Understand how backend applications receive HTTP requests and decide which handler should process each request.

---

# 1. What is Routing?

Routing is the process of **mapping an incoming HTTP request to a specific piece of backend code**.

In simple words:

> **Routing decides what code should run when a particular HTTP method and URL are requested.**

For example:

```text
GET /users
```

might be mapped to code that returns all users.

```text
GET /users/101
```

might be mapped to code that returns one specific user.

```text
POST /users
```

might be mapped to code that creates a new user.

So:

```text
HTTP Request
     ↓
   Router
     ↓
Matching Route
     ↓
Handler
     ↓
Response
```

---

# 2. Why Do We Need Routing?

Imagine your backend has hundreds of APIs:

```text
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

POST   /orders
GET    /orders
GET    /orders/:id

POST   /login
POST   /logout
POST   /register
```

The server needs to know:

> Which code should execute for each request?

Routing provides that mapping.

---

# 3. Basic Routing Concept

Think of routing like a receptionist in an office.

```text
Visitor
   ↓
Receptionist
   ↓
"What do you need?"
   ↓
 ┌───────────────┬───────────────┐
 ▼               ▼               ▼
Accounts       Support         Sales
```

Similarly:

```text
HTTP Request
      ↓
    Router
      ↓
 ┌────┼────────────┐
 ▼    ▼            ▼
Users Products    Orders
```

The router looks at the request and sends it to the appropriate handler.

---

# 4. What Does a Route Usually Contain?

A route is generally defined using:

```text
HTTP Method + Path + Handler
```

For example:

```js
app.get("/users", handler);
```

Here:

```text
GET      → HTTP Method
/users   → Path
handler  → Function that handles request
```

So we can think of a route as:

```text
GET /users
   ↓
User Handler
```

---

# 5. Basic Express Routing

Let's create a simple Node.js backend.

```js
const express = require("express");

const app = express();

app.get("/users", (req, res) => {
  res.json({
    message: "Get all users"
  });
});

app.post("/users", (req, res) => {
  res.json({
    message: "Create a user"
  });
});

app.listen(5000, () => {
  console.log("Server running on port 5000");
});
```

Now we have two routes:

```text
GET  /users
POST /users
```

Even though the path is the same, they are different routes because the HTTP methods are different.

---

# 6. HTTP Method + Path Together Identify a Route

This is very important.

These are different routes:

```text
GET    /users
POST   /users
PUT    /users
PATCH  /users
DELETE /users
```

The path alone does not determine the route.

The combination matters:

```text
HTTP Method + Path
```

For example:

```text
GET /users
```

means something different from:

```text
POST /users
```

---

# 7. Static Routes

A static route has a fixed path.

Example:

```js
app.get("/users", (req, res) => {
  res.send("All users");
});
```

Another:

```js
app.get("/products", (req, res) => {
  res.send("All products");
});
```

The path is fixed:

```text
/users
/products
/orders
```

---

# 8. Dynamic Routes

Sometimes we don't know the exact value in the URL.

For example:

```text
/users/101
/users/102
/users/103
```

We don't want to create a separate route for every user.

Instead, we use a **route parameter**.

```js
app.get("/users/:id", (req, res) => {
  const id = req.params.id;

  res.json({
    userId: id
  });
});
```

Now:

```text
GET /users/101
```

gives:

```js
req.params.id
```

as:

```text
101
```

And:

```text
GET /users/500
```

gives:

```text
500
```

---

# 9. Route Parameters

Route parameters are dynamic values inside the URL path.

Example:

```text
/users/:id
```

Here:

```text
:id
```

is a route parameter.

Example request:

```text
GET /users/101
```

Then:

```js
req.params.id
```

is:

```text
101
```

---

# 10. Multiple Route Parameters

You can have multiple parameters.

Example:

```text
/products/:productId/reviews/:reviewId
```

Request:

```text
GET /products/101/reviews/55
```

Express:

```js
app.get(
  "/products/:productId/reviews/:reviewId",
  (req, res) => {
    console.log(req.params);
    
    res.json(req.params);
  }
);
```

Result:

```js
{
  productId: "101",
  reviewId: "55"
}
```

---

# 11. Query Parameters vs Route Parameters

This is an important distinction.

### Route Parameter

Used to identify a specific resource.

```text
GET /users/101
```

Here:

```text
101
```

is a route parameter.

Usually:

```js
req.params.id
```

---

### Query Parameter

Usually used for filtering, searching, sorting, pagination, etc.

```text
GET /users?page=2&limit=20
```

Here:

```text
page=2
limit=20
```

are query parameters.

In Express:

```js
req.query
```

Example:

```js
app.get("/users", (req, res) => {
  console.log(req.query);

  res.json(req.query);
});
```

Request:

```text
GET /users?page=2&limit=20
```

Result:

```js
{
  page: "2",
  limit: "20"
}
```

---

# 12. Simple Difference

Remember:

```text
Route Parameter
→ Identifies a resource

Query Parameter
→ Modifies the request
```

Example:

```text
GET /products/101
             ↑
        Route Parameter
```

```text
GET /products?category=mobile&sort=price
                ↑
          Query Parameters
```

---

# 13. Route Handler

The function that executes when a route matches the request is called a **route handler**.

Example:

```js
app.get("/users", (req, res) => {
  res.json({
    message: "Users fetched"
  });
});
```

This function:

```js
(req, res) => {
  res.json({
    message: "Users fetched"
  });
}
```

is the handler.

It receives:

```text
req → Request
res → Response
```

---

# 14. Routing Flow

When a request arrives:

```text
Client
  ↓
HTTP Request
  ↓
Server
  ↓
Router
  ↓
Match Method + Path
  ↓
Route Handler
  ↓
Response
```

Example:

```text
GET /users/101
```

The router checks:

```text
Method = GET
Path   = /users/101
```

It matches:

```js
app.get("/users/:id", handler);
```

Then the handler executes.

---

# 15. Route Matching

Suppose we have:

```js
app.get("/users", handler1);

app.get("/users/:id", handler2);

app.post("/users", handler3);
```

Requests:

```text
GET /users
```

matches:

```text
handler1
```

---

```text
GET /users/101
```

matches:

```text
handler2
```

---

```text
POST /users
```

matches:

```text
handler3
```

This demonstrates why both the HTTP method and path matter.

---

# 16. Route Ordering

Route order can matter, especially when patterns overlap.

For example:

```js
app.get("/users/:id", (req, res) => {
  res.send("User");
});

app.get("/users/me", (req, res) => {
  res.send("Current user");
});
```

Depending on the router and route matching behavior, the dynamic route can match:

```text
/users/me
```

as:

```text
id = "me"
```

A safer ordering is:

```js
app.get("/users/me", (req, res) => {
  res.send("Current user");
});

app.get("/users/:id", (req, res) => {
  res.send("User");
});
```

General principle:

> Put more specific routes before broader dynamic routes when their patterns can overlap.

---

# 17. Route Prefixes

In a real application, APIs are often grouped under a common prefix.

For example:

```text
/api/users
/api/products
/api/orders
```

Instead of:

```text
/users
/products
/orders
```

A common API structure might be:

```text
/api/v1/users
/api/v1/products
/api/v1/orders
```

The `v1` represents an API version.

Versioning becomes useful when you need to introduce breaking changes without immediately breaking existing clients.

---

# 18. Express Router

As applications become larger, putting every route inside `server.js` becomes difficult.

Instead, we can use `express.Router()`.

Example project:

```text
backend/
│
├── server.js
│
├── routes/
│   ├── user.routes.js
│   ├── product.routes.js
│   └── order.routes.js
│
└── controllers/
```

---

# 19. User Router

Create:

```text
routes/user.routes.js
```

```js
const express = require("express");

const router = express.Router();

router.get("/", (req, res) => {
  res.json({
    message: "Get users"
  });
});

router.get("/:id", (req, res) => {
  res.json({
    message: "Get user",
    id: req.params.id
  });
});

router.post("/", (req, res) => {
  res.json({
    message: "Create user"
  });
});

module.exports = router;
```

---

# 20. Connecting the Router

In `server.js`:

```js
const express = require("express");
const userRoutes = require("./routes/user.routes");

const app = express();

app.use(express.json());

app.use("/api/users", userRoutes);

app.listen(5000, () => {
  console.log("Server running on port 5000");
});
```

Now the routes become:

```text
GET  /api/users
GET  /api/users/:id
POST /api/users
```

This is much cleaner.

---

# 21. Why Use Routers?

Without routers:

```text
server.js
│
├── users
├── products
├── orders
├── auth
├── payments
├── reviews
└── notifications
```

The file can become huge.

With routers:

```text
routes/
│
├── user.routes.js
├── product.routes.js
├── order.routes.js
├── auth.routes.js
├── payment.routes.js
└── review.routes.js
```

Benefits:

* Better organization
* Easier maintenance
* Easier testing
* Separation of concerns
* Easier team development
* Cleaner application structure

---

# 22. Routing vs Controller

This distinction is important.

### Routing

Decides:

> **Which handler should receive this request?**

### Controller

Usually handles the HTTP-level part of the request.

For example:

```text
Router
   ↓
Controller
```

Example:

```js
router.get("/:id", getUser);
```

Here:

```text
router.get()
```

defines the route.

And:

```js
getUser
```

is the controller/handler function.

---

# 23. Routing vs Business Logic

A common mistake is putting everything inside the route.

Bad structure:

```js
app.post("/users", async (req, res) => {
  // validation

  // database query

  // password hashing

  // email sending

  // business rules

  // response
});
```

This becomes difficult to maintain.

A better architecture is:

```text
Route
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

For example:

```js
router.post("/", createUser);
```

Controller:

```js
const createUser = async (req, res) => {
  const user = await userService.createUser(req.body);

  res.status(201).json(user);
};
```

Service:

```js
const createUser = async (data) => {
  // Business logic
  // Call repository
};
```

We will study this architecture deeply later.

---

# 24. RESTful Route Design

Good routing is not just about making routes work.

We should design routes consistently.

For example, for users:

```text
GET    /users
GET    /users/:id
POST   /users
PUT    /users/:id
PATCH  /users/:id
DELETE /users/:id
```

This represents CRUD operations clearly.

Avoid unnecessarily action-based URLs such as:

```text
GET /getUsers
POST /createUser
POST /deleteUser
```

In REST-style APIs, the HTTP method communicates the operation.

Better:

```text
GET    /users
POST   /users
DELETE /users/:id
```

---

# 25. Nested Routes

Sometimes resources have relationships.

Example:

```text
GET /users/101/orders
```

Meaning:

> Get orders belonging to user 101.

Another:

```text
GET /products/101/reviews
```

Meaning:

> Get reviews for product 101.

However, don't create deeply nested URLs unnecessarily.

For example:

```text
/users/101/orders/500/products/20/reviews
```

can become difficult to understand and maintain.

Keep resource relationships reasonably simple.

---

# 26. Route Naming Best Practices

Prefer resource-oriented names:

```text
/users
/products
/orders
/payments
/reviews
```

Generally use nouns rather than verbs:

```text
Good:
GET /users

Avoid:
GET /getUsers
```

Use plural resource names consistently:

```text
/users
/products
/orders
```

Use route parameters for specific resources:

```text
/users/101
/products/500
/orders/900
```

---

# 27. Route Versioning

Large APIs may use versioning.

Example:

```text
/api/v1/users
/api/v1/products
```

Later:

```text
/api/v2/users
```

Why?

Suppose version 1 returns:

```json
{
  "name": "Lokendra"
}
```

But version 2 needs:

```json
{
  "firstName": "Lokendra",
  "lastName": "Singh"
}
```

Changing the existing API might break old clients.

Versioning allows both versions to coexist while clients migrate.

---

# 28. Route Constraints and Validation

A route parameter doesn't automatically mean the value is valid.

Example:

```text
GET /users/hello
```

If the application expects a numeric ID, the backend should validate it.

For example:

```js
app.get("/users/:id", (req, res) => {
  const id = Number(req.params.id);

  if (Number.isNaN(id)) {
    return res.status(400).json({
      message: "Invalid user ID"
    });
  }

  res.json({
    id
  });
});
```

Routing identifies the endpoint.

Validation checks whether the provided data is acceptable.

These are separate responsibilities.

---

# 29. Route + Middleware

A route can have middleware before the handler.

Example:

```js
router.get(
  "/profile",
  authenticate,
  getProfile
);
```

Flow:

```text
GET /profile
     ↓
authenticate
     ↓
getProfile
     ↓
Response
```

If authentication fails:

```text
GET /profile
     ↓
authenticate
     ↓
401 Unauthorized
```

The controller may never execute.

We will study middleware separately.

---

# 30. Complete Request Flow

A more realistic backend request flow looks like:

```text
Client
  ↓
HTTP Request
  ↓
Server
  ↓
Router
  ↓
Middleware
  ↓
Authentication
  ↓
Validation
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
```

This is one of the most important mental models for backend development.

---

# 31. Real-World Example

Suppose an e-commerce application receives:

```http
GET /api/products/101
```

The routing system finds:

```js
router.get("/:id", getProduct);
```

Then:

```text
Request
  ↓
GET /api/products/101
  ↓
Product Router
  ↓
getProduct Controller
  ↓
Product Service
  ↓
Database
  ↓
Product Data
  ↓
Controller
  ↓
JSON Response
```

Response:

```json
{
  "id": 101,
  "name": "Laptop",
  "price": 50000
}
```

The router's job is mainly to **connect the request to the correct application logic**.

---

# 32. Routing in Other Backend Technologies

Routing is not specific to Node.js.

The concept exists in almost every backend framework.

```text
Node.js      → Express Router
Java         → Spring MVC
Python       → Django / FastAPI routes
C#           → ASP.NET Core routing
Go           → net/http / Gin / Fiber
```

The syntax changes, but the concept remains:

```text
HTTP Method + Path
        ↓
     Handler
```

---

# 33. Common Beginner Mistakes

### Mistake 1: Confusing route and URL

A route is a rule defined by the server.

Example:

```js
app.get("/users/:id", handler);
```

A URL is an actual address/request:

```text
/users/101
```

---

### Mistake 2: Ignoring HTTP methods

These are different:

```text
GET /users
POST /users
```

The path is the same, but the method changes the route behavior.

---

### Mistake 3: Putting business logic inside routes

Avoid huge route handlers.

Prefer:

```text
Router
  ↓
Controller
  ↓
Service
  ↓
Repository
```

---

### Mistake 4: Using query parameters for resource identity

Usually:

```text
/users/101
```

is clearer for identifying one user than:

```text
/users?id=101
```

The latter can be valid in some APIs, but route parameters are commonly used when the identifier is part of the resource path.

---

### Mistake 5: Creating too many nested routes

Avoid unnecessarily complicated paths.

Keep API design simple and predictable.

---

# 34. Interview-Friendly Answers

## Q1. What is routing?

> Routing is the process of mapping an incoming HTTP request, based on its method and path, to the appropriate handler or controller in the backend application.

### Short answer

> Routing decides which backend code should handle a particular HTTP request.

---

## Q2. What is the difference between routing and a controller?

> Routing determines which handler should process a request, while the controller generally handles the HTTP-level logic after the route has matched. In a layered architecture, the controller may then call a service containing the business logic.

---

## Q3. What are route parameters?

> Route parameters are dynamic values embedded in the URL path and are commonly used to identify a specific resource.

Example:

```text
/users/:id
```

For:

```text
/users/101
```

`id` is `101`.

---

## Q4. What is the difference between route parameters and query parameters?

> Route parameters are typically used to identify a specific resource, while query parameters are commonly used for filtering, searching, sorting, and pagination.

Example:

```text
/users/101
       ↑
Route parameter
```

```text
/users?page=2&limit=20
       ↑
Query parameters
```

---

## Q5. Why do we use Express Router?

> Express Router allows us to organize routes into separate modules based on resources or features. This improves maintainability, readability, testing, and separation of concerns.

---

## Q6. Why shouldn't we put business logic directly inside routes?

> Putting business logic directly inside routes creates tightly coupled and difficult-to-maintain code. A better approach is to separate routing, controllers, services, and data-access logic so each layer has a clear responsibility.

---

# 35. Quick Revision

Remember:

```text
ROUTING

HTTP Request
     ↓
Method + Path
     ↓
Router
     ↓
Matching Route
     ↓
Handler / Controller
     ↓
Service
     ↓
Database
     ↓
Response
```

### Common routes

```text
GET    /users
GET    /users/:id
POST   /users
PUT    /users/:id
PATCH  /users/:id
DELETE /users/:id
```

### Parameters

```text
/users/:id
       ↑
Route Parameter
```

```text
/users?page=2&limit=20
       ↑
Query Parameters
```

### Architecture

```text
Route
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

---

# 36. What You Should Know Before Moving On

* [ ] What routing is
* [ ] Why routing is needed
* [ ] HTTP method + path
* [ ] Static routes
* [ ] Dynamic routes
* [ ] Route parameters
* [ ] Query parameters
* [ ] Route handlers
* [ ] Route matching
* [ ] Route ordering
* [ ] Express Router
* [ ] Route prefixes
* [ ] API versioning
* [ ] Nested routes
* [ ] Routing vs controller
* [ ] Routing vs business logic
* [ ] Basic RESTful route design

---

# One-Line Interview Summary

> **Routing is the mechanism that maps an incoming HTTP method and URL path to the appropriate handler or controller in a backend application.**
