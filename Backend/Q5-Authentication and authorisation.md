# Backend Development — Authentication & Authorization

## 1. The Big Picture

Whenever a user interacts with a protected backend API, two questions need to be answered:

```text
1. Who are you?
2. What are you allowed to do?
```

These correspond to:

```text
Authentication → Who are you?
Authorization  → What are you allowed to do?
```

A simple flow:

```text
Client
   ↓
Login
   ↓
Authentication
   ↓
Identity established
   ↓
Authorization
   ↓
Permission checked
   ↓
Protected Resource
```

---

# 2. Authentication

**Authentication** is the process of verifying the identity of a user or system.

In simple words:

> **Authentication answers: "Who are you?"**

For example, a user enters:

```text
Email: lokendra@example.com
Password: ********
```

The backend verifies the credentials.

If they are correct:

```text
Authentication successful
```

If they are incorrect:

```text
Authentication failed
```

---

# 3. Authorization

**Authorization** determines what an authenticated user is allowed to access or perform.

In simple words:

> **Authorization answers: "What are you allowed to do?"**

For example:

```text
User:
    Can view products
    Can create orders
    Cannot delete users

Admin:
    Can view products
    Can create orders
    Can delete users
```

So:

```text
Authentication
      ↓
Who are you?
      ↓
Authorization
      ↓
What can you do?
```

---

# 4. Authentication vs Authorization

This is one of the most common interview questions.

| Authentication          | Authorization                         |
| ----------------------- | ------------------------------------- |
| Verifies identity       | Checks permissions                    |
| "Who are you?"          | "What can you do?"                    |
| Happens first           | Happens after identity is established |
| Uses credentials/tokens | Uses roles/permissions/policies       |
| Example: Login          | Example: Admin-only API               |

### Easy example

Imagine entering an office.

```text
Security Guard:
"Show me your ID."
        ↓
Authentication
        ↓
"Okay, you are Lokendra."
        ↓
"Are you allowed into this room?"
        ↓
Authorization
```

---

# 5. Real-World Backend Flow

Suppose we have:

```text
GET /api/admin/users
```

The request might go through:

```text
Client
  ↓
Authentication
  ↓
Identify user
  ↓
Authorization
  ↓
Check admin permission
  ↓
Controller
  ↓
Service
  ↓
Database
  ↓
Response
```

If the user is not authenticated:

```text
401 Unauthorized
```

If the user is authenticated but doesn't have permission:

```text
403 Forbidden
```

These two status codes are extremely important.

---

# 6. 401 vs 403

## 401 Unauthorized

Usually means:

> The request does not have valid authentication credentials.

Examples:

```text
No token
Invalid token
Expired token
Invalid credentials
```

Example:

```http
GET /api/profile
Authorization: missing
```

Response:

```http
401 Unauthorized
```

---

## 403 Forbidden

Usually means:

> The user is authenticated, but doesn't have permission to perform the requested action.

Example:

```text
User role = USER

Endpoint:
DELETE /api/users/101

Required:
ADMIN
```

The user is authenticated.

But they don't have the required permission.

Response:

```http
403 Forbidden
```

Remember:

```text
401 → Authentication problem
403 → Authorization problem
```

---

# 7. Login Flow

Let's understand a typical login process.

User sends:

```http
POST /api/auth/login
```

Body:

```json
{
  "email": "lokendra@example.com",
  "password": "mypassword"
}
```

Backend:

```text
Receive credentials
      ↓
Validate input
      ↓
Find user
      ↓
Verify password
      ↓
Create authentication session/token
      ↓
Return authentication information
```

Conceptually:

```text
Client
  ↓
Email + Password
  ↓
Backend
  ↓
Database
  ↓
Verify credentials
  ↓
Create session/token
  ↓
Client
```

---

# 8. Never Store Plaintext Passwords

This is extremely important.

Never store:

```text
password = "mypassword"
```

directly in your database.

Instead, passwords should be stored using a **password hashing algorithm** designed for password storage.

Common choices include:

```text
Argon2
bcrypt
scrypt
```

For Node.js applications, `bcrypt` and Argon2 implementations are commonly encountered.

---

# 9. Password Hashing

Suppose the user enters:

```text
mypassword
```

The backend hashes it:

```text
mypassword
    ↓
Password Hashing
    ↓
$2b$10$................
```

Database:

```text
email: lokendra@example.com
passwordHash: $2b$10$................
```

The original password is not stored.

---

# 10. Why Hash Passwords?

If an attacker obtains the database, plaintext passwords would immediately be exposed.

With properly hashed passwords:

```text
Database
   ↓
Password Hash
```

The attacker does not directly get the original password.

However:

> Password hashing is not encryption.

Password hashing is designed to be difficult to reverse and resistant to guessing attacks.

---

# 11. Password Verification

Suppose the database contains:

```text
passwordHash
```

The user enters:

```text
mypassword
```

The backend doesn't simply compare:

```js
user.password === password
```

Instead, a password hashing library verifies the entered password against the stored hash.

Conceptually:

```text
Entered Password
       ↓
Password Verification
       ↓
Stored Password Hash
       ↓
Match?
```

Example with bcrypt:

```js
const bcrypt = require("bcrypt");

const password = "mypassword";

const hash = await bcrypt.hash(password, 10);

const isValid = await bcrypt.compare(
  password,
  hash
);

console.log(isValid);
```

Output:

```text
true
```

---

# 12. Authentication After Login

Once the user successfully logs in, the backend needs a way to recognize that user on subsequent requests.

Common approaches include:

```text
1. Session-based authentication
2. Token-based authentication
```

Two important concepts:

```text
Sessions
JWTs
```

---

# 13. Session-Based Authentication

In session-based authentication, the server maintains session state.

Typical flow:

```text
Client
  ↓
POST /login
  ↓
Backend verifies credentials
  ↓
Server creates session
  ↓
Session ID sent to client
  ↓
Client sends session ID on future requests
  ↓
Server looks up session
  ↓
User identified
```

For example:

```text
Browser
   │
   │ Cookie: sessionId=abc123
   ▼
Backend
   │
   │ Lookup session
   ▼
Session Store
   │
   ▼
User 101
```

---

# 14. Cookies

A browser commonly stores a session identifier or authentication token in a cookie.

Example:

```http
Set-Cookie: sessionId=abc123
```

Then subsequent requests may contain:

```http
Cookie: sessionId=abc123
```

The browser can automatically send cookies to the appropriate domain/path according to cookie rules.

---

# 15. Token-Based Authentication

Another common approach is token-based authentication.

Flow:

```text
Client
   ↓
Login
   ↓
Backend verifies credentials
   ↓
Backend issues token
   ↓
Client stores token
   ↓
Client sends token with future requests
   ↓
Backend verifies token
   ↓
User identified
```

Example:

```http
Authorization: Bearer <token>
```

---

# 16. JWT

JWT stands for:

> **JSON Web Token**

It is a common token format used for authentication and authorization.

A JWT commonly contains three parts:

```text
Header.Payload.Signature
```

For example:

```text
xxxxx.yyyyy.zzzzz
```

---

# 17. JWT Structure

A JWT contains:

```text
Header
Payload
Signature
```

Conceptually:

```text
JWT
│
├── Header
├── Payload
└── Signature
```

### Header

Contains information such as the token type and signing algorithm.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload

Contains claims.

Example:

```json
{
  "sub": "101",
  "role": "user"
}
```

### Signature

Used to verify that the token was signed by a trusted party and has not been modified.

---

# 18. Important JWT Security Concept

A JWT is generally **signed, not encrypted**.

That means you should not put sensitive secrets inside the payload simply because the token is a JWT.

For example, don't put:

```json
{
  "password": "mypassword"
}
```

inside a JWT.

The payload can generally be decoded by anyone who has the token.

The signature provides integrity/authenticity verification, not confidentiality.

---

# 19. JWT Authentication Flow

Example:

```text
POST /login
     ↓
Email + Password
     ↓
Backend verifies credentials
     ↓
Generate JWT
     ↓
Return JWT
     ↓
Client
```

Then:

```http
GET /profile
Authorization: Bearer <JWT>
```

Backend:

```text
Receive JWT
    ↓
Verify signature
    ↓
Check expiration
    ↓
Read claims
    ↓
Identify user
    ↓
Authorization
    ↓
Controller
```

---

# 20. Node.js JWT Example

Using the `jsonwebtoken` package:

```js
const jwt = require("jsonwebtoken");

const token = jwt.sign(
  {
    sub: "101",
    role: "user"
  },
  process.env.JWT_SECRET,
  {
    expiresIn: "15m"
  }
);

console.log(token);
```

The backend can later verify it:

```js
const decoded = jwt.verify(
  token,
  process.env.JWT_SECRET
);

console.log(decoded);
```

Example decoded claims:

```js
{
  sub: "101",
  role: "user",
  iat: 1787560000,
  exp: 1787560900
}
```

---

# 21. What is `sub`?

`sub` is a standard JWT claim meaning:

> **Subject**

It commonly identifies the entity the token represents.

For example:

```json
{
  "sub": "101"
}
```

could mean:

```text
User ID = 101
```

---

# 22. What is `exp`?

`exp` means:

> **Expiration Time**

Example:

```json
{
  "sub": "101",
  "exp": 1787560900
}
```

After the expiration time, the token should no longer be accepted.

Short-lived access tokens are commonly used to reduce the impact of token theft.

---

# 23. Authentication Middleware

Authentication is often implemented as middleware.

Example:

```js
const jwt = require("jsonwebtoken");

function authenticate(req, res, next) {
  const authHeader = req.headers.authorization;

  if (!authHeader) {
    return res.status(401).json({
      message: "Authentication required"
    });
  }

  const token = authHeader.split(" ")[1];

  try {
    const decoded = jwt.verify(
      token,
      process.env.JWT_SECRET
    );

    req.user = decoded;

    next();
  } catch (error) {
    return res.status(401).json({
      message: "Invalid or expired token"
    });
  }
}
```

Then:

```js
app.get(
  "/api/profile",
  authenticate,
  (req, res) => {
    res.json({
      user: req.user
    });
  }
);
```

Flow:

```text
GET /api/profile
       ↓
authenticate()
       ↓
Token valid?
   ┌───┴───┐
  NO      YES
   ↓        ↓
  401     req.user
             ↓
          Handler
```

---

# 24. Authorization Middleware

Authentication tells us who the user is.

Authorization checks what the user can do.

Example:

```js
function requireAdmin(req, res, next) {
  if (req.user.role !== "admin") {
    return res.status(403).json({
      message: "Admin access required"
    });
  }

  next();
}
```

Route:

```js
app.delete(
  "/api/users/:id",
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
Who is the user?
   ↓
Authorization
   ↓
Is user an admin?
   ↓
Controller
```

---

# 25. Authentication + Authorization Together

A real route may look like:

```js
router.delete(
  "/users/:id",
  authenticate,
  requireAdmin,
  deleteUser
);
```

This means:

```text
1. Authenticate the user
2. Check admin permission
3. Delete the user
```

---

# 26. Role-Based Access Control (RBAC)

RBAC stands for:

> **Role-Based Access Control**

Instead of giving permissions individually to every user, we assign users roles.

Example:

```text
USER
ADMIN
MANAGER
SUPPORT
```

Then define permissions:

```text
USER
 ├── read_products
 └── create_order

ADMIN
 ├── read_products
 ├── create_order
 ├── delete_user
 └── manage_products
```

Example:

```js
req.user.role === "admin"
```

---

# 27. Permission-Based Authorization

Sometimes checking only the role isn't enough.

Instead of:

```text
role = admin
```

we can use permissions:

```text
permissions:
[
  "users:read",
  "users:create",
  "users:delete"
]
```

Then:

```js
if (!req.user.permissions.includes("users:delete")) {
  return res.status(403).json({
    message: "Permission denied"
  });
}
```

This gives more granular access control.

---

# 28. RBAC vs Permission-Based Access Control

### RBAC

```text
User
 ↓
Role
 ↓
Permissions
```

Example:

```text
Lokendra
   ↓
Admin
   ↓
users:delete
```

### Permission-based

The system directly evaluates permissions.

```text
User
 ↓
Permissions
 ↓
users:delete?
```

Many real applications combine roles and permissions.

---

# 29. Resource Ownership

Authorization isn't always about roles.

Sometimes the question is:

> Does this resource belong to this user?

Example:

```text
GET /users/101/orders/500
```

Suppose:

```text
Logged-in user = 101
Order 500 belongs to user 101
```

Access:

```text
Allowed
```

But:

```text
Logged-in user = 202
Order 500 belongs to user 101
```

Access:

```text
Forbidden
```

This is often called **resource-level authorization** or an ownership check.

---

# 30. Example: Update Profile

Suppose:

```text
PATCH /users/101
```

The logged-in user is:

```text
userId = 101
```

We can check:

```js
if (req.user.sub !== req.params.id) {
  return res.status(403).json({
    message: "You cannot modify this user"
  });
}
```

The important principle is:

> Never trust a user simply because they are authenticated. Always check whether they are authorized to access the requested resource.

---

# 31. Authentication Methods

Authentication can be implemented in several ways.

Common mechanisms include:

```text
Username + Password
Session Cookies
JWT
OAuth 2.0
OpenID Connect
API Keys
mTLS
```

These solve different problems.

For user login applications, you will commonly encounter:

```text
Session-based authentication
JWT-based authentication
OAuth / OpenID Connect
```

---

# 32. OAuth 2.0

OAuth 2.0 is primarily an **authorization framework**.

It allows an application to obtain limited access to resources on behalf of a user without obtaining the user's password.

Example:

```text
"Continue with Google"
```

Your application may use Google's identity platform and OAuth/OIDC mechanisms.

---

# 33. OpenID Connect

OpenID Connect (OIDC) is an identity layer built on top of OAuth 2.0.

It is commonly used for:

```text
Login with Google
Login with Microsoft
Enterprise SSO
```

Simple distinction:

```text
OAuth 2.0
→ Delegated authorization

OpenID Connect
→ Authentication / identity on top of OAuth 2.0
```

This distinction is commonly tested in interviews.

---

# 34. API Keys

API keys are commonly used for machine-to-machine or developer API access.

Example:

```http
X-API-Key: abc123
```

The backend checks the key:

```text
API Request
    ↓
API Key
    ↓
Valid?
    ↓
Allow / Reject
```

API keys are generally not a replacement for user authentication systems in every scenario.

---

# 35. Access Token vs Refresh Token

Modern authentication systems often use:

```text
Access Token
Refresh Token
```

### Access Token

Used to access protected APIs.

Usually short-lived.

Example:

```text
15 minutes
```

### Refresh Token

Used to obtain a new access token.

Usually longer-lived and should be protected carefully.

Flow:

```text
Login
  ↓
Access Token + Refresh Token
  ↓
Access Token expires
  ↓
Refresh Token
  ↓
New Access Token
```

---

# 36. Why Short-Lived Access Tokens?

Suppose an access token is valid for:

```text
30 days
```

If it is stolen, the attacker may be able to use it for a long time.

Instead:

```text
Access Token
     ↓
15 minutes
```

If stolen, its useful lifetime is reduced.

A refresh mechanism can allow the legitimate user to continue without logging in every few minutes.

The exact design depends on the application and threat model.

---

# 37. Where Should Tokens Be Stored?

This is an important security topic.

For browser applications, token storage needs careful consideration.

One common approach is to use:

```text
HttpOnly
Secure
SameSite
```

cookies for sensitive session/authentication credentials.

### HttpOnly

Helps prevent JavaScript from reading the cookie.

### Secure

Cookie should only be sent over HTTPS.

### SameSite

Controls when cookies are sent in cross-site contexts and can help mitigate CSRF in appropriate configurations.

There is no universal "store every token here" rule; the correct design depends on the authentication architecture and threat model.

---

# 38. XSS and Token Storage

Suppose an authentication token is accessible to JavaScript:

```js
localStorage.getItem("token")
```

If an attacker successfully executes malicious JavaScript through an XSS vulnerability, that token may potentially be stolen.

HttpOnly cookies can reduce this particular token-theft risk because JavaScript cannot directly read them.

However:

> HttpOnly cookies do not automatically eliminate all authentication risks.

You still need to consider:

```text
XSS
CSRF
CORS
Session fixation
Token theft
Credential stuffing
Brute force
```

---

# 39. CSRF

CSRF stands for:

> **Cross-Site Request Forgery**

It is primarily relevant to browser-based authentication mechanisms where credentials such as cookies are automatically attached to requests.

A malicious website may attempt to cause the victim's browser to send an authenticated request to another site.

Defenses can include:

```text
SameSite cookies
CSRF tokens
Origin/Referer validation
Proper request design
```

The exact defense depends on your architecture.

---

# 40. CORS vs Authentication

These are often confused.

### Authentication

Answers:

```text
Who is the user?
```

### CORS

Controls which browser origins are allowed to make certain cross-origin requests.

CORS does **not** authenticate users.

For example:

```text
Authentication:
"Is this request from user 101?"
```

CORS:

```text
"Is this browser origin allowed to make this cross-origin request?"
```

They solve different problems.

---

# 41. Session vs JWT

This is a common interview comparison.

| Session                            | JWT                                                             |
| ---------------------------------- | --------------------------------------------------------------- |
| Server maintains session state     | Token contains claims                                           |
| Client typically sends session ID  | Client sends token                                              |
| Session store required             | Token verification can be stateless                             |
| Easy server-side invalidation      | Immediate revocation can be more complex                        |
| Common with cookies                | Common with Authorization headers or cookies                    |
| Good for many traditional web apps | Useful for APIs/distributed systems when designed appropriately |

Important:

> JWT is not automatically better than sessions.

Choose based on the application's requirements.

---

# 42. JWT Is Not Automatically Stateless Everywhere

People often say:

> "JWT means no server state."

That's not always true.

You might still maintain state for:

```text
Refresh tokens
Token revocation
Session tracking
Device management
Logout
Risk detection
```

So the more accurate statement is:

> Access-token validation using self-contained JWTs can be stateless, but the overall authentication system may still maintain server-side state.

---

# 43. Logout

With session authentication:

```text
Logout
  ↓
Destroy/invalidate session
```

With JWT access tokens, logout is more complicated if the token is still valid.

Common strategies include:

```text
Short-lived access tokens
Refresh-token revocation
Session/device tracking
Token deny lists in special cases
```

The exact strategy depends on the system.

---

# 44. Authentication Middleware Flow

A typical Node.js backend:

```text
Request
   ↓
Extract credentials/token
   ↓
Verify credentials/token
   ↓
Identify user
   ↓
Attach user to request context
   ↓
Authorization middleware
   ↓
Controller
```

Example:

```js
router.get(
  "/admin/dashboard",
  authenticate,
  requireAdmin,
  getDashboard
);
```

---

# 45. Complete Node.js Example

A simplified example:

```js
const express = require("express");
const jwt = require("jsonwebtoken");

const app = express();

app.use(express.json());

function authenticate(req, res, next) {
  const authHeader = req.headers.authorization;

  if (!authHeader) {
    return res.status(401).json({
      message: "Authentication required"
    });
  }

  const token = authHeader.split(" ")[1];

  try {
    const user = jwt.verify(
      token,
      process.env.JWT_SECRET
    );

    req.user = user;

    next();
  } catch {
    return res.status(401).json({
      message: "Invalid or expired token"
    });
  }
}

function requireAdmin(req, res, next) {
  if (req.user.role !== "admin") {
    return res.status(403).json({
      message: "Admin access required"
    });
  }

  next();
}

app.get(
  "/admin/users",
  authenticate,
  requireAdmin,
  (req, res) => {
    res.json({
      message: "Admin users data"
    });
  }
);

app.listen(5000);
```

Flow:

```text
GET /admin/users
       ↓
authenticate
       ↓
JWT valid?
   ┌───┴───┐
  NO      YES
   ↓        ↓
  401    requireAdmin
             ↓
        role = admin?
         ┌───┴───┐
        NO      YES
         ↓        ↓
        403    Controller
```

---

# 46. Important Security Principles

### Never store plaintext passwords

Use a password hashing algorithm such as:

```text
Argon2
bcrypt
scrypt
```

---

### Never put sensitive secrets inside JWT payloads

JWT payloads should not be treated as confidential.

---

### Use HTTPS

Authentication credentials and tokens should be protected in transit using TLS/HTTPS.

---

### Validate authentication input

Don't blindly trust:

```text
email
password
token
user ID
role
```

---

### Don't trust client-provided roles

This is dangerous:

```json
{
  "role": "admin"
}
```

The client should not be able to simply declare itself an admin.

The backend must determine the user's permissions from trusted server-side data.

---

# 47. Common Beginner Mistakes

## Mistake 1: Authentication = Authorization

Wrong.

```text
Authentication → Who are you?
Authorization  → What can you do?
```

---

## Mistake 2: JWT = Encryption

Wrong.

JWTs are generally signed, not encrypted.

---

## Mistake 3: Password hashing = Encryption

Wrong.

Passwords should generally be stored using password hashing algorithms, not reversible encryption.

---

## Mistake 4: Trusting the frontend

Never assume:

```js
if (frontendSaysUserIsAdmin)
```

The backend must enforce authorization.

---

## Mistake 5: Returning 403 for every authentication failure

Usually:

```text
Missing/invalid authentication → 401
Authenticated but forbidden → 403
```

---

## Mistake 6: Storing passwords directly

Never:

```js
password: "mypassword"
```

Store an appropriate password hash instead.

---

# 48. Interview Questions

## Q1. What is authentication?

> Authentication is the process of verifying the identity of a user or system.

---

## Q2. What is authorization?

> Authorization is the process of determining whether an authenticated user has permission to perform a specific action or access a specific resource.

---

## Q3. Authentication vs authorization?

> Authentication determines who the user is, while authorization determines what that user is allowed to do.

---

## Q4. What is the difference between 401 and 403?

> 401 indicates that valid authentication credentials are missing or invalid, while 403 indicates that the user is authenticated but doesn't have sufficient permission to access the resource.

---

## Q5. Why shouldn't passwords be stored directly?

> Plaintext passwords create a major security risk if the database is compromised. Passwords should be stored using a slow, password-specific hashing algorithm such as Argon2, bcrypt, or scrypt.

---

## Q6. Is JWT encrypted?

> No. A standard JWT is signed rather than encrypted. Its payload can generally be decoded, so sensitive confidential information should not be placed inside it.

---

## Q7. What is RBAC?

> RBAC, or Role-Based Access Control, is an authorization model where users are assigned roles and roles determine which permissions they have.

Example:

```text
Admin → users:delete
User  → users:read
```

---

## Q8. What is the purpose of refresh tokens?

> Refresh tokens allow a client to obtain new short-lived access tokens without requiring the user to repeatedly enter their credentials.

---

## Q9. Session vs JWT?

> Session authentication stores session state on the server and typically gives the client a session identifier, while JWT authentication can carry signed claims that the server validates. Sessions are often simpler to revoke, while JWTs can be useful in distributed systems, but neither is universally better.

---

## Q10. Where should authorization be enforced?

> Authorization must be enforced on the backend because the client cannot be trusted. Frontend checks are useful for user experience but are not a security boundary.

---

# 49. Most Important Mental Model

Memorize this:

```text
                 REQUEST
                    │
                    ▼
            ┌───────────────┐
            │ Authentication│
            └───────┬───────┘
                    │
              Who are you?
                    │
                    ▼
             User identified
                    │
                    ▼
            ┌───────────────┐
            │ Authorization │
            └───────┬───────┘
                    │
             What can you do?
                    │
                    ▼
              Permission OK?
               /          \
             NO            YES
             ↓              ↓
            403          Controller
                            ↓
                         Service
                            ↓
                         Database
```

---

# 50. Quick Revision

```text
Authentication
→ Who are you?

Authorization
→ What can you do?

401
→ Authentication problem

403
→ Authorization problem

Password
→ Hash it

JWT
→ Signed token, not automatically encrypted

RBAC
→ Role-based permissions

Access Token
→ Access protected resources

Refresh Token
→ Obtain new access tokens

Backend
→ Must enforce authorization
```

---

# 51. Final Interview Summary

> **Authentication verifies the identity of a user, while authorization determines what that authenticated user is allowed to access or perform. In a typical backend, authentication establishes the user's identity using credentials, sessions, or tokens, and authorization then checks roles, permissions, or resource ownership before allowing access to protected resources.**
