# Backend Development — Handlers, Controllers and Services

## 1. The Big Picture

In a backend application, a request usually moves through several layers:

    Client
      ↓
    HTTP Request
      ↓
    Middleware
      ↓
    Handler / Controller
      ↓
    Service
      ↓
    Repository / Model
      ↓
    Database
      ↓
    Response
      ↓
    Client

The important idea is:

    Handler / Controller
    → Deals with the HTTP request and response

    Service
    → Contains business logic

    Repository / Model
    → Deals with data access

A simple mental model:

    Controller
    → "What endpoint was called?"

    Service
    → "What should the application actually do?"

    Repository
    → "How do we get or save the data?"

---

# 2. What is a Handler?

A **handler** is a function that handles an incoming request and produces a response.

In a simple Express application:

    app.get("/users", (req, res) => {
      res.json({
        message: "Users fetched"
      });
    });

The function:

    (req, res) => {
      ...
    }

is the request handler.

Its responsibility is to handle the HTTP request.

A handler commonly:

    Receives request
       ↓
    Reads request data
       ↓
    Calls application logic
       ↓
    Sends response

---

# 3. What is a Controller?

A **controller** is a component responsible for handling HTTP requests and coordinating the response.

For example:

    async function getUsers(req, res) {
      const users = await userService.getUsers();

      res.json(users);
    }

Here:

    getUsers()

is a controller function.

The controller receives:

    req
    res

and communicates with the service layer.

---

# 4. Handler vs Controller

The terms **handler** and **controller** are sometimes used interchangeably, especially in small applications.

But conceptually:

    Handler
    → A function that handles a request

    Controller
    → A structured application layer responsible for handling HTTP requests

For example:

    app.get("/users", getUsers);

Here:

    getUsers

can be called a handler.

If the application follows a layered architecture:

    routes/
    controllers/
    services/

then `getUsers` would usually be called a controller.

Mental model:

    Handler
    → General concept

    Controller
    → Architectural role

---

# 5. What is a Service?

A **service** contains application or business logic.

For example:

    async function createUser(userData) {
      const existingUser = await userRepository.findByEmail(
        userData.email
      );

      if (existingUser) {
        throw new Error("User already exists");
      }

      const user = await userRepository.create(userData);

      return user;
    }

The service decides:

    What should happen?
    What business rules apply?
    What operations are required?

The service should generally not be responsible for HTTP-specific details.

---

# 6. Controller vs Service

This is one of the most important backend concepts.

### Controller

The controller deals with HTTP.

It commonly handles:

    req
    res
    params
    query
    body
    status codes
    HTTP responses

### Service

The service deals with application/business logic.

It commonly handles:

    Business rules
    Calculations
    Workflows
    Database operations through repositories
    External service interactions

Mental model:

    Controller
    → HTTP layer

    Service
    → Business/application layer

---

# 7. Simple Example

Suppose we have:

    GET /users/101

The request flow could be:

    Client
      ↓
    GET /users/101
      ↓
    Route
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

Controller:

    async function getUser(req, res) {
      const user = await userService.getUserById(
        req.params.id
      );

      res.json(user);
    }

Service:

    async function getUserById(id) {
      return await userRepository.findById(id);
    }

The controller knows about:

    req.params.id

The service knows about:

    user ID
    business logic
    data retrieval

---

# 8. Why Separate Controllers and Services?

Imagine putting everything inside the controller:

    async function createOrder(req, res) {
      // validate data
      // check authentication
      // check inventory
      // calculate price
      // apply discount
      // create order
      // update inventory
      // send email
      // send response
    }

This controller becomes large and difficult to maintain.

Instead:

    Controller
       ↓
    Order Service
       ↓
    Inventory Service
       ↓
    Payment Service
       ↓
    Repository

Now each layer has a clear responsibility.

---

# 9. Controller Responsibility

A controller should generally be responsible for:

    Reading HTTP input
    ↓
    Calling the appropriate service
    ↓
    Formatting the HTTP response

Example:

    async function createUser(req, res, next) {
      try {
        const user = await userService.createUser(
          req.body
        );

        res.status(201).json(user);
      } catch (error) {
        next(error);
      }
    }

The controller:

    Reads req.body
    Calls service
    Returns HTTP response

It does not need to contain all the business logic.

---

# 10. Service Responsibility

A service should generally be responsible for:

    Business rules
    Application workflows
    Coordinating multiple operations
    Calling repositories
    Calling external services

Example:

    async function createOrder(userId, items) {
      const products = await productRepository.findProducts(
        items
      );

      // Check inventory

      // Calculate total

      // Apply business rules

      const order = await orderRepository.create({
        userId,
        items
      });

      return order;
    }

The service is focused on what the application should do.

---

# 11. Repository Responsibility

A repository is commonly responsible for data access.

Example:

    async function findUserById(id) {
      return await User.findById(id);
    }

The repository knows how to communicate with the database.

Mental model:

    Controller
    → HTTP

    Service
    → Business logic

    Repository
    → Database access

---

# 12. Complete Request Flow

Suppose the client sends:

    POST /users

with:

    {
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

The flow can be:

    Client
      ↓
    POST /users
      ↓
    Middleware
      ↓
    Controller
      ↓
    User Service
      ↓
    User Repository
      ↓
    Database
      ↓
    User Repository
      ↓
    User Service
      ↓
    Controller
      ↓
    HTTP Response

---

# 13. Example Architecture

A typical Node.js backend may look like:

    src/
    │
    ├── routes/
    │   └── user.routes.js
    │
    ├── controllers/
    │   └── user.controller.js
    │
    ├── services/
    │   └── user.service.js
    │
    ├── repositories/
    │   └── user.repository.js
    │
    ├── middleware/
    │   └── auth.middleware.js
    │
    └── app.js

The responsibilities are separated.

---

# 14. Route

The route connects an HTTP endpoint to a controller.

Example:

    router.get(
      "/users/:id",
      authenticate,
      userController.getUser
    );

The route mainly defines:

    HTTP Method
    URL
    Middleware
    Controller

It should not contain business logic.

---

# 15. Controller Example

`user.controller.js`

    const userService = require("../services/user.service");

    async function getUser(req, res, next) {
      try {
        const user = await userService.getUserById(
          req.params.id
        );

        res.status(200).json(user);
      } catch (error) {
        next(error);
      }
    }

    module.exports = {
      getUser
    };

The controller handles the HTTP layer.

---

# 16. Service Example

`user.service.js`

    const userRepository = require(
      "../repositories/user.repository"
    );

    async function getUserById(id) {
      const user = await userRepository.findById(id);

      if (!user) {
        throw new Error("User not found");
      }

      return user;
    }

    module.exports = {
      getUserById
    };

The service contains application logic.

---

# 17. Repository Example

`user.repository.js`

    const User = require("../models/user.model");

    async function findById(id) {
      return await User.findById(id);
    }

    module.exports = {
      findById
    };

The repository handles database access.

---

# 18. Complete Flow

Putting everything together:

    Client
       ↓
    GET /users/101
       ↓
    Router
       ↓
    Authentication Middleware
       ↓
    User Controller
       ↓
    User Service
       ↓
    User Repository
       ↓
    Database
       ↓
    User Repository
       ↓
    User Service
       ↓
    User Controller
       ↓
    HTTP Response

---

# 19. What Should a Controller NOT Do?

Avoid putting heavy business logic inside controllers.

Avoid:

    async function createOrder(req, res) {

      // Validate everything

      // Check inventory

      // Calculate price

      // Apply discount

      // Calculate tax

      // Process payment

      // Update inventory

      // Send email

      // Save order

      // Send response
    }

This creates a large controller.

Instead:

    async function createOrder(req, res, next) {
      try {
        const order = await orderService.createOrder(
          req.user.id,
          req.body
        );

        res.status(201).json(order);
      } catch (error) {
        next(error);
      }
    }

The business logic belongs in the service.

---

# 20. What Should a Service NOT Do?

A service should generally avoid directly depending on HTTP objects.

Avoid:

    async function createUser(req, res) {
      // business logic
    }

This couples the service to Express.

Prefer:

    async function createUser(userData) {
      // business logic
    }

Then the controller handles HTTP:

    const user = await userService.createUser(req.body);

    res.status(201).json(user);

This makes the service easier to reuse and test.

---

# 21. Service Should Not Know About HTTP

Avoid putting things like this in services:

    res.status(404).json(...)

or:

    req.body

or:

    req.params.id

Instead:

    Controller
    → Extracts data from HTTP request

    Service
    → Receives normal application data

Example:

    Controller:

    const user = await userService.getUserById(
      req.params.id
    );

    Service:

    async function getUserById(id) {
      // Business logic
    }

This separation is important.

---

# 22. Controller Should Not Know Database Details

Avoid:

    async function getUser(req, res) {
      const user = await User.findById(req.params.id);

      res.json(user);
    }

This can be acceptable in a very small application.

But in a layered architecture, prefer:

    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database

The controller should not need to know whether the application uses:

    MongoDB
    PostgreSQL
    MySQL
    Redis
    External API

The service/repository layer handles those details.

---

# 23. Thin Controller

A common backend design principle is:

> **Keep controllers thin.**

A thin controller usually looks like:

    async function getUser(req, res, next) {
      try {
        const user = await userService.getUserById(
          req.params.id
        );

        res.json(user);
      } catch (error) {
        next(error);
      }
    }

The controller mainly:

    Extracts input
       ↓
    Calls service
       ↓
    Sends response

---

# 24. Business Logic Example

Suppose there is an order system.

Business rule:

    If order total > ₹5000
    → Give 10% discount

This is business logic.

It belongs in the service:

    async function calculateOrderTotal(items) {
      let total = 0;

      for (const item of items) {
        total += item.price * item.quantity;
      }

      if (total > 5000) {
        total = total * 0.9;
      }

      return total;
    }

The controller should not need to know this rule.

It simply calls:

    orderService.createOrder(...)

---

# 25. Controller and Validation

Basic HTTP-level validation can happen before the controller using validation middleware.

Flow:

    Request
       ↓
    Validation Middleware
       ↓
    Controller
       ↓
    Service

For example:

    validateCreateUser
           ↓
    createUserController
           ↓
    userService.createUser()

This keeps validation separate from business logic.

However, business rules still belong in the service.

Example:

    Input validation
    → Email must be provided

    Business rule
    → A user cannot create more than 5 active orders

These are different concerns.

---

# 26. Error Handling

A common architecture is:

    Controller
       ↓
    Service
       ↓
    Error
       ↓
    Controller calls next(error)
       ↓
    Error Middleware
       ↓
    HTTP Response

Example:

    async function getUser(req, res, next) {
      try {
        const user = await userService.getUserById(
          req.params.id
        );

        res.json(user);
      } catch (error) {
        next(error);
      }
    }

Centralized error middleware can then handle the error.

---

# 27. Why Services Improve Reusability

Suppose two controllers need the same business operation.

For example:

    User Controller
       ↓
    createUser()

    Admin Controller
       ↓
    createUser()

Instead of duplicating the business logic, both can call:

    userService.createUser()

Flow:

    User Controller
          ↓
    User Service
          ↑
    Admin Controller

This improves:

    Reusability
    Maintainability
    Testing
    Consistency

---

# 28. Controller vs Service vs Repository

Remember this table:

    Layer        Responsibility

    Controller
    → HTTP request and response

    Service
    → Business/application logic

    Repository
    → Data access

A simple mental model:

    Controller
    "What HTTP endpoint was called?"

    Service
    "What should the application do?"

    Repository
    "How do I get or save the data?"

---

# 29. Example: Create User

Request:

    POST /users

Body:

    {
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

Flow:

    Request
       ↓
    Validation Middleware
       ↓
    Controller
       ↓
    User Service
       ↓
    User Repository
       ↓
    Database

Controller:

    async function createUser(req, res, next) {
      try {
        const user = await userService.createUser(
          req.body
        );

        res.status(201).json(user);
      } catch (error) {
        next(error);
      }
    }

Service:

    async function createUser(data) {
      const existingUser =
        await userRepository.findByEmail(data.email);

      if (existingUser) {
        throw new Error("User already exists");
      }

      return await userRepository.create(data);
    }

Repository:

    async function create(data) {
      return await User.create(data);
    }

Each layer has a different responsibility.

---

# 30. When You Might Not Need a Service

Not every tiny application needs a service layer.

For a very simple endpoint:

    app.get("/health", (req, res) => {
      res.json({
        status: "OK"
      });
    });

Adding:

    Controller
       ↓
    Service
       ↓
    Repository

would be unnecessary.

For small applications, simpler architecture can be better.

As the application becomes more complex, separating responsibilities becomes more valuable.

---

# 31. Handler in Different Frameworks

Different backend frameworks use different terminology.

For example:

    Express
    → Route Handler / Controller

    NestJS
    → Controller

    Django
    → View / View Function

    Spring
    → Controller

    ASP.NET
    → Controller / Action

The names can differ, but the basic idea is similar:

    Receive Request
       ↓
    Execute Application Logic
       ↓
    Return Response

---

# 32. Handler vs Service — Simple Example

Think about a restaurant.

The **handler/controller** is like the waiter.

The **service** is like the kitchen.

The waiter:

    Takes the order
       ↓
    Sends it to the kitchen
       ↓
    Receives the result
       ↓
    Gives it to the customer

The kitchen:

    Applies rules
       ↓
    Prepares the food
       ↓
    Produces the result

Backend:

    Controller
       ↓
    Service

Controller handles communication.

Service handles the actual application work.

---

# 33. Common Beginner Mistakes

## Mistake 1: Putting all logic in controllers

Avoid huge controllers containing:

    Authentication
    Validation
    Business Rules
    Database Queries
    Payment Logic
    Email Logic

Prefer:

    Middleware
       ↓
    Controller
       ↓
    Service
       ↓
    Repository

---

## Mistake 2: Putting HTTP logic in services

Avoid:

    service(req, res)

Prefer:

    service(userId, data)

The service should generally not depend on Express-specific objects.

---

## Mistake 3: Direct database access everywhere

Avoid:

    Controller
       ↓
    Database

for every complex operation.

Prefer:

    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database

---

## Mistake 4: Creating unnecessary layers

Don't create five layers for a simple:

    GET /health

Architecture should match application complexity.

---

## Mistake 5: Mixing responsibilities

Avoid:

    Controller
    → HTTP
    → Database
    → Payment
    → Email
    → Business Logic
    → Logging
    → Everything

Prefer clear responsibilities.

---

# 34. Best Practice Architecture

A common architecture is:

    Request
       ↓
    Middleware
       ↓
    Route
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database

Each layer has a clear purpose.

### Middleware

    Cross-cutting request processing

Examples:

    Authentication
    Authorization
    Logging
    Validation
    Rate Limiting

### Route

    Maps HTTP method + URL to middleware/controller

### Controller

    Handles HTTP request and response

### Service

    Contains business/application logic

### Repository

    Handles data access

### Database

    Stores data

---

# 35. Complete Mental Model

Memorize this:

    HTTP Request
         ↓
    Middleware
         ↓
    Route
         ↓
    Controller
         ↓
    Service
         ↓
    Repository
         ↓
    Database
         ↓
    Repository
         ↓
    Service
         ↓
    Controller
         ↓
    HTTP Response

Responsibilities:

    Middleware
    → "Can this request continue?"

    Controller
    → "What HTTP operation is being requested?"

    Service
    → "What should the application actually do?"

    Repository
    → "How do we access the data?"

---

# 36. Interview Questions

## Q1. What is a handler?

> **A handler is a function that receives an incoming request and handles the resulting response. In frameworks such as Express, a route handler receives `req` and `res` and processes the request.**

## Q2. What is a controller?

> **A controller is an application layer responsible for handling HTTP requests, extracting input, calling the appropriate application logic, and returning an HTTP response.**

## Q3. What is a service?

> **A service contains application or business logic and coordinates the operations required to complete a business use case.**

## Q4. What is the difference between a controller and a service?

> **A controller belongs to the HTTP layer and handles requests and responses, while a service contains application or business logic and should generally remain independent of HTTP-specific objects.**

## Q5. Why should controllers be thin?

> **Thin controllers keep HTTP handling separate from business logic, making the code easier to test, maintain, and reuse.**

## Q6. Should a service receive `req` and `res`?

> **Generally no. A service should receive the data it needs rather than Express-specific objects such as `req` and `res`.**

## Q7. Where should database logic go?

> **In a layered architecture, database access is commonly placed in a repository or data-access layer, while the service decides what data operations are required.**

## Q8. Can a small application skip the service layer?

> **Yes. For very simple applications or endpoints, adding unnecessary layers can increase complexity. Services become more useful as business logic and application complexity grow.**

---

# 37. Interview Scenario

### Interviewer:

> "Explain the request flow in your backend architecture."

Strong answer:

> **"The request first passes through middleware for concerns such as authentication and validation. The route maps the request to a controller. The controller extracts the HTTP input and calls the appropriate service. The service contains the business logic and coordinates with the repository or data-access layer to interact with the database. The result then returns back through the service and controller, which sends the HTTP response to the client."**

Flow:

    Client
       ↓
    Middleware
       ↓
    Route
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

---

# 38. Quick Revision

    Handler
    → Function that handles a request

    Controller
    → Handles HTTP request/response

    Service
    → Contains business/application logic

    Repository
    → Handles database/data access

    Controller
    → Uses req/res

    Service
    → Should generally not depend on req/res

    Controller
    → Thin

    Service
    → Business logic

    Repository
    → Data access

    Typical flow
    → Request
    → Middleware
    → Route
    → Controller
    → Service
    → Repository
    → Database
    → Response

---

# 39. One-Line Interview Summary

> **Handlers and controllers handle the HTTP layer, while services contain the application's business logic; separating these responsibilities keeps backend code modular, reusable, testable, and maintainable.**