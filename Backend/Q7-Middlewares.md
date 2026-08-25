# Backend Development — Middlewares

## 1. What is Middleware?

**Middleware is code that runs between receiving an HTTP request and sending the final response.**

In simple words:

> **Middleware sits in the request-response pipeline and can inspect, modify, reject, or pass the request to the next step.**

A simple mental model:

```text
Client
  ↓
HTTP Request
  ↓
Middleware
  ↓
Middleware
  ↓
Controller / Handler
  ↓
Service
  ↓
Response
  ↓
Client
```

Middleware is not specific to Node.js.

The same concept exists in many backend frameworks.

Examples:

```text
Node.js / Express → Middleware
Django            → Middleware
ASP.NET            → Middleware
Spring             → Filters / Interceptors / Middleware-like components
Laravel            → Middleware
```

---

# 2. Why Do We Need Middleware?

Imagine you have 100 API routes.

You want to:

* Log every request
* Authenticate users
* Check permissions
* Validate requests
* Handle CORS
* Parse JSON
* Add request IDs
* Measure response time
* Handle errors

Without middleware, you might repeat the same code in every controller.

Bad approach:

```js
app.get("/users", () => {
  // authentication

  // logging

  // validation

  // controller logic
});

app.get("/products", () => {
  // authentication

  // logging

  // validation

  // controller logic
});

app.get("/orders", () => {
  // authentication

  // logging

  // validation

  // controller logic
});
```

This creates:

```text
Duplication
↓
Harder maintenance
↓
More bugs
```

Middleware allows us to reuse these concerns.

```text
Request
  ↓
Logging Middleware
  ↓
Authentication Middleware
  ↓
Validation Middleware
  ↓
Controller
```

---

# 3. The Core Idea

The most important thing to understand is:

```text
Middleware can decide what happens next.
```

It can:

### 1. Continue

```text
Request
  ↓
Middleware
  ↓
next()
  ↓
Next middleware
```

### 2. Stop the request

```text
Request
  ↓
Middleware
  ↓
Error
  ↓
Response
```

### 3. Modify the request

```text
Request
  ↓
Middleware
  ↓
Add data
  ↓
Next middleware/controller
```

### 4. Modify the response

Middleware can also observe or modify response-related behavior before the response is completed.

---

# 4. Express Middleware Function

In Express, a middleware commonly looks like:

```js
function middleware(req, res, next) {
  // middleware logic

  next();
}
```

There are three important objects:

```text
req
res
next
```

### `req`

Contains information about the request.

Examples:

```js
req.body
req.params
req.query
req.headers
req.user
```

### `res`

Used to send the response.

Examples:

```js
res.json()
res.send()
res.status()
```

### `next`

Passes control to the next middleware or handler.

```js
next();
```

---

# 5. Basic Middleware Example

```js
function logger(req, res, next) {
  console.log(req.method, req.url);

  next();
}
```

Register it:

```js
app.use(logger);
```

Now every matching request goes through the middleware.

Request:

```http
GET /users
```

Flow:

```text
GET /users
    ↓
logger()
    ↓
next()
    ↓
/users handler
```

---

# 6. What Happens If We Don't Call `next()`?

Consider:

```js
function middleware(req, res, next) {
  console.log("Middleware");

  // next() missing
}
```

The request may stop here because nothing continues the pipeline and no response is sent.

Flow:

```text
Request
   ↓
Middleware
   ↓
  STOP
```

The client may keep waiting until a timeout occurs.

Therefore, a middleware normally needs to do one of two things:

```text
Continue:
next()

OR

End the request:
res.status(...).json(...)
```

---

# 7. Middleware Can Send a Response

Example:

```js
function checkMaintenance(req, res, next) {
  const maintenance = true;

  if (maintenance) {
    return res.status(503).json({
      message: "Server is under maintenance"
    });
  }

  next();
}
```

Flow:

```text
Request
   ↓
Maintenance Middleware
   ↓
Maintenance?
 ┌─┴─┐
YES NO
 ↓   ↓
503 next()
     ↓
 Controller
```

Notice:

```js
return res.status(...).json(...)
```

There is no `next()` because the request has already been completed.

---

# 8. Middleware Can Modify the Request

Suppose authentication middleware verifies a JWT.

It can attach the authenticated user:

```js
req.user = decodedUser;
```

Then:

```text
Request
  ↓
Authentication Middleware
  ↓
req.user = user
  ↓
Controller
```

Controller:

```js
app.get("/profile", authenticate, (req, res) => {
  res.json({
    userId: req.user.id
  });
});
```

This is a very common real-world pattern.

---

# 9. Middleware Chain

You can have multiple middleware functions.

```js
app.get(
  "/profile",
  logger,
  authenticate,
  validateProfileRequest,
  getProfile
);
```

Flow:

```text
Request
   ↓
logger
   ↓
authenticate
   ↓
validateProfileRequest
   ↓
getProfile
   ↓
Response
```

Each middleware decides whether the request should continue.

---

# 10. Middleware Execution Order

**Order matters.**

Consider:

```js
app.use(logger);
app.use(authenticate);
app.use(validate);
```

The execution order is:

```text
logger
  ↓
authenticate
  ↓
validate
```

Not:

```text
validate
  ↓
logger
  ↓
authenticate
```

Express processes middleware according to how it is registered and how the route is matched.

---

# 11. Global Middleware

Global middleware runs for many or all requests.

Example:

```js
app.use(express.json());
```

This parses JSON request bodies.

Another example:

```js
app.use(logger);
```

This can log incoming requests.

Flow:

```text
Every Request
     ↓
Global Middleware
     ↓
Specific Route
```

---

# 12. Route-Level Middleware

Sometimes middleware should only apply to specific routes.

Example:

```js
app.get(
  "/admin/users",
  authenticate,
  requireAdmin,
  getUsers
);
```

Here:

```text
authenticate
requireAdmin
```

only apply to that route.

This is useful when different routes require different behavior.

---

# 13. Router-Level Middleware

You can also attach middleware to a router.

Example:

```js
const express = require("express");

const router = express.Router();

router.use(authenticate);

router.get("/profile", getProfile);
router.get("/orders", getOrders);
```

Now:

```text
/profile
/orders
```

both go through:

```text
authenticate
```

This is useful for protecting a group of related routes.

---

# 14. Example Architecture

A typical Node.js API might look like:

```text
Request
   ↓
Global Middleware
   ↓
Router
   ↓
Route Middleware
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

```text
POST /orders
      ↓
JSON Parser
      ↓
Request ID
      ↓
Logger
      ↓
Authentication
      ↓
Validation
      ↓
Controller
      ↓
Order Service
      ↓
Database
```

---

# 15. Authentication Middleware

Authentication is one of the most common middleware use cases.

```js
function authenticate(req, res, next) {
  const token = req.headers.authorization;

  if (!token) {
    return res.status(401).json({
      message: "Authentication required"
    });
  }

  // Verify token...

  req.user = user;

  next();
}
```

Route:

```js
app.get(
  "/profile",
  authenticate,
  getProfile
);
```

Flow:

```text
Request
   ↓
Authentication
   ↓
Token valid?
 ┌─┴─┐
NO  YES
 ↓    ↓
401  req.user
       ↓
   Controller
```

---

# 16. Authorization Middleware

Authentication answers:

```text
Who are you?
```

Authorization answers:

```text
What are you allowed to do?
```

Example:

```js
function requireAdmin(req, res, next) {
  if (req.user.role !== "admin") {
    return res.status(403).json({
      message: "Forbidden"
    });
  }

  next();
}
```

Route:

```js
app.delete(
  "/users/:id",
  authenticate,
  requireAdmin,
  deleteUser
);
```

Flow:

```text
Request
   ↓
Authentication
   ↓
Authorization
   ↓
Controller
```

---

# 17. Logging Middleware

Logging middleware records request information.

```js
function logger(req, res, next) {
  console.log({
    method: req.method,
    url: req.originalUrl,
    time: new Date().toISOString()
  });

  next();
}
```

Example output:

```text
{
  method: "GET",
  url: "/api/users",
  time: "2026-08-24T10:30:00.000Z"
}
```

In production systems, structured logging is generally preferable to scattered `console.log()` calls.

---

# 18. Request ID Middleware

A request ID helps trace one request across different services or logs.

Example:

```js
const crypto = require("crypto");

function requestId(req, res, next) {
  req.requestId = crypto.randomUUID();

  next();
}
```

Now:

```js
console.log({
  requestId: req.requestId,
  message: "Fetching user"
});
```

Logs can show:

```text
requestId = 123
```

throughout the request.

This becomes very useful in distributed systems.

---

# 19. Response Time Middleware

Middleware can measure how long a request takes.

```js
function responseTime(req, res, next) {
  const start = Date.now();

  res.on("finish", () => {
    const duration = Date.now() - start;

    console.log(
      `${req.method} ${req.url} - ${duration}ms`
    );
  });

  next();
}
```

Flow:

```text
Request
  ↓
Start timer
  ↓
Next middleware
  ↓
Controller
  ↓
Response
  ↓
Finish event
  ↓
Calculate duration
```

This is useful for performance monitoring.

---

# 20. Validation Middleware

Validation can be implemented as middleware.

```js
function validateCreateUser(req, res, next) {
  const { name, email } = req.body;

  if (!name || !email) {
    return res.status(400).json({
      message: "Name and email are required"
    });
  }

  next();
}
```

Route:

```js
app.post(
  "/users",
  validateCreateUser,
  createUser
);
```

---

# 21. CORS Middleware

CORS middleware controls cross-origin browser access according to configured policies.

Example:

```js
const cors = require("cors");

app.use(cors());
```

Conceptually:

```text
Browser
   ↓
Cross-Origin Request
   ↓
CORS Middleware
   ↓
Allowed?
 ┌─┴─┐
NO  YES
 ↓    ↓
Block Continue
```

Remember:

> CORS is a browser security mechanism. It is not authentication.

---

# 22. JSON Parsing Middleware

Express commonly uses:

```js
app.use(express.json());
```

Suppose the client sends:

```http
Content-Type: application/json
```

with:

```json
{
  "name": "Lokendra"
}
```

The JSON parser makes the parsed body available through:

```js
req.body
```

So:

```text
Raw HTTP Body
     ↓
JSON Parser
     ↓
req.body
```

This is another example of middleware performing work before your handler.

---

# 23. Error-Handling Middleware

Error handling is a special middleware pattern in Express.

It has four parameters:

```js
function errorHandler(
  err,
  req,
  res,
  next
) {
  // handle error
}
```

Example:

```js
app.use((err, req, res, next) => {
  console.error(err);

  res.status(500).json({
    message: "Internal Server Error"
  });
});
```

This middleware is generally registered after routes and other middleware.

---

# 24. `next()` vs `next(error)`

Normal flow:

```js
next();
```

means:

```text
Continue normal middleware chain
```

Error flow:

```js
next(error);
```

means:

```text
Skip normal middleware and move toward error handling
```

Example:

```js
app.get("/users", async (req, res, next) => {
  try {
    const users = await getUsers();

    res.json(users);
  } catch (error) {
    next(error);
  }
});
```

Then the error middleware can handle it.

---

# 25. Error Flow

Normal:

```text
Request
   ↓
Middleware
   ↓
Controller
   ↓
Response
```

Error:

```text
Request
   ↓
Middleware
   ↓
Controller
   ↓
Error
   ↓
next(error)
   ↓
Error Middleware
   ↓
Error Response
```

This allows centralized error handling.

---

# 26. Middleware vs Controller

This distinction is important.

### Middleware

Usually handles cross-cutting or pipeline concerns.

Examples:

```text
Authentication
Logging
Validation
Request IDs
CORS
Rate limiting
Parsing
```

### Controller / Handler

Usually handles the endpoint-specific request and coordinates application logic.

Example:

```js
async function getUser(req, res) {
  const user = await userService.getUser(req.params.id);

  res.json(user);
}
```

Mental model:

```text
Middleware
→ "Should this request continue and what context should it have?"

Controller
→ "What should this endpoint do?"
```

---

# 27. Middleware vs Service

A service usually contains application/business logic.

Example:

```js
async function createOrder(userId, items) {
  // business rules
  // calculate totals
  // check inventory
  // create order
}
```

Middleware:

```text
Authentication
Validation
Logging
```

Service:

```text
Business Rules
```

So:

```text
Middleware
    ↓
Controller
    ↓
Service
```

---

# 28. Middleware vs Utility Function

A utility function is usually a reusable function that performs a specific operation.

Example:

```js
function formatDate(date) {
  // ...
}
```

Middleware participates directly in the request pipeline.

```js
function authenticate(req, res, next) {
  // ...
}
```

The presence of:

```js
req
res
next
```

is a strong clue that you're dealing with request middleware in Express.

---

# 29. Middleware Should Have One Clear Responsibility

Avoid huge middleware:

```js
function everything(req, res, next) {
  // authentication
  // database calls
  // business logic
  // email
  // logging
  // validation
  // payment
  // ...
}
```

Instead:

```text
requestId
    ↓
logger
    ↓
authenticate
    ↓
authorize
    ↓
validate
    ↓
controller
```

Each component has a clear responsibility.

This improves:

```text
Readability
Testing
Maintenance
Reusability
Debugging
```

---

# 30. Middleware Composition

A powerful concept is **composition**.

Instead of one giant function:

```text
Middleware
```

we compose smaller middleware:

```text
A
 ↓
B
 ↓
C
 ↓
D
```

Example:

```js
app.post(
  "/orders",
  authenticate,
  requireUser,
  validateOrder,
  createOrder
);
```

This makes the pipeline easy to understand.

---

# 31. Middleware Ordering Example

Consider:

```js
app.use(authenticate);
app.use(logger);
```

versus:

```js
app.use(logger);
app.use(authenticate);
```

These produce different execution order.

Usually, logging may be placed early so even rejected requests are recorded:

```text
Request
  ↓
Request ID
  ↓
Logger
  ↓
Authentication
  ↓
Authorization
  ↓
Validation
  ↓
Controller
```

But the ideal ordering depends on what each middleware needs.

---

# 32. Example — Complete Request Pipeline

Imagine:

```http
POST /api/orders
```

Request:

```json
{
  "productId": "101",
  "quantity": 2
}
```

Pipeline:

```text
                    POST /api/orders
                           ↓
                    Request ID
                           ↓
                        Logger
                           ↓
                     JSON Parser
                           ↓
                    Authentication
                           ↓
                    Authorization
                           ↓
                       Validation
                           ↓
                       Controller
                           ↓
                         Service
                           ↓
                       Database
                           ↓
                        Response
```

Each stage has a specific responsibility.

---

# 33. Middleware and Request Context

Middleware is commonly used to build request context.

Example:

```js
function requestContext(req, res, next) {
  req.context = {
    requestId: crypto.randomUUID(),
    startedAt: Date.now()
  };

  next();
}
```

Later:

```js
console.log(req.context.requestId);
```

This connects directly to the next topic:

> **Request Context**

We'll go deeper into this concept separately.

---

# 34. Middleware and Async Code

Middleware can perform asynchronous operations.

Example:

```js
async function authenticate(req, res, next) {
  try {
    const user = await findUser();

    req.user = user;

    next();
  } catch (error) {
    next(error);
  }
}
```

Important:

> Async middleware should properly propagate errors to the framework/error handler.

---

# 35. Don't Put Heavy Work in Middleware

Suppose middleware does:

```text
Request
 ↓
Download 500 MB file
 ↓
Complex calculation
 ↓
Database processing
 ↓
Controller
```

Every request now has to wait.

Middleware should generally perform lightweight pipeline concerns.

Heavy work may belong in:

```text
Service
Background Job
Task Queue
Worker
```

depending on the use case.

---

# 36. Middleware and Database Calls

You can perform database operations in middleware, but don't do it automatically just because you can.

Example:

```js
async function loadUser(req, res, next) {
  const user = await User.findById(req.params.id);

  if (!user) {
    return res.status(404).json({
      message: "User not found"
    });
  }

  req.user = user;

  next();
}
```

This can be useful when multiple downstream handlers need the same resource.

But unnecessary database calls in middleware can hurt performance.

---

# 37. Middleware and Rate Limiting

Rate limiting is another common middleware use case.

Conceptually:

```text
Request
   ↓
Rate Limiter
   ↓
Within allowed limit?
 ┌────┴────┐
NO        YES
 ↓          ↓
429       next()
```

Example:

```text
100 requests/minute
```

If a client exceeds the limit:

```http
429 Too Many Requests
```

Rate limiting is commonly used for:

```text
Login
OTP endpoints
Public APIs
Password reset
Expensive endpoints
```

---

# 38. Middleware and Security Headers

Security-related middleware can add HTTP headers.

For example, Node.js applications commonly use security middleware such as Helmet.

Conceptually:

```text
Request
   ↓
Security Middleware
   ↓
Add security headers
   ↓
Next
```

This is another example of a cross-cutting concern.

---

# 39. Middleware and Request Lifecycle

A backend request can be thought of as a pipeline:

```text
                REQUEST
                   ↓
          ┌─────────────────┐
          │ Middleware #1   │
          └────────┬────────┘
                   ↓
          ┌─────────────────┐
          │ Middleware #2   │
          └────────┬────────┘
                   ↓
          ┌─────────────────┐
          │ Middleware #3   │
          └────────┬────────┘
                   ↓
          ┌─────────────────┐
          │ Controller      │
          └────────┬────────┘
                   ↓
                Service
                   ↓
                Database
                   ↓
                RESPONSE
```

This is the core concept you should remember.

---

# 40. Middleware Can Run After the Controller Starts

Middleware is not always simply:

```text
before controller
```

Middleware can also register logic that runs when the response finishes.

Example:

```js
function logger(req, res, next) {
  const start = Date.now();

  res.on("finish", () => {
    const duration = Date.now() - start;

    console.log({
      method: req.method,
      url: req.originalUrl,
      statusCode: res.statusCode,
      duration
    });
  });

  next();
}
```

This lets middleware observe the final outcome.

---

# 41. Middleware in Large Applications

A production backend may have middleware such as:

```text
Request ID
      ↓
Access Logging
      ↓
Security Headers
      ↓
CORS
      ↓
Body Parsing
      ↓
Authentication
      ↓
Authorization
      ↓
Validation
      ↓
Rate Limiting
      ↓
Controller
      ↓
Error Handler
```

Not every API needs every middleware.

The correct pipeline depends on the application's requirements.

---

# 42. A Practical Node.js Folder Structure

A clean project might look like:

```text
src/
│
├── middleware/
│   ├── auth.middleware.js
│   ├── error.middleware.js
│   ├── logger.middleware.js
│   ├── validation.middleware.js
│   └── requestId.middleware.js
│
├── controllers/
│   ├── user.controller.js
│   └── order.controller.js
│
├── services/
│   ├── user.service.js
│   └── order.service.js
│
├── routes/
│   ├── user.routes.js
│   └── order.routes.js
│
└── app.js
```

This keeps middleware separate from business logic.

---

# 43. Complete Example

### `logger.middleware.js`

```js
function logger(req, res, next) {
  console.log(
    `${req.method} ${req.originalUrl}`
  );

  next();
}

module.exports = logger;
```

### `auth.middleware.js`

```js
function authenticate(req, res, next) {
  const token = req.headers.authorization;

  if (!token) {
    return res.status(401).json({
      message: "Authentication required"
    });
  }

  // Verify token...

  req.user = {
    id: 101,
    role: "user"
  };

  next();
}

module.exports = authenticate;
```

### `validation.middleware.js`

```js
function validateCreateUser(req, res, next) {
  const { name, email } = req.body;

  if (!name || !email) {
    return res.status(400).json({
      message: "Name and email are required"
    });
  }

  next();
}

module.exports = validateCreateUser;
```

### Route

```js
router.post(
  "/users",
  logger,
  authenticate,
  validateCreateUser,
  createUser
);
```

Flow:

```text
POST /users
     ↓
Logger
     ↓
Authentication
     ↓
Validation
     ↓
Controller
     ↓
Service
     ↓
Database
```

---

# 44. Common Beginner Mistakes

## Mistake 1: Forgetting `next()`

Wrong:

```js
function logger(req, res, next) {
  console.log("Request");
}
```

Correct:

```js
function logger(req, res, next) {
  console.log("Request");

  next();
}
```

Unless the middleware intentionally ends the request.

---

## Mistake 2: Calling `next()` after sending a response

Bad:

```js
if (!req.user) {
  res.status(401).json({
    message: "Unauthorized"
  });

  next();
}
```

This can cause the pipeline to continue after the response has already been sent.

Better:

```js
if (!req.user) {
  return res.status(401).json({
    message: "Unauthorized"
  });
}
```

---

## Mistake 3: Doing business logic in middleware

Avoid:

```js
function middleware(req, res, next) {
  // calculate order total
  // process payment
  // update database
  // send email
}
```

Put business logic in services where appropriate.

---

## Mistake 4: Making middleware do everything

Avoid:

```text
Authentication
Validation
Database
Payment
Email
Logging
Business Rules
```

inside one middleware.

Prefer small, focused middleware.

---

## Mistake 5: Wrong middleware order

For example:

```text
Controller
 ↓
Authentication
```

doesn't make sense if the controller requires an authenticated user.

Usually:

```text
Authentication
 ↓
Authorization
 ↓
Controller
```

---

# 45. Middleware vs Interceptor vs Filter

Different backend frameworks use different terminology.

The general concept is similar:

```text
Request
   ↓
Cross-cutting processing
   ↓
Handler
   ↓
Response
```

For example:

```text
Express      → Middleware
NestJS       → Middleware / Guards / Interceptors / Pipes
Spring       → Filters / Interceptors
ASP.NET      → Middleware
Django       → Middleware
```

The exact features differ, but the underlying idea is:

> **Intercept the request/response lifecycle to perform reusable cross-cutting work.**

---

# 46. Interview Questions

## Q1. What is middleware?

> Middleware is a function or component that runs during the request-response lifecycle and can inspect, modify, reject, or pass a request to the next stage of processing.

---

## Q2. What is `next()` in Express?

> `next()` passes control to the next middleware or route handler in the middleware chain.

---

## Q3. What happens if you don't call `next()`?

> If the middleware doesn't send a response and doesn't call `next()`, the request can remain pending because the pipeline doesn't continue.

---

## Q4. Can middleware send a response?

> Yes. Middleware can terminate the request by sending a response, for example when authentication or authorization fails.

---

## Q5. What is middleware used for?

> Middleware is commonly used for cross-cutting concerns such as logging, authentication, authorization, validation, request IDs, CORS, rate limiting, parsing, and error handling.

---

## Q6. What is the difference between middleware and controller?

> Middleware handles reusable request-pipeline concerns, while a controller or handler implements the endpoint-specific behavior and coordinates the application logic.

---

## Q7. What is error-handling middleware in Express?

> Error-handling middleware is middleware with the signature `(err, req, res, next)` and is used to centrally process errors and generate appropriate responses.

---

## Q8. Why does middleware order matter?

> Middleware executes according to its position in the pipeline, so order determines which middleware runs first and what data or context is available to later middleware and handlers.

---

## Q9. Can middleware modify the request?

> Yes. Middleware can attach trusted information to the request, such as an authenticated user, request ID, or parsed data, which can then be used by downstream handlers.

---

## Q10. Should business logic be placed in middleware?

> Generally no. Middleware should focus on request-pipeline or cross-cutting concerns, while domain and business logic should usually live in services or dedicated business layers.

---

# 47. Interview Scenario

### Interviewer:

> "How would you protect an admin API?"

A strong answer:

> "I would first use authentication middleware to verify the user's credentials or access token and attach the authenticated user to the request context. Then I'd use authorization middleware to check whether the user has the required admin role or permission. Only after those checks pass would the request reach the controller."

Flow:

```text
Request
  ↓
Authentication Middleware
  ↓
Authorization Middleware
  ↓
Controller
  ↓
Service
```

---

# 48. Interview Scenario — Logging

### Interviewer:

> "How would you log every API request?"

Answer:

> "I'd create a reusable logging middleware that records information such as the HTTP method, URL, request ID, response status, and request duration. I'd register it early in the middleware pipeline so requests can be traced even when later middleware rejects them."

---

# 49. Interview Scenario — Validation

### Interviewer:

> "Where would you validate API input?"

Answer:

> "I would validate untrusted request data at the API boundary, typically using schema-based validation middleware. The controller and service should then receive validated and appropriately transformed data instead of repeatedly validating raw request input."

---

# 50. The Most Important Mental Model

Memorize this:

```text
                    HTTP REQUEST
                         │
                         ▼
                ┌─────────────────┐
                │ Request ID      │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Logger          │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Authentication  │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Authorization   │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Validation      │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Controller      │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Service         │
                └────────┬────────┘
                         ↓
                ┌─────────────────┐
                │ Database        │
                └────────┬────────┘
                         ↓
                    HTTP RESPONSE
```

The key idea:

```text
Middleware
    =
Reusable processing inside the request-response pipeline
```

---

# 51. Quick Revision

```text
Middleware
→ Runs during request-response processing

next()
→ Continue to the next stage

res.json(...)
→ End the request

next(error)
→ Pass error to error-handling middleware

Global middleware
→ Applies broadly

Route middleware
→ Applies to specific routes

Common uses
→ Logging
→ Authentication
→ Authorization
→ Validation
→ CORS
→ Rate limiting
→ Request IDs
→ Error handling
→ Parsing

Best practice
→ Keep middleware focused
→ Respect execution order
→ Don't mix business logic unnecessarily
```

---

# 52. One-Line Interview Summary

> **Middleware is a reusable component in the backend request-response pipeline that can inspect, transform, enrich, reject, or pass a request to the next stage, making it ideal for cross-cutting concerns such as authentication, logging, validation, and error handling.**
