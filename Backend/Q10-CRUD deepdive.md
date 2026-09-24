# Backend Development — CRUD Deep Dive

## 1. What is CRUD?

**CRUD stands for Create, Read, Update, and Delete.**

These are the four   basic operations performed on data in most backend applications.

    C → Create
    R → Read
    U → Update
    D → Delete

For example, in a user management system:

    Create
    → Create a new user

    Read
    → Get users or a specific user

    Update
    → Update user information

    Delete
    → Delete a user

CRUD is one of the most fundamental concepts in backend development.

---

# 2. CRUD and HTTP Methods

CRUD operations are commonly mapped to HTTP methods.

    Create → POST
    Read   → GET
    Update → PUT / PATCH
    Delete → DELETE

Example:

    POST   /users
    GET    /users
    GET    /users/:id
    PUT    /users/:id
    PATCH  /users/:id
    DELETE /users/:id

Mental model:

    POST
    → Create

    GET
    → Read

    PUT / PATCH
    → Update

    DELETE
    → Delete

---

# 3. CRUD Request Flow

A typical CRUD request follows the backend architecture:

    Client
       ↓
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
    Repository / Model
       ↓
    Database
       ↓
    Response
       ↓
    Client

For example:

    POST /users
       ↓
    Authentication
       ↓
    Validation
       ↓
    User Controller
       ↓
    User Service
       ↓
    User Repository
       ↓
    Database
       ↓
    Response

---

# 4. CREATE

The **Create** operation is used to add new data.

HTTP method:

    POST

Example:

    POST /users

Request body:

    {
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

The server receives the data, validates it, applies business rules, and stores it in the database.

Flow:

    Client
       ↓
    POST /users
       ↓
    Validate Input
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database
       ↓
    New User Created
       ↓
    Response

---

# 5. CREATE — Controller

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
       ↓
    Calls service
       ↓
    Returns HTTP response

The controller should not contain all the business logic.

---

# 6. CREATE — Service

Example:

    async function createUser(data) {
      const existingUser =
        await userRepository.findByEmail(data.email);

      if (existingUser) {
        throw new Error("User already exists");
      }

      return await userRepository.create(data);
    }

The service handles business logic such as:

    Check duplicate user
    Check business rules
    Prepare data
    Call repository

---

# 7. CREATE — Database

The repository can handle the actual database operation.

Example:

    async function create(data) {
      return await User.create(data);
    }

The flow becomes:

    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database

---

# 8. CREATE — HTTP Status Code

When a resource is successfully created, the common status code is:

    201 Created

Example:

    HTTP/1.1 201 Created

Response:

    {
      "id": 101,
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

Other possible responses:

    400 → Invalid request
    401 → Not authenticated
    403 → Not authorized
    409 → Conflict
    500 → Server error

For example, if the email already exists:

    409 Conflict

can be appropriate.

---

# 9. READ

The **Read** operation retrieves existing data.

HTTP method:

    GET

There are two common types:

    Get collection
    → GET /users

    Get single resource
    → GET /users/:id

---

# 10. READ — Get All Resources

Request:

    GET /users

Controller:

    async function getUsers(req, res, next) {
      try {
        const users = await userService.getUsers();

        res.status(200).json(users);
      } catch (error) {
        next(error);
      }
    }

Flow:

    GET /users
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database
       ↓
    Users
       ↓
    Response

---

# 11. READ — Get One Resource

Request:

    GET /users/101

The `101` is a route parameter.

Controller:

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

Here:

    req.params.id

contains:

    101

---

# 12. READ — Service

Example:

    async function getUserById(id) {
      const user = await userRepository.findById(id);

      if (!user) {
        throw new Error("User not found");
      }

      return user;
    }

The service can apply business rules before returning the result.

---

# 13. READ — Query Parameters

GET requests commonly use query parameters for filtering, sorting, searching, and pagination.

Example:

    GET /users?role=admin&sort=name

The query parameters are available through:

    req.query

Example:

    req.query.role
    req.query.sort

Possible request:

    GET /products?category=electronics&sort=price

The backend can use these values to build the query.

---

# 14. READ — Pagination

Returning thousands or millions of records in one response is inefficient.

Instead, use pagination.

Example:

    GET /users?page=2&limit=20

Meaning:

    page = 2
    limit = 20

The backend returns only the required records.

A common response structure:

    {
      "data": [...],
      "page": 2,
      "limit": 20,
      "total": 150
    }

Pagination improves:

    Performance
    Response size
    Database load
    User experience

---

# 15. READ — Filtering

Filtering allows clients to request only specific records.

Example:

    GET /products?category=mobile

The backend may generate a database query equivalent to:

    category = "mobile"

Another example:

    GET /users?role=admin

Only admin users are returned.

---

# 16. READ — Sorting

Sorting allows clients to control the order of results.

Example:

    GET /products?sort=price

or:

    GET /products?sort=-price

A common convention is:

    price
    → Ascending

    -price
    → Descending

The exact convention depends on the API design.

---

# 17. UPDATE

The **Update** operation modifies existing data.

HTTP methods commonly used:

    PUT
    PATCH

Both are used for updates, but they have different semantics.

---

# 18. PUT

**PUT generally represents replacing the resource with a new representation.**

Example:

    PUT /users/101

Request:

    {
      "name": "Lokendra Singh",
      "email": "lokendra@example.com"
    }

Conceptually:

    Existing Resource
          ↓
    Replace with
          ↓
    New Representation

PUT is generally used when the client sends the complete representation of the resource.

---

# 19. PATCH

**PATCH generally represents a partial update.**

Example:

    PATCH /users/101

Request:

    {
      "name": "Lokendra Singh"
    }

Only the name is changed.

The other fields remain unchanged.

Mental model:

    PUT
    → Replace the resource representation

    PATCH
    → Partially modify the resource

---

# 20. UPDATE — Controller

Example:

    async function updateUser(req, res, next) {
      try {
        const user = await userService.updateUser(
          req.params.id,
          req.body
        );

        res.status(200).json(user);
      } catch (error) {
        next(error);
      }
    }

The controller:

    Gets ID from req.params
       ↓
    Gets update data from req.body
       ↓
    Calls service
       ↓
    Sends response

---

# 21. UPDATE — Service

Example:

    async function updateUser(id, data) {
      const user = await userRepository.findById(id);

      if (!user) {
        throw new Error("User not found");
      }

      return await userRepository.update(id, data);
    }

The service can perform:

    Resource existence check
    Business rule validation
    Permission checks
    Data transformation
    Update operation

---

# 22. UPDATE — Important Considerations

When updating data, consider:

    Does the resource exist?
    Is the user authorized?
    Is the new data valid?
    Are business rules satisfied?
    Should fields be immutable?
    Should updatedAt change?
    Is the operation atomic?
    Could two requests update the same resource?

For example:

    User ID = 101
    User does not exist

The API should not silently pretend the update succeeded.

---

# 23. DELETE

The **Delete** operation removes a resource.

HTTP method:

    DELETE

Example:

    DELETE /users/101

Flow:

    Client
       ↓
    DELETE /users/101
       ↓
    Authentication
       ↓
    Authorization
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

# 24. DELETE — Controller

Example:

    async function deleteUser(req, res, next) {
      try {
        await userService.deleteUser(
          req.params.id
        );

        res.status(204).send();
      } catch (error) {
        next(error);
      }
    }

A successful delete can commonly return:

    204 No Content

This means the request succeeded and there is no response body.

---

# 25. DELETE — Service

Example:

    async function deleteUser(id) {
      const user = await userRepository.findById(id);

      if (!user) {
        throw new Error("User not found");
      }

      await userRepository.delete(id);
    }

The service can check whether the resource exists and whether the deletion is allowed.

---

# 26. Hard Delete vs Soft Delete

There are two common deletion strategies.

### Hard Delete

The record is permanently removed from the database.

Example:

    DELETE FROM users
    WHERE id = 101;

The data is physically deleted.

### Soft Delete

The record remains in the database but is marked as deleted.

Example:

    {
      "id": 101,
      "name": "Lokendra",
      "deletedAt": "2026-08-26T10:00:00Z"
    }

Queries can then exclude deleted records.

Mental model:

    Hard Delete
    → Remove data

    Soft Delete
    → Mark data as deleted

Soft delete is useful when:

    Data needs to be restored
    Audit history is important
    Regulations require retention
    Accidental deletion must be recoverable

---

# 27. CRUD and RESTful API Design

A REST-style user API might look like:

    POST   /users
    → Create user

    GET    /users
    → Get all users

    GET    /users/:id
    → Get one user

    PUT    /users/:id
    → Replace user

    PATCH  /users/:id
    → Partially update user

    DELETE /users/:id
    → Delete user

Notice that the URL represents the resource:

    /users

rather than an action:

    /createUser
    /getUsers
    /deleteUser

RESTful design generally uses HTTP methods to represent the operation.

---

# 28. CRUD and Status Codes

Common status codes:

    CREATE

    201 Created
    → Resource successfully created

    400 Bad Request
    → Invalid request

    409 Conflict
    → Resource conflicts with existing state


    READ

    200 OK
    → Successful retrieval

    404 Not Found
    → Resource does not exist


    UPDATE

    200 OK
    → Update succeeded and response contains data

    204 No Content
    → Update succeeded without response body

    404 Not Found
    → Resource does not exist


    DELETE

    204 No Content
    → Delete succeeded

    404 Not Found
    → Resource does not exist

---

# 29. CRUD Validation

Every CRUD operation should validate input.

Example:

    POST /users

Input:

    {
      "name": "",
      "email": "invalid"
    }

The backend should validate:

    Is name present?
    Is email valid?
    Is email unique?
    Are values within allowed limits?

Validation should happen before invalid data reaches the database.

Flow:

    Request
       ↓
    Validation
       ↓
    Controller
       ↓
    Service
       ↓
    Database

---

# 30. CRUD and Authentication

CRUD operations often require authentication.

For example:

    GET /profile
    → Logged-in user

    POST /orders
    → Authenticated user

    DELETE /users/:id
    → Admin or authorized user

Flow:

    Request
       ↓
    Authentication
       ↓
    Authorization
       ↓
    CRUD Operation

Authentication answers:

    "Who are you?"

Authorization answers:

    "Are you allowed to perform this operation?"

---

# 31. CRUD and Authorization

Different users may have different CRUD permissions.

Example:

    Admin
    → Create
    → Read
    → Update
    → Delete

    Manager
    → Read
    → Update

    Regular User
    → Read

This can be implemented using:

    Roles
    Permissions
    RBAC
    ABAC

Example:

    DELETE /users/101

Before deleting:

    Is user authenticated?
       ↓
    Is user authorized?
       ↓
    Can this user delete user 101?
       ↓
    Perform delete

---

# 32. CRUD and Transactions

Some CRUD operations involve multiple database operations.

Example:

    Create Order
       ↓
    Create Order Record
       ↓
    Reduce Product Inventory
       ↓
    Create Payment Record

What if inventory update fails after the order is created?

We could end up with inconsistent data.

A database transaction can help:

    BEGIN TRANSACTION
       ↓
    Create Order
       ↓
    Update Inventory
       ↓
    Create Payment Record
       ↓
    COMMIT

If something fails:

    ROLLBACK

Mental model:

    All operations succeed
    → COMMIT

    Something fails
    → ROLLBACK

Transactions are especially important when multiple related database changes must remain consistent.

---

# 33. CRUD and Idempotency

Idempotency means that making the same request multiple times produces the same intended result.

GET is generally idempotent.

PUT is designed to be idempotent.

DELETE is generally idempotent in its intended effect.

POST is generally not idempotent.

Example:

    PUT /users/101

with:

    {
      "name": "Lokendra"
    }

Sending the same request multiple times should leave the resource in the same state.

But:

    POST /orders

may create a new order every time it is sent.

For important operations such as payments or order creation, idempotency mechanisms may be required.

---

# 34. CRUD and Concurrency

Multiple users can update the same resource at the same time.

Example:

    User A
    → Reads balance = 1000

    User B
    → Reads balance = 1000

Both then update the balance.

This can cause a race condition or lost update depending on how the operation is implemented.

Possible solutions include:

    Transactions
    Optimistic locking
    Pessimistic locking
    Atomic database operations
    Version fields

Concurrency becomes especially important in high-traffic systems.

---

# 35. CRUD and Database Constraints

Application validation is useful, but database constraints are also important.

For example:

    email must be unique

Application:

    Check whether email exists

Database:

    UNIQUE constraint

The database constraint provides a final layer of protection against race conditions and invalid state.

Mental model:

    Application Validation
          +
    Database Constraints
          ↓
    Data Integrity

---

# 36. CRUD and Error Handling

CRUD operations can fail for many reasons.

Examples:

    Invalid input
    Authentication failure
    Authorization failure
    Resource not found
    Duplicate resource
    Database failure
    Network failure
    Unexpected server error

A good API should return meaningful status codes.

Example:

    User does not exist
    → 404 Not Found

    User is not authenticated
    → 401 Unauthorized

    User is authenticated but not allowed
    → 403 Forbidden

    Duplicate email
    → 409 Conflict

    Invalid input
    → 400 Bad Request

---

# 37. CRUD Response Design

Keep API responses consistent.

Example:

    {
      "data": {
        "id": 101,
        "name": "Lokendra"
      }
    }

For errors:

    {
      "error": {
        "code": "USER_NOT_FOUND",
        "message": "User not found"
      }
    }

Consistency makes APIs easier for frontend developers to consume.

---

# 38. CRUD Example — Complete User API

Routes:

    POST   /users
    GET    /users
    GET    /users/:id
    PATCH  /users/:id
    DELETE /users/:id

Architecture:

    Routes
       ↓
    Controllers
       ↓
    Services
       ↓
    Repositories
       ↓
    Database

Create:

    POST /users
       ↓
    createUser()
       ↓
    userService.createUser()
       ↓
    userRepository.create()

Read:

    GET /users/:id
       ↓
    getUser()
       ↓
    userService.getUserById()
       ↓
    userRepository.findById()

Update:

    PATCH /users/:id
       ↓
    updateUser()
       ↓
    userService.updateUser()
       ↓
    userRepository.update()

Delete:

    DELETE /users/:id
       ↓
    deleteUser()
       ↓
    userService.deleteUser()
       ↓
    userRepository.delete()

---

# 39. CRUD Security Checklist

For every CRUD endpoint, consider:

    Authentication
    Authorization
    Input validation
    Input sanitization where appropriate
    Rate limiting
    Database constraints
    Error handling
    Sensitive data exposure
    Logging
    Audit requirements

Never assume that hiding a frontend button provides security.

For example:

    Frontend
    → Hide Delete button

does NOT provide real authorization.

The backend must still check:

    Is the user allowed to delete this resource?

---

# 40. CRUD Performance Considerations

For large applications, CRUD operations should also consider performance.

Important techniques include:

    Database indexes
    Pagination
    Efficient queries
    Selecting only required fields
    Caching
    Connection pooling
    Batch operations
    Avoiding N+1 queries

For example, this can be expensive:

    GET /users

if the database contains millions of users.

Instead:

    GET /users?page=1&limit=20

can return only the required records.

---

# 41. CRUD and N+1 Query Problem

Suppose we fetch 100 users and then fetch orders for every user separately.

This can produce:

    1 query → Get users
    100 queries → Get orders

Total:

    101 queries

This is the N+1 query problem.

Better approaches may include:

    JOINs
    Population
    Eager loading
    Batch queries
    Data loaders

The exact solution depends on the database and architecture.

---

# 42. CRUD Best Practices

Follow these principles:

    1. Use correct HTTP methods.

    2. Use resource-oriented URLs.

    3. Keep controllers thin.

    4. Put business logic in services.

    5. Keep database access separated when useful.

    6. Validate input.

    7. Authenticate protected endpoints.

    8. Authorize every sensitive operation.

    9. Use appropriate HTTP status codes.

    10. Handle errors consistently.

    11. Use pagination for large collections.

    12. Use database constraints for data integrity.

    13. Use transactions when multiple changes must succeed together.

    14. Consider idempotency for important operations.

    15. Consider concurrency for frequently updated resources.

---

# 43. CRUD Mental Model

Memorize this:

    CREATE
    POST /users
       ↓
    Validate
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database


    READ
    GET /users/:id
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database


    UPDATE
    PATCH /users/:id
       ↓
    Validate
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database


    DELETE
    DELETE /users/:id
       ↓
    Authorization
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database

---

# 44. Interview Questions

## Q1. What is CRUD?

> **CRUD stands for Create, Read, Update, and Delete. These are the four fundamental operations for managing resources in an application.**

## Q2. Which HTTP methods are commonly used for CRUD?

> **POST is commonly used for Create, GET for Read, PUT or PATCH for Update, and DELETE for Delete.**

## Q3. What is the difference between PUT and PATCH?

> **PUT generally represents replacing a resource representation, while PATCH is used for partial modifications to a resource.**

## Q4. What status code would you return after successfully creating a resource?

> **201 Created.**

## Q5. What status code would you return after successfully deleting a resource with no response body?

> **204 No Content.**

## Q6. What is soft delete?

> **Soft delete means marking a record as deleted instead of physically removing it from the database, for example by storing a `deletedAt` timestamp.**

## Q7. Why is pagination important for CRUD APIs?

> **Pagination prevents the API from returning very large datasets at once, which reduces response size, database load, and memory usage.**

## Q8. Why are database constraints important if we already validate data in the backend?

> **Application validation improves user feedback, while database constraints provide a final layer of data integrity and help protect against race conditions.**

## Q9. What is idempotency?

> **An operation is idempotent when repeating the same request results in the same intended resource state. GET, PUT, and DELETE are generally designed to be idempotent, while POST usually is not.**

## Q10. When would you use a database transaction?

> **When multiple related database operations need to succeed or fail together to maintain consistency.**

---

# 45. Interview Scenario

### Interviewer:

> "Design CRUD APIs for a user resource."

A strong answer:

    POST   /users
    → Create user

    GET    /users
    → Get users

    GET    /users/:id
    → Get a specific user

    PATCH  /users/:id
    → Partially update user

    DELETE /users/:id
    → Delete user

Architecture:

    Request
       ↓
    Middleware
       ↓
    Controller
       ↓
    Service
       ↓
    Repository
       ↓
    Database

I would also consider:

    Input validation
    Authentication
    Authorization
    Pagination
    Error handling
    Database constraints
    Transactions where required
    Idempotency for critical operations
    Concurrency handling

---

# 46. Quick Revision

    CRUD
    → Create
    → Read
    → Update
    → Delete

    Create
    → POST

    Read
    → GET

    Update
    → PUT / PATCH

    Delete
    → DELETE

    POST
    → Usually not idempotent

    PUT
    → Generally idempotent
    → Full replacement semantics

    PATCH
    → Partial update

    DELETE
    → Generally idempotent

    Create success
    → 201 Created

    Read success
    → 200 OK

    Update success
    → 200 OK / 204 No Content

    Delete success
    → 204 No Content

    Not found
    → 404

    Not authenticated
    → 401

    Not authorized
    → 403

    Conflict
    → 409

    CRUD flow
    → Route
    → Controller
    → Service
    → Repository
    → Database

---

# 47. One-Line Interview Summary

> **CRUD represents Create, Read, Update, and Delete operations, typically implemented using POST, GET, PUT/PATCH, and DELETE, with proper validation, authorization, error handling, database integrity, and performance considerations.**
