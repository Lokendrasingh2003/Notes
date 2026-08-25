# Backend Development — Request Context

## 1. What is Request Context?

**Request context is the information and state associated with a single HTTP request while it moves through the backend request-response lifecycle.**

In simple words:

> **Request context is the collection of data that belongs to one specific request and is available to different parts of the backend while processing that request.**

A simple mental model:

    Client
      ↓
    HTTP Request
      ↓
    Request Context
      ↓
    Middleware
      ↓
    Controller
      ↓
    Service
      ↓
    Database
      ↓
    Response
      ↓
    Client

---

## 2. Why Do We Need Request Context?

Imagine a request:

    GET /api/orders

The request passes through:

    Request
       ↓
    Authentication
       ↓
    Authorization
       ↓
    Controller
       ↓
    Service
       ↓
    Database

Different parts of the application may need information about the same request.

For example:

    Authentication
    → Who is the user?

    Controller
    → What is the request ID?

    Service
    → Which user is making this request?

    Logger
    → Which request generated this log?

Request context provides a way to carry request-specific information through the request lifecycle.

---

## 3. What Can Request Context Contain?

Common examples include:

    Request ID
    Authenticated User
    User ID
    Role
    Permissions
    Correlation ID
    Trace ID
    Request Start Time
    Tenant ID
    Locale

The exact information depends on the application.

---

## 4. Request Context in Express

In Express, request-specific information is commonly attached to the `req` object.

Example:

    function requestContext(req, res, next) {
      req.context = {
        requestId: crypto.randomUUID(),
        startedAt: Date.now()
      };

      next();
    }

Now downstream middleware and handlers can access:

    req.context.requestId

For example:

    app.get("/users", (req, res) => {
      console.log(req.context.requestId);

      res.json({
        message: "Users fetched"
      });
    });

Flow:

    Request
       ↓
    Request Context Middleware
       ↓
    req.context created
       ↓
    Controller
       ↓
    req.context.requestId

---

## 5. Request ID

One of the most common pieces of request context is a **request ID**.

A request ID uniquely identifies a particular request.

Example:

    requestId = "abc-123"

Suppose a request generates these logs:

    Fetching user
    Checking permissions
    Fetching orders
    Returning response

Without a request ID, it may be difficult to know which logs belong to which request.

With a request ID:

    requestId=123 → Fetching user
    requestId=123 → Checking permissions
    requestId=123 → Fetching orders
    requestId=123 → Returning response

Now all logs can be connected to the same request.

---

## 6. Creating a Request ID

Example:

    const crypto = require("crypto");

    function requestId(req, res, next) {
      req.requestId = crypto.randomUUID();

      next();
    }

Register it:

    app.use(requestId);

Now every request receives a unique ID.

Flow:

    HTTP Request
          ↓
    Request ID Middleware
          ↓
    Generate ID
          ↓
    req.requestId
          ↓
    Controller

---

## 7. Request Context and Authentication

Authentication middleware can add the authenticated user to the request context.

Example:

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

Now the controller can access:

    req.user

Example:

    app.get("/profile", authenticate, (req, res) => {
      res.json({
        userId: req.user.id,
        role: req.user.role
      });
    });

Flow:

    Request
       ↓
    Authentication Middleware
       ↓
    Verify Token
       ↓
    req.user = authenticated user
       ↓
    Controller

---

## 8. Request Context and Authorization

Authorization can use information already added to the request context.

Example:

    function requireAdmin(req, res, next) {
      if (req.user.role !== "admin") {
        return res.status(403).json({
          message: "Forbidden"
        });
      }

      next();
    }

Route:

    app.delete(
      "/users/:id",
      authenticate,
      requireAdmin,
      deleteUser
    );

Flow:

    Request
       ↓
    Authentication
       ↓
    req.user created
       ↓
    Authorization
       ↓
    Check req.user.role
       ↓
    Controller

---

## 9. Request Context and Logging

Request context is very useful for logging.

Example:

    function logger(req, res, next) {
      console.log({
        requestId: req.requestId,
        method: req.method,
        url: req.originalUrl
      });

      next();
    }

Example output:

    {
      requestId: "abc-123",
      method: "GET",
      url: "/api/users"
    }

Now logs can be connected to a specific request.

---

## 10. Request Context and Response Time

Request context can store when the request started.

Example:

    function requestContext(req, res, next) {
      req.context = {
        requestId: crypto.randomUUID(),
        startedAt: Date.now()
      };

      next();
    }

Later:

    const duration = Date.now() - req.context.startedAt;

This allows us to measure request duration.

Flow:

    Request
       ↓
    Store startedAt
       ↓
    Controller
       ↓
    Response
       ↓
    Calculate duration

---

## 11. Request Context and Tracing

In distributed systems, a request may travel through multiple services.

For example:

    Client
       ↓
    API Gateway
       ↓
    User Service
       ↓
    Order Service
       ↓
    Payment Service
       ↓
    Database

The same request may need to be tracked across all these services.

A trace or correlation ID can be propagated:

    traceId = abc-123

Flow:

    API Gateway
    traceId=abc-123
          ↓
    User Service
    traceId=abc-123
          ↓
    Order Service
    traceId=abc-123
          ↓
    Payment Service
    traceId=abc-123

This makes distributed debugging much easier.

---

## 12. Request ID vs Correlation ID

These terms can vary between systems.

### Request ID

Usually identifies one particular request within a service or application.

    requestId = 123

### Correlation ID

Usually helps connect related operations across multiple services.

    correlationId = abc-123

Mental model:

    Request ID
    → Identify a request

    Correlation ID
    → Connect related operations across systems

---

## 13. Request Context and Tenant Information

In multi-tenant applications, the request context may contain information about which tenant is making the request.

Example:

    req.context = {
      requestId: crypto.randomUUID(),
      tenantId: "tenant-101"
    };

Then the service can use:

    req.context.tenantId

Flow:

    Request
       ↓
    Tenant Identification
       ↓
    tenantId
       ↓
    Request Context
       ↓
    Service
       ↓
    Tenant-specific Data

This is common in SaaS applications.

---

## 14. Request Context and Permissions

Authorization middleware may attach permissions to the request context.

Example:

    req.user = {
      id: 101,
      role: "manager",
      permissions: [
        "orders:read",
        "orders:create"
      ]
    };

Later:

    if (!req.user.permissions.includes("orders:create")) {
      return res.status(403).json({
        message: "Permission denied"
      });
    }

The request context allows downstream components to access the authenticated user's permissions.

---

## 15. Request Context Lifecycle

Request context exists for the lifetime of the request.

A simplified lifecycle:

    Request arrives
          ↓
    Create request context
          ↓
    Add request ID
          ↓
    Add authentication information
          ↓
    Add authorization information
          ↓
    Controller
          ↓
    Service
          ↓
    Database
          ↓
    Response
          ↓
    Request finishes
          ↓
    Context is no longer needed

Mental model:

    ONE REQUEST
        ↓
    ONE REQUEST CONTEXT
        ↓
    USED THROUGHOUT THAT REQUEST
        ↓
    REQUEST FINISHES
        ↓
    CONTEXT LIFETIME ENDS

---

## 16. Request Context vs Function Parameters

This distinction is important.

Suppose a service needs:

    userId

You could pass it explicitly:

    createOrder(userId, items);

This is often clear because `userId` is part of the operation's business input.

Request metadata such as:

    requestId
    traceId
    locale

may be more appropriate as request context.

Mental model:

    Business Data
    → Explicit function parameters

    Request Metadata
    → Request Context

Do not put every piece of data into request context.

---

## 17. What Should NOT Go Into Request Context?

Avoid putting large or unnecessary objects into the request context.

Avoid things like:

    Entire Database
    Entire Application State
    Huge Response Data
    Large Files

Request context should contain lightweight request-specific information.

Prefer information such as:

    Request ID
    User
    Tenant ID
    Permissions
    Trace ID
    Start Time
    Locale

when actually needed.

---

## 18. Request Context and Security

Request context can contain sensitive information.

For example:

    User information
    Permissions
    Tenant information
    Authentication details

Therefore:

> **Do not blindly log the entire request context.**

Avoid:

    console.log(req.context);

if the context contains sensitive information.

Instead, log only what is necessary:

    console.log({
      requestId: req.context.requestId,
      userId: req.user?.id
    });

Never unnecessarily log:

    Passwords
    Access Tokens
    Refresh Tokens
    Secrets
    Sensitive Personal Data

---

## 19. Complete Request Context Flow

A typical backend request can look like:

    HTTP Request
          ↓
    Request Context Middleware
          ↓
    Generate Request ID
          ↓
    Authentication
          ↓
    Add User to Context
          ↓
    Authorization
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
    Response

The request context may carry information such as:

    requestId
    user
    permissions
    tenantId
    traceId
    startedAt

throughout this lifecycle.

---

## 20. Request Context vs Middleware

Middleware and request context are closely related, but they are not the same thing.

### Middleware

Middleware is responsible for processing the request.

Examples:

    Authentication
    Logging
    Validation
    Request ID generation
    Authorization

### Request Context

Request context is the request-specific information available while processing the request.

Example:

    req.context = {
      requestId,
      user,
      tenantId,
      startedAt
    };

Mental model:

    Middleware
        ↓
    Creates / modifies
        ↓
    Request Context
        ↓
    Used by downstream components

---

## 21. Request Context vs Global Variables

Do not store request-specific information in global variables.

Bad approach:

    let currentUser;

Different requests can arrive at the same time.

For example:

    Request A → User A
    Request B → User B

A global variable can cause the data from one request to interfere with another request.

Instead, keep request-specific information associated with the request:

    req.user

or:

    req.context

Mental model:

    Request A → Context A
    Request B → Context B
    Request C → Context C

Each request should have its own context.

---

## 22. Request Context in Concurrent Requests

Backend servers handle many requests at the same time.

For example:

    Request A
       ↓
    requestId = A123

    Request B
       ↓
    requestId = B456

    Request C
       ↓
    requestId = C789

Each request must maintain its own state.

The context should not be shared accidentally between requests.

This is one of the most important reasons to avoid global mutable state for request-specific data.

---

## 23. Important Mental Model

Remember:

    Request Context
        =
    Request-specific information
        +
    Request-specific state
        +
    Available during request processing

Example:

    Request
       ↓
    Context
    ├── requestId
    ├── user
    ├── permissions
    ├── tenantId
    └── startedAt
       ↓
    Middleware
       ↓
    Controller
       ↓
    Service
       ↓
    Response

---

## 24. Interview Questions

### Q1. What is request context?

> **Request context is the request-specific information and state that is available to different parts of the backend while processing a particular HTTP request.**

### Q2. Why do we need request context?

> **It allows request-specific information such as request IDs, authenticated users, permissions, tenant IDs, and tracing information to be shared across the request-processing pipeline.**

### Q3. How is request context commonly implemented in Express?

> **In Express, request-specific information is commonly attached to the `req` object, such as `req.user`, `req.requestId`, or `req.context`.**

### Q4. What is a request ID?

> **A request ID is a unique identifier assigned to a request so that logs, errors, and operations related to that request can be traced together.**

### Q5. What is the difference between request context and global state?

> **Request context belongs to one specific request, while global state can be shared across multiple requests. Request-specific data should not be stored in global mutable variables.**

### Q6. What information can be stored in request context?

> **Common examples include request ID, authenticated user, permissions, tenant ID, trace ID, locale, and request start time.**

### Q7. Should all application data be stored in request context?

> **No. Request context should contain lightweight request-specific information. Important business data should usually be passed explicitly through function parameters or appropriate application layers.**

---

# 25. Interview Scenario

### Interviewer:

> "How would you track a request across multiple services?"

A strong answer:

> **"I would assign a unique request or correlation ID at the beginning of the request lifecycle and propagate it through the services. I would include that ID in structured logs and tracing information so that all operations belonging to the same request can be correlated across the system."**

Flow:

    Client
       ↓
    API Gateway
       ↓
    correlationId = abc-123
       ↓
    User Service
       ↓
    Order Service
       ↓
    Payment Service

All services can use:

    correlationId = abc-123

---

# 26. Quick Revision

    Request Context
    → Request-specific information and state

    Common data
    → Request ID
    → User
    → Permissions
    → Tenant ID
    → Trace ID
    → Start Time
    → Locale

    Express
    → Usually stored on req or req.context

    Request ID
    → Helps identify one request

    Correlation ID
    → Helps connect related operations across services

    Authentication
    → Can add req.user

    Logging
    → Can use requestId

    Tracing
    → Can use traceId / correlationId

    Security
    → Don't blindly log sensitive context

    Important
    → Don't use global variables for request-specific state

    Best Practice
    → Keep request context lightweight
    → Store only request-specific information
    → Avoid unnecessary database or large objects
    → Keep business data explicit where appropriate

---

# 27. One-Line Interview Summary

> **Request context is the request-specific information and state carried through the backend request lifecycle, commonly used for things like request IDs, authentication data, permissions, tenant information, logging, tracing, and debugging.**