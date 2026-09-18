# Error Handling in Backend Development

## 1. What is Error Handling?

Error handling is the process of detecting, handling, and responding to errors that occur while a backend application is running.

Errors can happen because of:

- Invalid user input
- Authentication failure
- Database failure
- External API failure
- Network problems
- Missing resources
- Programming bugs
- Server configuration problems
- Unexpected exceptions

The goal is not to prevent every error.

The goal is to:

1. Detect the error
2. Handle it safely
3. Return a meaningful response
4. Log useful information
5. Prevent the application from crashing unnecessarily
6. Avoid exposing sensitive internal details

---

# 2. Simple Mental Model

Think about a backend request:

    Client
       ↓
    Router
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

If something fails anywhere:

    Error
      ↓
    Error Handler
      ↓
    Log Error
      ↓
    Send Safe Response
      ↓
    Client

Example:

    GET /users/123

If user 123 doesn't exist:

    Database → User not found
                  ↓
             Error Handler
                  ↓
             HTTP 404
                  ↓
        { message: "User not found" }

---

# 3. Why Error Handling is Important

Without proper error handling:

- Server may crash
- Client may receive unclear errors
- Debugging becomes difficult
- Sensitive information may leak
- Database errors may be exposed
- Users get poor experience
- Production systems become difficult to monitor

Good error handling makes a backend:

- Reliable
- Debuggable
- Secure
- Maintainable
- Predictable

---

# 4. Types of Errors

There are several common categories.

## A. Client Errors

The client sends something invalid.

Examples:

- Invalid email
- Missing required field
- Invalid ID
- Unauthorized request
- Resource doesn't exist

Usually:

    400 Bad Request
    401 Unauthorized
    403 Forbidden
    404 Not Found
    409 Conflict
    422 Unprocessable Entity

---

## B. Server Errors

Something went wrong on the server.

Examples:

- Database unavailable
- Unexpected exception
- Third-party API failure
- Programming bug

Usually:

    500 Internal Server Error
    502 Bad Gateway
    503 Service Unavailable
    504 Gateway Timeout

---

# 5. HTTP Status Codes

Understanding status codes is extremely important for backend development.

## 2xx — Success

    200 OK
    201 Created
    202 Accepted
    204 No Content

Example:

    POST /users

Successful user creation:

    201 Created

---

## 4xx — Client-side problems

### 400 Bad Request

Request is invalid.

Example:

    {
      "email": "abc"
    }

If the API requires a valid email, it can return:

    400 Bad Request

---

### 401 Unauthorized

Authentication is missing or invalid.

Example:

    Authorization: Bearer invalid-token

Response:

    401 Unauthorized

Important:

401 generally means:

    "You are not authenticated."

---

### 403 Forbidden

The user is authenticated but doesn't have permission.

Example:

    Admin-only API
          ↓
    Normal user
          ↓
    403 Forbidden

Difference:

    401 → Who are you?
    403 → I know who you are, but you cannot do this.

---

### 404 Not Found

Requested resource doesn't exist.

Example:

    GET /users/999

If user 999 doesn't exist:

    404 Not Found

---

### 409 Conflict

Request conflicts with the current state.

Example:

    POST /users

Trying to create an account with an email that already exists.

Response:

    409 Conflict

---

### 422 Unprocessable Entity

Request format may be valid, but the provided data fails validation or business rules.

Example:

    age = -5

---

## 5xx — Server-side problems

### 500 Internal Server Error

Unexpected server error.

Example:

    Database query unexpectedly throws an exception.

Return:

    500 Internal Server Error

Do not expose the actual stack trace to the client.

---

### 502 Bad Gateway

A server acting as a gateway/proxy received an invalid response from an upstream service.

Example:

    API Gateway
         ↓
    Payment Service
         ↓
    Invalid response

---

### 503 Service Unavailable

Service is temporarily unavailable.

Example:

    Database/service is down
    Server is overloaded
    Maintenance is happening

---

### 504 Gateway Timeout

An upstream service didn't respond within the expected time.

---

# 6. Expected vs Unexpected Errors

This is an important backend concept.

## Expected Errors

These are errors that can happen normally and should be handled intentionally.

Examples:

    User not found
    Invalid password
    Email already exists
    Insufficient balance
    Product out of stock
    Invalid coupon

These should usually be converted into meaningful application errors.

---

## Unexpected Errors

These are generally programming/system failures.

Examples:

    Cannot read property of undefined
    Database connection unexpectedly failed
    Unexpected null value
    Programming bug

These should be:

- Logged
- Tracked
- Returned as a safe generic error

Example client response:

    {
      "message": "Internal server error"
    }

Instead of:

    {
      "message": "MongoServerError: ...",
      "stack": "..."
    }

---

# 7. try/catch

JavaScript provides `try/catch` for handling exceptions.

Example:

    try {
      const user = await getUser();

      return user;
    } catch (error) {
      console.error(error);
    }

If `getUser()` throws an error, control moves to `catch`.

---

# 8. Why try/catch Everywhere is Not Ideal

A common beginner approach is:

    try {
      ...
    } catch (error) {
      ...
    }

in every controller.

This can create:

- Repeated code
- Large controllers
- Inconsistent responses
- Difficult maintenance

Instead, production applications often use a centralized error-handling mechanism.

---

# 9. Centralized Error Handling

The basic idea:

    Controller
        ↓
    throw error
        ↓
    Central Error Middleware
        ↓
    Log
        ↓
    Format response
        ↓
    Client

This gives the application one place to handle errors consistently.

---

# 10. Express Error Middleware

In Express, error middleware has four parameters:

    (err, req, res, next)

Example:

    app.use((err, req, res, next) => {
      console.error(err);

      res.status(500).json({
        message: "Internal server error"
      });
    });

The important part is the four-argument signature.

---

# 11. Custom Error Class

Instead of throwing random strings or objects, we can create a custom application error.

Conceptually:

    class AppError extends Error {
      constructor(message, statusCode) {
        super(message);
        this.statusCode = statusCode;
      }
    }

Then:

    throw new AppError("User not found", 404);

The centralized error handler can read:

    error.statusCode
    error.message

and generate the response.

---

# 12. Error Response Structure

A consistent error response is important.

Example:

    {
      "success": false,
      "message": "User not found"
    }

For validation:

    {
      "success": false,
      "message": "Validation failed",
      "errors": {
        "email": "Invalid email",
        "age": "Age must be greater than 18"
      }
    }

A production API may also include:

    code
    requestId
    details

Example:

    {
      "success": false,
      "message": "Validation failed",
      "code": "VALIDATION_ERROR",
      "requestId": "abc-123"
    }

---

# 13. Error Code vs Error Message

Do not depend only on human-readable messages.

Example:

    {
      "message": "Email already exists",
      "code": "EMAIL_ALREADY_EXISTS"
    }

The frontend can use:

    code === "EMAIL_ALREADY_EXISTS"

instead of checking:

    message === "Email already exists"

Why?

Because messages may change.

Error codes provide a more stable API contract.

---

# 14. Error Handling Across Layers

Consider:

    Router
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database

Each layer has a different responsibility.

## Repository

Handles database-related failures.

Example:

    Database connection failed

---

## Service

Handles business errors.

Example:

    Insufficient balance

---

## Controller

Converts application results/errors into HTTP responses.

Example:

    Insufficient balance
          ↓
    400 Bad Request

---

## Error Middleware

Provides the final centralized HTTP error response.

---

# 15. Business Error Example

Imagine an e-commerce order.

Request:

    POST /orders

User wants to buy:

    iPhone 15

But inventory is:

    0

Service detects:

    ProductOutOfStock

Then:

    Service
       ↓
    throw ProductOutOfStockError
       ↓
    Error Middleware
       ↓
    409 Conflict
       ↓
    Client

Response:

    {
      "success": false,
      "message": "Product is out of stock",
      "code": "PRODUCT_OUT_OF_STOCK"
    }

---

# 16. Validation Errors

Validation should happen before unnecessary business/database work.

Example:

    POST /users

Request:

    {
      "email": "hello",
      "password": ""
    }

Validation detects:

    Invalid email
    Missing password

Return:

    400 Bad Request

or, depending on the API's conventions:

    422 Unprocessable Entity

The important thing is to use a consistent convention.

---

# 17. Authentication Errors

Example:

    GET /profile

No token:

    401 Unauthorized

Invalid/expired token:

    401 Unauthorized

Authenticated user tries to access admin endpoint:

    403 Forbidden

Flow:

    Request
       ↓
    Auth Middleware
       ↓
    Token invalid?
       ↓
    401

or:

    Token valid
       ↓
    Check role
       ↓
    Not allowed
       ↓
    403

---

# 18. Database Errors

Databases can fail for many reasons:

- Connection failure
- Duplicate key
- Timeout
- Constraint violation
- Query failure
- Server unavailable

Do not blindly return database errors to users.

Bad:

    {
      "error": "MongoServerError: E11000 duplicate key..."
    }

Better:

    {
      "success": false,
      "message": "Email already exists",
      "code": "EMAIL_ALREADY_EXISTS"
    }

The backend can log the original database error for debugging.

---

# 19. External API Errors

Modern applications often depend on external services.

Example:

    Your Backend
         ↓
    Payment API
         ↓
    Email API
         ↓
    Shipping API

Any external service can fail.

Example:

    Payment API
         ↓
    Timeout

Your backend should:

1. Detect timeout
2. Log the failure
3. Decide whether retry is safe
4. Return an appropriate response
5. Avoid exposing internal details

Example:

    {
      "success": false,
      "message": "Payment service temporarily unavailable",
      "code": "PAYMENT_SERVICE_UNAVAILABLE"
    }

---

# 20. Timeouts

Never assume an external API will always respond.

Bad:

    await fetch(paymentUrl);

If the external service hangs, your request may remain waiting.

Better architecture:

    Client
       ↓
    Backend
       ↓
    External API
       ↓
    Timeout
       ↓
    Handle error

Timeouts protect your application from hanging indefinitely.

---

# 21. Retries

Some errors are temporary.

Example:

    Your API
       ↓
    External API
       ↓
    Temporary network failure

You may retry.

Typical retry strategy:

    Attempt 1
       ↓
    wait
       ↓
    Attempt 2
       ↓
    wait longer
       ↓
    Attempt 3

This is called exponential backoff.

Example:

    100ms
    200ms
    400ms
    800ms

But do NOT blindly retry every operation.

For example:

    POST /payment

Retrying could potentially create duplicate effects unless the operation is designed to be idempotent.

---

# 22. Idempotency and Error Handling

This is especially important for payments/orders.

Suppose:

    POST /payment

Payment succeeds, but the response is lost.

Client thinks:

    "Payment failed"

Client retries.

Without idempotency:

    Payment 1 → ₹1000
    Payment 2 → ₹1000

User may be charged twice.

With an idempotency key:

    Idempotency-Key: abc123

The server can recognize the repeated request and avoid creating a duplicate operation.

---

# 23. Logging Errors

Error handling and logging go together.

At minimum, logs should help answer:

- What happened?
- When did it happen?
- Which endpoint?
- Which request?
- Which user/request context?
- What service failed?
- What was the error?

Example conceptual log:

    {
      "level": "error",
      "message": "Payment service timeout",
      "requestId": "req-123",
      "endpoint": "/payments",
      "method": "POST"
    }

---

# 24. Don't Log Sensitive Data

Never casually log:

    Passwords
    Access tokens
    Refresh tokens
    Credit card numbers
    API secrets
    Private keys

Bad:

    console.log(req.body);

if `req.body` contains passwords or sensitive information.

Logs can themselves become a security risk.

---

# 25. Request ID / Correlation ID

A request ID uniquely identifies a request.

Example:

    Client
       ↓
    Request ID: req-123
       ↓
    API Gateway
       ↓
    Backend
       ↓
    Database
       ↓
    Payment Service

If something fails, you can search logs for:

    req-123

This is extremely useful in distributed systems.

---

# 26. Error Propagation

Errors should usually move upward until the layer responsible for handling them.

Example:

    Database
       ↓
    Repository
       ↓
    Service
       ↓
    Controller
       ↓
    Error Middleware

Don't silently swallow important errors.

Bad:

    try {
      await saveUser();
    } catch (error) {
      console.log(error);
    }

The operation failed, but the application may continue as if it succeeded.

Better:

    catch (error) {
      logger.error(error);
      throw error;
    }

or transform it into an appropriate application error.

---

# 27. Error Transformation

Low-level errors should often be transformed into meaningful application errors.

Example:

    MongoDB duplicate key error
             ↓
    Repository
             ↓
    "email already exists"
             ↓
    Service / error handler
             ↓
    409 Conflict

The client doesn't need to know MongoDB's internal error format.

---

# 28. Don't Expose Stack Traces in Production

Development:

    {
      "message": "...",
      "stack": "..."
    }

can be useful.

Production should generally return something like:

    {
      "success": false,
      "message": "Internal server error",
      "requestId": "req-123"
    }

while the full stack trace is stored securely in logs/monitoring.

---

# 29. Development vs Production Error Responses

## Development

More debugging information can be available.

Example:

    message
    stack
    error type
    request ID

## Production

Keep responses safe:

    message
    code
    request ID

Detailed implementation information stays in logs.

---

# 30. Operational vs Programming Errors

Another useful distinction:

## Operational Errors

Expected failures that the application can handle.

Examples:

    Invalid input
    User not found
    Database temporarily unavailable
    External API timeout
    Authentication failure

These can often be handled without restarting the application.

---

## Programming Errors

Usually indicate a bug.

Examples:

    undefined variable
    incorrect function usage
    unexpected null
    invalid assumptions

These require investigation and fixing the code.

---

# 31. Global Error Handler

A typical Express architecture:

    app.use(routes);

    app.use(notFoundHandler);

    app.use(globalErrorHandler);

Conceptually:

    Request
       ↓
    Routes
       ↓
    Controller
       ↓
    Error?
       ↓
    Global Error Handler
       ↓
    Response

This gives the application a final safety net.

---

# 32. 404 Route Handler

If no route matches:

    GET /something-that-does-not-exist

The application can generate:

    {
      "success": false,
      "message": "Route not found",
      "code": "ROUTE_NOT_FOUND"
    }

with:

    404 Not Found

This is different from:

    GET /users/999

where the route exists but the user doesn't.

---

# 33. Async Errors

Backend applications frequently use asynchronous operations.

Example:

    const user = await User.findById(id);

If the promise rejects, the error must reach the centralized error handler.

Depending on the Express version/setup, you may use:

- Native async error propagation
- An async wrapper
- Explicit `next(error)`

The important concept is:

    Async error
        ↓
    Error handler

not:

    Async error
        ↓
    Unhandled rejection
        ↓
    Unstable application

---

# 34. Error Handling with Services

Example architecture:

    Controller:

    const user = await userService.getUserById(id);

    if (!user) {
      throw new AppError("User not found", 404);
    }

The controller does not need to manually build every error response.

Centralized handler handles it.

---

# 35. Error Handling and Transactions

Consider:

    Create Order
       ↓
    Decrease Inventory
       ↓
    Create Payment Record

Suppose:

    Create Order → success
    Decrease Inventory → success
    Payment Record → failure

Without a transaction/compensation strategy, the system may become inconsistent.

Possible approaches:

    Database Transaction

or, for distributed operations:

    Saga / compensation

Error handling is therefore closely related to data consistency.

---

# 36. Partial Failure

Distributed systems frequently experience partial failures.

Example:

    Order Service → SUCCESS
    Payment Service → SUCCESS
    Inventory Service → FAILURE

The entire system isn't necessarily down.

Only part of the workflow failed.

You need strategies such as:

- Retries
- Timeouts
- Circuit breakers
- Queues
- Idempotency
- Transactions where applicable
- Compensation
- Monitoring

---

# 37. Circuit Breaker

Suppose your backend calls a payment service.

Payment service is continuously failing.

Without protection:

    Request 1 → Payment API → fail
    Request 2 → Payment API → fail
    Request 3 → Payment API → fail
    Request 4 → Payment API → fail
    ...

Your system keeps wasting resources.

A circuit breaker can temporarily stop calls:

    Backend
       ↓
    Circuit Breaker
       ↓
    Payment Service

After repeated failures:

    CLOSED
       ↓
    OPEN
       ↓
    Don't call service
       ↓
    After waiting
       ↓
    HALF-OPEN
       ↓
    Test request
       ↓
    Success → CLOSED

This protects your system from cascading failures.

---

# 38. Error Handling with Queues

For background jobs:

    API
     ↓
    Queue
     ↓
    Worker
     ↓
    External Service

If worker fails:

    Job fails
       ↓
    Retry
       ↓
    Retry
       ↓
    Retry
       ↓
    Dead Letter Queue

This prevents temporary failures from immediately losing the task.

---

# 39. Dead Letter Queue

A Dead Letter Queue (DLQ) stores jobs that repeatedly fail.

Example:

    Job
     ↓
    Worker
     ↓
    Failure
     ↓
    Retry 1
     ↓
    Retry 2
     ↓
    Retry 3
     ↓
    DLQ

Later, developers can inspect and resolve the problem.

---

# 40. Error Handling in File Uploads

Example:

    POST /upload

Possible errors:

    File missing
    File too large
    Invalid file type
    Storage service unavailable
    Upload timeout

Return appropriate errors instead of allowing the process to crash.

---

# 41. Security Considerations

Error messages should not reveal:

    Database credentials
    Internal IP addresses
    File paths
    Stack traces
    SQL queries
    API keys
    Authentication tokens
    Infrastructure details

Bad:

    "MongoDB connection failed at mongodb://admin:password@..."

Good:

    "Internal server error"

and log the detailed error securely.

---

# 42. Error Handling Best Practices

## 1. Centralize error handling

Use one consistent mechanism.

## 2. Use meaningful status codes

Don't return:

    200 OK

for every error.

## 3. Use consistent response structure

Example:

    {
      "success": false,
      "message": "...",
      "code": "..."
    }

## 4. Use custom application errors

For predictable business/application failures.

## 5. Log unexpected errors

Keep enough context for debugging.

## 6. Don't expose internal details

Especially in production.

## 7. Use request IDs

Useful for distributed debugging.

## 8. Handle async failures

Don't allow unhandled promise rejections.

## 9. Use timeouts

Especially for external services.

## 10. Retry carefully

Only when retrying is safe.

## 11. Make critical operations idempotent

Especially:

    Payments
    Orders
    Webhooks
    Background jobs

## 12. Monitor errors

Use logs, metrics, tracing, and error-monitoring systems.

---

# 43. Common Mistakes

## Mistake 1: Returning 200 for errors

Bad:

    200 OK
    {
      "error": "User not found"
    }

Better:

    404 Not Found

---

## Mistake 2: Exposing raw database errors

Bad:

    MongoServerError: E11000 ...

Better:

    {
      "message": "Email already exists",
      "code": "EMAIL_ALREADY_EXISTS"
    }

---

## Mistake 3: Sending stack traces to users

Avoid this in production.

---

## Mistake 4: Swallowing errors

Bad:

    try {
      await processPayment();
    } catch (error) {
      console.log(error);
    }

The application may incorrectly assume payment succeeded.

---

## Mistake 5: Logging sensitive information

Never log passwords or secrets.

---

## Mistake 6: Retrying everything

Some operations are not safe to retry.

---

## Mistake 7: No timeout for external APIs

A slow dependency can make your entire application slow.

---

## Mistake 8: Inconsistent error formats

Endpoint A:

    { "error": "..." }

Endpoint B:

    { "message": "..." }

Endpoint C:

    { "errors": [...] }

A consistent contract is easier for frontend developers.

---

# 44. Complete Error Flow

Example:

    Client
       ↓
    POST /orders
       ↓
    Router
       ↓
    Authentication Middleware
       ↓
    Validation Middleware
       ↓
    Controller
       ↓
    Order Service
       ↓
    Inventory Repository
       ↓
    Database
       ↓
    Database error
       ↓
    Repository transforms error
       ↓
    Service handles/propagates error
       ↓
    Global Error Handler
       ↓
    Logger / Monitoring
       ↓
    Safe HTTP Response
       ↓
    Client

Example response:

    HTTP 409

    {
      "success": false,
      "message": "Product is out of stock",
      "code": "PRODUCT_OUT_OF_STOCK",
      "requestId": "req-123"
    }

---

# 45. Error Handling Architecture

A production-oriented backend may look like:

    Client
      ↓
    API Gateway
      ↓
    Router
      ↓
    Middleware
      ↓
    Controller
      ↓
    Service / Business Logic
      ↓
    Repository
      ↓
    Database

    External APIs
          ↑
          |
    Queue / Workers

    Errors
      ↓
    Error Handler
      ↓
    Logger
      ↓
    Monitoring / Alerting

Important principle:

    Detect → Propagate → Transform → Log → Respond

---

# 46. Error Handling vs Validation

These concepts are related but different.

## Validation

Checks whether input is valid.

Example:

    email = "abc"

Validation:

    Invalid email

---

## Error Handling

Handles what happens when something fails.

Example:

    Database unavailable

Error handling:

    Log error
    Return 503
    Monitor failure

Simple difference:

    Validation = Is the input valid?

    Error handling = What do we do when something goes wrong?

---

# 47. Error Handling vs HTTP Status Codes

Error handling is the broader concept.

HTTP status codes are one way to communicate the result to the client.

Example:

    Database timeout
         ↓
    Error detected
         ↓
    Log error
         ↓
    Decide response
         ↓
    503 Service Unavailable

So:

    Error Handling
        |
        +-- Logging
        +-- Recovery
        +-- Retry
        +-- Transformation
        +-- Monitoring
        +-- HTTP Response

---

# 48. Production Error Handling Checklist

Before deploying a backend, ask:

    [ ] Is there a centralized error handler?
    [ ] Are HTTP status codes correct?
    [ ] Are validation errors handled?
    [ ] Are authentication errors handled?
    [ ] Are authorization errors handled?
    [ ] Are database errors handled?
    [ ] Are external API failures handled?
    [ ] Are external API calls given timeouts?
    [ ] Are retries controlled?
    [ ] Are important operations idempotent?
    [ ] Are errors logged?
    [ ] Are sensitive values excluded from logs?
    [ ] Are stack traces hidden in production?
    [ ] Are request IDs available?
    [ ] Are errors monitored?
    [ ] Are background job failures retried?
    [ ] Is there a DLQ where appropriate?
    [ ] Are critical workflows protected against partial failure?

---

# 49. Interview Questions

## Q1. What is error handling?

Error handling is the process of detecting, propagating, logging, and safely responding to failures in an application.

---

## Q2. Why use centralized error handling?

It avoids duplicate error-handling code and provides consistent responses, logging, and status codes across the application.

---

## Q3. Difference between 401 and 403?

    401 → Authentication is missing/invalid.

    403 → User is authenticated but doesn't have permission.

---

## Q4. Difference between 400 and 404?

    400 → Request itself is invalid.

    404 → Requested resource/route was not found.

---

## Q5. Difference between 404 user and 404 route?

    GET /users/999

Route exists but user doesn't:

    User not found

    GET /abcxyz

Route itself doesn't exist:

    Route not found

Both can use 404, but they represent different application situations.

---

## Q6. Why shouldn't we expose stack traces?

Because stack traces can reveal:

- Internal implementation
- File paths
- Libraries
- Database details
- Sensitive infrastructure information

---

## Q7. What is a custom error class?

A custom error class represents application-specific errors with additional information such as:

    message
    statusCode
    code

---

## Q8. What is error propagation?

It is the process of passing an error from the layer where it occurred to a layer capable of handling it.

Example:

    Repository
       ↓
    Service
       ↓
    Controller
       ↓
    Error Handler

---

## Q9. What is an operational error?

An expected runtime failure that the application can generally handle.

Examples:

    User not found
    Invalid input
    Timeout
    Authentication failure

---

## Q10. What is a programming error?

An unexpected bug in the application code.

Examples:

    Undefined variable
    Incorrect function call
    Unexpected null

---

## Q11. Why are retries dangerous for payments?

Because the first request may succeed even if the response is lost.

Retrying without idempotency can create duplicate payments.

---

## Q12. What is a circuit breaker?

A resilience pattern that temporarily stops calls to a failing dependency to prevent cascading failures.

---

## Q13. What is a Dead Letter Queue?

A queue where jobs are moved after repeatedly failing so they can be inspected or manually/reliably processed later.

---

## Q14. What should be logged for an error?

Useful information includes:

    Timestamp
    Error type
    Message
    Stack trace
    Request ID
    Endpoint
    HTTP method
    Relevant service/context

But sensitive information should be excluded.

---

# 50. Real Interview Scenario

### Interviewer:

Your payment API sometimes times out. How would you handle it?

### Answer:

I would set a timeout for the external payment API and classify timeout as a potentially temporary failure. Depending on the payment provider's guarantees, I would use controlled retries with exponential backoff. For payment creation, I would also use idempotency keys so retries don't create duplicate charges. I would log the failure with a request or transaction ID, return a safe response to the client, and monitor repeated failures. If the dependency continues failing, a circuit breaker can prevent cascading failures.

---

# 51. Another Interview Scenario

### Interviewer:

The database throws a duplicate-key error when registering a user. What would you return?

### Answer:

I would catch or transform the database-specific error into an application-level error such as `EMAIL_ALREADY_EXISTS`, then return a suitable HTTP status such as `409 Conflict`. I would avoid exposing the raw database error to the client.

---

# 52. Another Interview Scenario

### Interviewer:

What happens if an unexpected error occurs in production?

### Answer:

The error should reach the centralized error handler. The backend should log the detailed error securely with useful context such as the request ID, while returning a generic safe response such as `500 Internal Server Error` to the client. Monitoring should alert the team if the error is significant or recurring.

---

# 53. Quick Revision

    Error Handling
        ↓
    Detect error
        ↓
    Classify error
        ↓
    Propagate error
        ↓
    Transform if necessary
        ↓
    Log securely
        ↓
    Return correct status code
        ↓
    Monitor
        ↓
    Recover / retry when appropriate

Important status codes:

    200 → OK
    201 → Created
    204 → No Content

    400 → Bad Request
    401 → Unauthorized
    403 → Forbidden
    404 → Not Found
    409 → Conflict
    422 → Validation/semantic problem

    500 → Internal Server Error
    502 → Bad Gateway
    503 → Service Unavailable
    504 → Gateway Timeout

Important production concepts:

    Centralized error handling
    Custom errors
    Error codes
    Logging
    Request IDs
    Timeouts
    Retries
    Exponential backoff
    Idempotency
    Circuit breakers
    Dead Letter Queues
    Monitoring
    Safe production responses

---

# 54. One-Line Interview Summary

> Error handling is the process of safely detecting, propagating, transforming, logging, and responding to application failures while keeping the system reliable, secure, and debuggable.