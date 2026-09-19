# Backend Security

## 1. What is Backend Security?

Backend security means protecting your:

- APIs
- Database
- Server
- Users
- Authentication system
- Authorization rules
- Files
- Secrets
- Internal services
- Infrastructure

from:

- Unauthorized access
- Data theft
- Data manipulation
- Account takeover
- Injection attacks
- Denial of service
- Information leakage
- Malicious requests

### Simple mental model

Think of your backend as a building:

- Authentication → Who are you?
- Authorization → What are you allowed to do?
- Validation → Is your input acceptable?
- Encryption → Can someone read the data?
- Rate limiting → How many requests can you make?
- Logging → What happened?
- Security headers → How should clients interact with the server?
- Database security → Who can access the data?

---

# 2. Authentication vs Authorization

## Authentication

Authentication answers:

> "Who are you?"

Example:

User logs in with:

- Email
- Password

Backend verifies the credentials and identifies the user.

## Authorization

Authorization answers:

> "What are you allowed to do?"

Example:

Admin can:

- Delete users
- Create products
- View admin dashboard

Normal user can:

- View products
- Create orders
- View their own orders

### Interview answer

Authentication verifies identity, while authorization determines what an authenticated user is allowed to access.

---

# 3. Password Security

Never store passwords directly.

### ❌ Bad

    {
      email: "user@gmail.com",
      password: "mypassword123"
    }

If the database is compromised, the attacker gets the actual password.

### ✅ Good

Store a password hash:

    {
      email: "user@gmail.com",
      passwordHash: "$2b$12$..."
    }

Common password hashing algorithms:

- bcrypt
- Argon2
- scrypt

### Important

Hashing is different from encryption.

Hashing:

    password → hash

It is designed to be one-way.

Encryption:

    data → encrypted data → decrypted data

Encryption is reversible with the appropriate key.

---

# 4. Salt

A salt is random data added during password hashing.

Conceptually:

    password + salt → password hash

Why?

Suppose two users have:

    password = "hello123"

Without proper salting, their hashes could be identical.

With unique salts:

    user1 → password + salt1 → hash1
    user2 → password + salt2 → hash2

Modern password hashing libraries such as bcrypt and Argon2 handle salting for you.

---

# 5. Never Store Plain Passwords

Avoid:

- Plain passwords
- Passwords in logs
- Passwords in API responses
- Passwords in frontend local storage
- Passwords in error messages

Example response:

    {
      "id": 123,
      "email": "user@example.com"
    }

Do not return:

    {
      "password": "..."
    }

---

# 6. JWT Authentication

JWT = JSON Web Token.

A JWT can be used to represent authenticated user information.

Typical flow:

    Login
      ↓
    Backend verifies credentials
      ↓
    Backend creates token
      ↓
    Client receives token
      ↓
    Client sends token with protected requests
      ↓
    Backend verifies token
      ↓
    Request allowed

Example:

    Authorization: Bearer <token>

### JWT structure

JWT contains three parts:

    header.payload.signature

Example conceptually:

    xxxxx.yyyyy.zzzzz

---

# 7. JWT Security

Never put sensitive information inside JWT payloads assuming they are secret.

JWT payloads are normally encoded, not encrypted.

Do not store:

- Passwords
- Credit card information
- Secrets
- Highly sensitive personal data

A token might contain:

    {
      "userId": "123",
      "role": "user"
    }

The signature helps the server verify that the token was not modified.

---

# 8. Access Token and Refresh Token

A common architecture uses:

### Access token

Short-lived.

Used for API requests.

Example:

    Access Token
    ↓
    /api/orders
    /api/profile
    /api/products

### Refresh token

Longer-lived.

Used to obtain a new access token.

Flow:

    Login
      ↓
    Access Token + Refresh Token
      ↓
    Access token expires
      ↓
    Refresh token
      ↓
    New Access Token

This reduces the impact of a stolen short-lived access token.

---

# 9. Where Should Tokens Be Stored?

For browser applications, token storage requires careful consideration.

A common secure approach is to use:

- Secure
- HttpOnly
- SameSite

cookies for sensitive session/refresh credentials.

### HttpOnly

JavaScript cannot directly read the cookie.

This helps reduce token theft through certain XSS scenarios.

### Secure

Cookie is sent only over HTTPS.

### SameSite

Controls when cookies are sent with cross-site requests and helps mitigate CSRF.

Example concept:

    Set-Cookie:
    session=abc123;
    HttpOnly;
    Secure;
    SameSite=Lax

---

# 10. HTTPS

Never send sensitive data over plain HTTP in production.

HTTP:

    Client → Server

HTTPS:

    Client → Encrypted connection → Server

HTTPS protects data while it is travelling between client and server.

It helps protect:

- Passwords
- Tokens
- Personal information
- API requests
- Responses

---

# 11. Input Validation

Never trust user input.

Example:

    POST /api/users

User sends:

    {
      "email": "hello",
      "age": -500
    }

Backend should validate the input.

Check:

- Required fields
- Data types
- String length
- Email format
- Numeric ranges
- Allowed values
- Object structure

### Important rule

Frontend validation improves UX.

Backend validation provides security.

You need backend validation even if the frontend already validates the data.

---

# 12. SQL Injection

SQL injection occurs when untrusted input is interpreted as SQL.

### Dangerous idea

    SELECT * FROM users WHERE email = '${email}'

An attacker may manipulate the input to alter the query.

### Protection

Use:

- Parameterized queries
- Prepared statements
- ORM/query builders
- Input validation

Conceptually:

    SELECT * FROM users WHERE email = ?

Then pass the email as a parameter rather than constructing SQL manually.

---

# 13. NoSQL Injection

NoSQL databases can also be vulnerable to injection when untrusted objects are directly used in queries.

For example, don't blindly pass an entire request body into a database query.

Bad pattern:

    User.find(req.body)

Instead:

    User.findOne({
      email: req.body.email
    })

Only allow expected fields.

---

# 14. XSS

XSS = Cross-Site Scripting.

An attacker injects malicious JavaScript into content that is later executed in another user's browser.

Example malicious input:

    <script>...</script>

Potential impact:

- Session theft
- Account actions
- Data theft
- Malicious UI changes

### Prevention

- Escape output
- Sanitize HTML where necessary
- Avoid unsafe HTML rendering
- Use Content Security Policy
- Validate input
- Use framework protections correctly

React escapes normal rendered values by default, but unsafe APIs such as `dangerouslySetInnerHTML` require special care.

---

# 15. CSRF

CSRF = Cross-Site Request Forgery.

It tricks a user's browser into making an unwanted authenticated request.

Example:

User is logged into:

    bank.com

A malicious website attempts to trigger:

    POST /transfer

using the user's authenticated browser session.

### Protection

Depending on the authentication architecture:

- SameSite cookies
- CSRF tokens
- Origin/Referer validation
- Avoid unsafe cross-site cookie behavior

CSRF is particularly relevant when authentication relies on cookies.

---

# 16. CORS

CORS = Cross-Origin Resource Sharing.

It controls which browser origins are allowed to access your backend from frontend JavaScript.

Example:

Frontend:

    https://myapp.com

Backend:

    https://api.myapp.com

Backend may explicitly allow:

    https://myapp.com

### Bad production configuration

    Access-Control-Allow-Origin: *

when your application requires restricted origins and credentials.

Prefer explicitly configured trusted origins where appropriate.

---

# 17. Rate Limiting

Rate limiting controls how many requests a client can make within a period.

Example:

    100 requests / minute / IP

Useful for:

- Login
- OTP
- Password reset
- Public APIs
- Search endpoints
- Expensive operations

Example:

    POST /api/login

Could have a stricter limit than:

    GET /api/products

### Why?

Without rate limiting, attackers can perform:

- Brute-force attacks
- Credential stuffing
- API abuse
- Resource exhaustion

---

# 18. Brute Force Protection

Suppose an attacker repeatedly tries:

    password1
    password2
    password3
    ...

Protection can include:

- Rate limiting
- Temporary account lockouts
- Increasing delays
- CAPTCHA where appropriate
- Monitoring suspicious login attempts
- MFA

Be careful with permanent lockouts because attackers could intentionally lock other users' accounts.

---

# 19. Security Headers

Security-related HTTP headers can improve browser-side protection.

Common examples:

- Content-Security-Policy
- Strict-Transport-Security
- X-Content-Type-Options
- Referrer-Policy
- Permissions-Policy
- Frame-related protections

In Node.js applications, middleware such as Helmet can help configure common security headers.

---

# 20. Content Security Policy

CSP = Content Security Policy.

It controls which sources the browser is allowed to load or execute.

Conceptually:

    Content-Security-Policy:
    default-src 'self'

It can reduce the impact of certain XSS attacks.

CSP should be designed according to the application's actual requirements rather than blindly copying a policy.

---

# 21. Authorization Must Be Checked on the Backend

Never rely only on frontend restrictions.

Example:

Frontend hides:

    Delete User

But an attacker manually sends:

    DELETE /api/users/123

Backend must verify:

    Is authenticated?
          ↓
    Is authorized?
          ↓
    Is this user allowed to delete user 123?
          ↓
    Perform operation

---

# 22. Role-Based Access Control

RBAC = Role-Based Access Control.

Example:

    user
    admin
    manager

Permissions:

    user → read products
    manager → create/update products
    admin → delete products/users

Example:

    if (user.role !== "admin") {
      throw new ForbiddenError();
    }

For larger systems, permission-based authorization can be more flexible:

    products:read
    products:create
    products:update
    users:delete

---

# 23. Object-Level Authorization

One of the most important backend security concepts.

Suppose:

    GET /api/orders/123

User A requests order 123.

You must check whether order 123 actually belongs to User A or whether the user has permission to access it.

Bad:

    Order.findById(req.params.id)

Better concept:

    Order.findOne({
      _id: req.params.id,
      userId: req.user.id
    })

This prevents users from accessing another user's resources simply by changing an ID.

---

# 24. IDOR / BOLA

IDOR = Insecure Direct Object Reference.

Modern API security discussions often use:

BOLA = Broken Object Level Authorization.

Example:

    GET /api/users/100

Attacker changes:

    100 → 101

If user 101's information is returned without authorization checking, the API has an object-level authorization vulnerability.

### Key lesson

Authentication alone is not enough.

You must verify authorization for the specific resource.

---

# 25. Mass Assignment

Mass assignment happens when an application accepts arbitrary fields from user input and directly updates an object.

Example request:

    {
      "name": "Lokendra",
      "role": "admin"
    }

If the backend blindly updates every field, the user may elevate their privileges.

### Protection

Allowlist fields:

    const allowedFields = {
      name: body.name,
      phone: body.phone
    };

Never blindly trust:

    req.body

for privileged updates.

---

# 26. Sensitive Data Exposure

Never expose more data than necessary.

Bad:

    {
      "id": 1,
      "email": "...",
      "passwordHash": "...",
      "internalNotes": "...",
      "resetToken": "..."
    }

Better:

    {
      "id": 1,
      "email": "..."
    }

Follow the principle:

> Return only what the client actually needs.

---

# 27. Secrets Management

Never hardcode secrets.

### ❌ Bad

    const JWT_SECRET = "my-secret-123";

### Better

Use environment/configuration management:

    JWT_SECRET=<secret>

Examples of secrets:

- Database passwords
- JWT signing keys
- API keys
- Cloud credentials
- Payment provider secrets
- Encryption keys

Also:

- Do not commit `.env` files containing secrets.
- Rotate compromised secrets.
- Use secret managers in production when appropriate.

---

# 28. Environment Variables

Typical configuration:

    PORT
    DATABASE_URL
    JWT_SECRET
    REDIS_URL
    CLOUDINARY_API_KEY
    CLOUDINARY_API_SECRET

`.env` might be used locally.

Production applications should use secure secret/configuration management provided by the deployment environment.

---

# 29. Dependency Security

Your application depends on:

- npm packages
- frameworks
- libraries
- system packages

A vulnerable dependency can introduce security problems.

Useful practices:

- Keep dependencies updated
- Remove unused dependencies
- Review security advisories
- Run dependency audits
- Lock dependency versions appropriately
- Use automated dependency scanning

Example:

    npm audit

But don't blindly update every package in production without testing.

---

# 30. File Upload Security

File uploads are dangerous if unrestricted.

Potential problems:

- Malicious files
- Huge files
- Executable files
- Malicious filenames
- Path traversal
- Storage abuse

Validate:

- File size
- File type
- MIME type
- File extension
- File content where necessary

Use generated filenames rather than trusting user-provided filenames.

Example:

    user-profile-123.jpg

instead of:

    ../../server.js

Store uploaded files outside sensitive executable locations and use object storage/CDN when appropriate.

---

# 31. Path Traversal

An attacker tries to access files outside the intended directory.

Example malicious path:

    ../../../../etc/passwd

Never directly concatenate user-controlled paths.

Use:

- Path normalization
- Allowlisted filenames/IDs
- Safe storage mechanisms
- Access controls

---

# 32. Command Injection

Dangerous situation:

User input is passed directly into an operating system command.

Conceptually:

    exec(`some-command ${userInput}`)

If the input is malicious, the attacker may execute unintended commands.

### Protection

- Avoid shell execution when possible
- Use safe APIs
- Validate/allowlist inputs
- Never construct shell commands from untrusted input

---

# 33. SSRF

SSRF = Server-Side Request Forgery.

An attacker tricks your server into making a request to an unintended destination.

Example:

    POST /api/fetch-url

User provides:

    http://internal-service/

If your backend blindly fetches arbitrary URLs, attackers may access internal services.

### Protection

- Allowlist permitted domains
- Block private/internal IP ranges where appropriate
- Validate URLs
- Restrict protocols
- Apply network-level controls
- Set timeouts

---

# 34. API Security

Every API should consider:

    Authentication
        ↓
    Authorization
        ↓
    Input validation
        ↓
    Business rules
        ↓
    Database operation
        ↓
    Safe response

Also consider:

- Rate limiting
- CORS
- HTTPS
- Logging
- Request IDs
- Error handling
- Monitoring
- Timeouts

---

# 35. Error Handling

Do not expose internal implementation details.

### ❌ Bad

    {
      "error": "MongoServerError: connection failed at /app/src/db/index.js:72"
    }

This can reveal:

- Internal paths
- Database information
- Implementation details
- Stack traces

### Better

    {
      "error": "Internal server error"
    }

Log detailed information internally.

Return safe information to the client.

---

# 36. Security Logging

Security events worth logging include:

- Login failures
- Successful logins
- Password changes
- Permission changes
- Admin actions
- Suspicious requests
- Rate-limit violations
- Token failures
- Important resource changes

Never log:

- Passwords
- Access tokens
- Refresh tokens
- API secrets
- Full sensitive personal information

---

# 37. Account Security

Important features can include:

- Strong password hashing
- Email verification
- Password reset
- MFA
- Session management
- Login notifications
- Token expiration
- Refresh-token rotation where appropriate
- Account recovery controls

Password reset tokens should:

- Be random
- Expire
- Be single-use
- Be stored safely

---

# 38. Session Management

When using sessions, manage:

- Session expiration
- Logout
- Session invalidation
- Session rotation
- Secure cookies
- Concurrent sessions
- Suspicious session detection

For example, after a sensitive security event, previously issued sessions may need to be invalidated.

---

# 39. Encryption at Rest

Encryption at rest protects stored data.

Examples:

    Database
        ↓
    Encrypted storage

    Object Storage
        ↓
    Encrypted objects

Useful for:

- Personal information
- Sensitive business data
- Backups
- Stored files

Encryption at rest does not replace access control.

---

# 40. Encryption in Transit

Encryption in transit protects data while travelling between systems.

Examples:

    Browser → HTTPS → API

    API → TLS → Database

    Service A → TLS → Service B

TLS is commonly used for secure communication.

---

# 41. Principle of Least Privilege

Give users and services only the permissions they need.

Example:

A product API does not need database permission to:

    DROP DATABASE

Instead, give it only the database permissions required for its job.

Apply least privilege to:

- Users
- Database accounts
- Cloud IAM roles
- Services
- Containers
- API keys

---

# 42. Database Security

Important practices:

- Strong database credentials
- Least-privilege database users
- Encrypted connections
- Parameterized queries
- Network restrictions
- Backups
- Encryption at rest
- Avoid exposing databases directly to the internet

Typical architecture:

    Internet
       ↓
    Load Balancer
       ↓
    Backend
       ↓
    Private Database

---

# 43. Network Security

Production systems commonly use network isolation.

Example:

    Internet
       ↓
    Load Balancer
       ↓
    Application Servers
       ↓
    Private Database
       ↓
    Private Redis

The database does not need to be publicly accessible.

Use appropriate:

- Firewalls
- Security groups
- Private networks
- Network ACLs
- VPC/subnets
- Service-to-service restrictions

---

# 44. DDoS Protection

DDoS = Distributed Denial of Service.

Attackers send huge amounts of traffic to exhaust resources.

Protection may involve:

- CDN
- WAF
- Rate limiting
- Load balancing
- Autoscaling
- Traffic filtering
- DDoS protection services

Application-level rate limiting alone is not sufficient for every DDoS scenario.

---

# 45. WAF

WAF = Web Application Firewall.

A WAF sits in front of applications and can inspect HTTP traffic.

Typical architecture:

    Client
       ↓
    CDN / WAF
       ↓
    Load Balancer
       ↓
    Backend
       ↓
    Database

It can help detect/block patterns associated with attacks such as:

- SQL injection
- XSS
- Malicious requests
- Abnormal traffic

WAF is an additional layer, not a replacement for secure application code.

---

# 46. Security Layers

Think about security as multiple layers:

    Layer 1 → HTTPS
    Layer 2 → CDN/WAF
    Layer 3 → Rate limiting
    Layer 4 → Authentication
    Layer 5 → Authorization
    Layer 6 → Input validation
    Layer 7 → Secure business logic
    Layer 8 → Database security
    Layer 9 → Infrastructure security
    Layer 10 → Logging/monitoring

This is called defense in depth.

---

# 47. Zero Trust Concept

Zero Trust follows the idea:

> Do not automatically trust a request just because it came from an internal network.

Verify:

- Identity
- Authorization
- Device/service identity
- Request context
- Permissions

This becomes especially important in distributed systems and microservices.

---

# 48. API Gateway Security

An API Gateway can provide centralized controls such as:

- Authentication
- Rate limiting
- Request routing
- TLS termination
- Logging
- Request validation
- Access policies

Example:

    Client
       ↓
    API Gateway
       ↓
    Auth Service
       ↓
    Order Service
       ↓
    Database

---

# 49. Security in Microservices

In microservices:

    Client
      ↓
    API Gateway
      ↓
    ┌──────────────┐
    │              │
    User Service   Order Service
    │              │
    Database       Database

Security concerns include:

- Service authentication
- Service authorization
- TLS
- Secret management
- Network restrictions
- API gateway security
- Distributed tracing
- Audit logs

Do not assume internal services are automatically trusted.

---

# 50. Security and Queues

Background jobs also need security.

Example:

    API
     ↓
    Queue
     ↓
    Worker

Protect:

- Queue credentials
- Job payloads
- Worker permissions
- Sensitive job data
- Retry behavior
- Dead-letter queues

Avoid putting unnecessary sensitive data directly into job payloads.

Prefer storing a resource ID and retrieving the necessary data securely.

---

# 51. Security and WebSockets

WebSockets also require:

- Authentication
- Authorization
- Origin validation where appropriate
- Rate limiting
- Message validation
- Connection limits
- Timeout/heartbeat handling

Do not assume:

    Connected = Authorized for everything

Every sensitive action still requires authorization.

---

# 52. Security and Webhooks

Incoming webhooks must be verified.

Typical approach:

    External Service
          ↓
    Webhook Request
          ↓
    Verify Signature
          ↓
    Validate Payload
          ↓
    Process Event

Do not trust the request merely because it came to your webhook endpoint.

Also consider:

- Replay attacks
- Idempotency
- Timestamp validation
- Rate limiting

---

# 53. Idempotency and Security

Some operations can accidentally be repeated.

Example:

    POST /payments

Network retry:

    Request 1 → payment
    Request 2 → payment again

Use an idempotency key where supported:

    Idempotency-Key: abc123

The server ensures repeated requests with the same key do not create duplicate effects.

This is especially important for:

- Payments
- Orders
- Financial transactions
- Webhooks

---

# 54. Security Testing

Security testing can include:

### Static analysis

Checks source code for vulnerabilities.

### Dependency scanning

Checks vulnerable packages.

### Dynamic testing

Tests the running application.

### Penetration testing

Authorized security professionals attempt to discover vulnerabilities.

### API testing

Test:

- Authentication bypass
- Authorization bypass
- Injection
- Invalid input
- Rate limiting
- Sensitive data exposure

Never perform penetration testing against systems without authorization.

---

# 55. OWASP

OWASP = Open Worldwide Application Security Project.

The OWASP Top 10 is a widely used awareness resource for common web application security risks.

Important categories include concepts such as:

- Broken access control
- Cryptographic failures
- Injection
- Security misconfiguration
- Authentication failures
- Vulnerable components
- Logging/monitoring failures
- SSRF

For interviews, understand the concepts rather than only memorizing the list.

---

# 56. Secure Backend Request Flow

A secure API request can conceptually look like:

    Client
       ↓
    HTTPS
       ↓
    WAF / Load Balancer
       ↓
    Rate Limiting
       ↓
    Authentication
       ↓
    Authorization
       ↓
    Validation
       ↓
    Controller
       ↓
    Business Logic
       ↓
    Repository
       ↓
    Database
       ↓
    Safe Response
       ↓
    Logging / Monitoring

---

# 57. Example: Secure Order API

Request:

    POST /api/orders

Flow:

    1. HTTPS protects the request
    2. Rate limiter checks request frequency
    3. Authentication identifies the user
    4. Authorization checks permissions
    5. Validation checks product IDs and quantities
    6. Business logic checks product availability
    7. Database transaction creates the order
    8. Sensitive fields are not returned
    9. Security-relevant events are logged
    10. Monitoring tracks failures and latency

---

# 58. Common Security Mistakes

### Mistake 1

Trusting frontend validation.

### Mistake 2

Storing plain passwords.

### Mistake 3

Returning password hashes or secrets.

### Mistake 4

Putting secrets in GitHub.

### Mistake 5

Using `req.body` blindly.

### Mistake 6

Not checking object-level authorization.

### Mistake 7

No rate limiting on login/OTP APIs.

### Mistake 8

Exposing detailed stack traces.

### Mistake 9

Allowing unrestricted file uploads.

### Mistake 10

Using `eval()` with user input.

### Mistake 11

Constructing SQL queries using string concatenation.

### Mistake 12

Trusting webhook requests without signature verification.

### Mistake 13

Making internal databases publicly accessible.

### Mistake 14

Logging passwords or tokens.

### Mistake 15

Assuming internal services are automatically trusted.

---

# 59. Security Checklist

Before deploying a backend, ask:

### Authentication

- Are passwords securely hashed?
- Are tokens/session credentials protected?
- Are sessions expired appropriately?
- Is MFA needed?

### Authorization

- Are permissions checked on the backend?
- Is object-level authorization implemented?
- Are admin endpoints protected?

### Input

- Is every external input validated?
- Are request sizes limited?
- Are uploaded files validated?

### Database

- Are queries parameterized?
- Is database access restricted?
- Is the database private?

### API

- Is HTTPS enabled?
- Is rate limiting configured?
- Is CORS configured correctly?
- Are security headers configured?

### Secrets

- Are secrets outside source code?
- Are secrets excluded from Git?
- Are production secrets managed securely?

### Errors

- Are internal errors hidden from clients?
- Are detailed errors logged securely?

### Monitoring

- Are suspicious activities logged?
- Are security alerts configured?

### Dependencies

- Are dependencies monitored for vulnerabilities?
- Are unused dependencies removed?

---

# 60. Security vs Performance

Security controls can have performance costs.

Examples:

- Password hashing intentionally consumes CPU.
- Encryption adds processing overhead.
- WAF adds network processing.
- Authorization checks add database/cache operations.
- Logging adds I/O.

The goal is not to remove security for performance.

Instead:

    Secure design
        +
    Appropriate caching
        +
    Efficient queries
        +
    Proper infrastructure
        =
    Secure and scalable system

---

# 61. Interview Questions

## Q1. What is the difference between authentication and authorization?

Authentication verifies who the user is.

Authorization determines what the authenticated user is allowed to do.

---

## Q2. Why should passwords be hashed?

Because storing plaintext passwords means anyone who gains database access can directly obtain users' passwords. Password hashing makes the stored representation one-way and significantly harder to exploit.

---

## Q3. Hashing vs encryption?

Hashing is generally one-way.

Encryption is reversible using a key.

Passwords should normally be stored using a password hashing algorithm rather than reversible encryption.

---

## Q4. What is SQL injection?

SQL injection occurs when attacker-controlled input changes the meaning of a database query.

Prevent it using parameterized queries/prepared statements, safe database APIs, and validation.

---

## Q5. What is XSS?

XSS allows attacker-controlled content to execute as script in another user's browser.

Protection includes output encoding, safe rendering, sanitization where necessary, and CSP.

---

## Q6. What is CSRF?

CSRF tricks an authenticated browser into making an unwanted request.

Protection can include SameSite cookies, CSRF tokens, and origin validation depending on the authentication architecture.

---

## Q7. What is CORS?

CORS is a browser security mechanism that controls which origins can make cross-origin requests to a server.

---

## Q8. Why is rate limiting important?

It reduces abuse such as brute-force attacks, credential stuffing, API abuse, and resource exhaustion.

---

## Q9. What is IDOR/BOLA?

It occurs when an application exposes an object through an identifier but fails to verify whether the requesting user is authorized to access that object.

---

## Q10. Why should authorization be performed on the backend?

Because frontend restrictions can be bypassed by directly calling the API.

The backend is the final authority for access control.

---

## Q11. Why should secrets not be stored in GitHub?

Because anyone who gains access to the repository or its history may obtain credentials and use them against your infrastructure or services.

---

## Q12. What is least privilege?

Give each user, service, or process only the permissions required to perform its job.

---

## Q13. What is defense in depth?

Using multiple independent security layers so that failure of one layer does not expose the entire system.

---

## Q14. How would you secure a login API?

I would use:

- HTTPS
- Secure password hashing
- Input validation
- Rate limiting
- Generic authentication errors
- Secure session/token handling
- Monitoring
- MFA where appropriate
- Protection against credential stuffing

---

## Q15. How would you secure a file upload API?

I would:

- Limit file size
- Validate file type
- Validate MIME type
- Generate safe filenames
- Prevent path traversal
- Store files safely
- Avoid executing uploaded files
- Restrict access
- Scan files where appropriate

---

# 62. Real-World Security Architecture

A production application might look like:

    User
      ↓
    HTTPS
      ↓
    CDN / WAF
      ↓
    Load Balancer
      ↓
    API Gateway
      ↓
    Backend
      ↓
    ┌────────────────────────┐
    │ Authentication         │
    │ Authorization           │
    │ Validation              │
    │ Rate Limiting           │
    │ Business Logic          │
    └────────────────────────┘
      ↓
    Cache / Database / Queue
      ↓
    Private Infrastructure

Alongside the system:

    Logging
       +
    Monitoring
       +
    Tracing
       +
    Security Alerts

---

# 63. Security Mental Model

Whenever you build an API, ask these questions:

    1. Who is making this request?
           ↓
    2. Are they authenticated?
           ↓
    3. Are they authorized?
           ↓
    4. Is their input valid?
           ↓
    5. Can this input cause injection?
           ↓
    6. Are they accessing only their own resources?
           ↓
    7. Can they abuse this endpoint?
           ↓
    8. Am I exposing sensitive information?
           ↓
    9. Are secrets protected?
           ↓
    10. Can I detect suspicious activity?

---

# 64. Quick Revision

    Authentication
    → Who are you?

    Authorization
    → What can you do?

    Password hashing
    → bcrypt / Argon2 / scrypt

    HTTPS
    → Encrypt data in transit

    Validation
    → Never trust user input

    SQL Injection
    → Use parameterized queries

    XSS
    → Prevent malicious script execution

    CSRF
    → Protect cookie-based authenticated requests

    CORS
    → Control allowed browser origins

    Rate Limiting
    → Prevent abuse

    RBAC
    → Role-based permissions

    BOLA/IDOR
    → Verify access to the specific resource

    Secrets
    → Never hardcode or commit them

    Least Privilege
    → Give only required permissions

    WAF
    → Filter/protect HTTP traffic

    Logging
    → Detect and investigate security events

    Defense in Depth
    → Multiple security layers

---

# 65. One-Line Interview Summary

> Backend security is about protecting APIs, users, data, and infrastructure through secure authentication, authorization, validation, encryption, access control, rate limiting, safe error handling, secret management, secure infrastructure, and continuous monitoring.