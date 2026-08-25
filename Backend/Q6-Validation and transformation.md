# Backend Development — Validation & Transformation

## 1. What is Validation?

**Validation** is the process of checking whether incoming data satisfies the rules required by the application.

In simple words:

> **Validation checks whether the data is acceptable.**

For example, suppose an API expects:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com",
  "age": 23
}
```

The backend may have rules such as:

```text
name  → required
email → valid email format
age   → number
age   → must be >= 18
```

If the client sends:

```json
{
  "name": "",
  "email": "hello",
  "age": "abc"
}
```

the backend should reject the request.

---

# 2. What is Transformation?

**Transformation** is the process of converting valid input into the format or shape required by the application.

In simple words:

> **Transformation changes data into the form the application wants to work with.**

For example, the client might send:

```json
{
  "name": "  Lokendra  ",
  "age": "23"
}
```

The backend may transform it into:

```js
{
  name: "Lokendra",
  age: 23
}
```

So:

```text
Validation
→ Is this data acceptable?

Transformation
→ How should we represent this data internally?
```

---

# 3. Validation vs Transformation

This is the most important distinction.

| Validation                             | Transformation                        |
| -------------------------------------- | ------------------------------------- |
| Checks data                            | Changes data                          |
| Answers "Is this valid?"               | Answers "What form should this have?" |
| Rejects invalid input                  | Converts acceptable input             |
| Protects business logic from bad input | Makes data convenient/consistent      |
| Example: age must be >= 18             | Example: `"23"` → `23`                |

Example:

```text
Input:
{
  "name": " Lokendra ",
  "age": "23"
}
```

Validation:

```text
name exists?      ✓
age is acceptable? ✓
```

Transformation:

```text
" Lokendra " → "Lokendra"
"23"         → 23
```

---

# 4. Why Do We Need Validation?

Never assume that data coming from a client is trustworthy.

A client can send:

```json
{
  "age": -100
}
```

or:

```json
{
  "email": "not-an-email"
}
```

or:

```json
{
  "role": "admin"
}
```

or even:

```json
{
  "price": "free"
}
```

The backend must validate incoming data.

> **The client is not a trusted source of truth.**

Even if your frontend already validates the data, the backend must perform its own validation.

---

# 5. Client-Side vs Server-Side Validation

You can validate data on both sides.

### Frontend

```text
User
 ↓
React Form
 ↓
Frontend Validation
 ↓
API
```

### Backend

```text
API
 ↓
Backend Validation
 ↓
Business Logic
 ↓
Database
```

Frontend validation improves:

* User experience
* Immediate feedback
* Form usability

Backend validation provides:

* Security
* Data integrity
* Protection for every client
* Consistent business rules

Therefore:

> **Frontend validation is useful, but backend validation is mandatory for trusted enforcement.**

---

# 6. Example — Registration API

Suppose we have:

```http
POST /api/users
```

Request:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com",
  "password": "mypassword"
}
```

We may define:

```text
name
→ required
→ string
→ minimum 2 characters

email
→ required
→ valid email

password
→ required
→ minimum 8 characters
```

The backend validates these rules before creating the user.

---

# 7. Basic Validation in Node.js

You can perform simple validation manually.

```js
app.post("/users", (req, res) => {
  const { name, email, password } = req.body;

  if (!name) {
    return res.status(400).json({
      message: "Name is required"
    });
  }

  if (!email) {
    return res.status(400).json({
      message: "Email is required"
    });
  }

  if (!password) {
    return res.status(400).json({
      message: "Password is required"
    });
  }

  res.status(201).json({
    message: "User is valid"
  });
});
```

This works for simple cases.

But as applications grow, manual validation becomes difficult to maintain.

---

# 8. Validation Libraries

Real-world Node.js applications often use validation/schema libraries.

Common examples include:

```text
Zod
Joi
Yup
Ajv
class-validator
```

You don't need to memorize every library.

The important concept is:

```text
Request
   ↓
Validation Schema
   ↓
Valid?
 ┌─┴─┐
No  Yes
 ↓    ↓
400  Application
```

---

# 9. Zod Example

A common modern approach is to define a schema.

```js
const userSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  age: z.number().int().min(18)
});
```

Then validate:

```js
const result = userSchema.safeParse(req.body);

if (!result.success) {
  return res.status(400).json({
    message: "Invalid request",
    errors: result.error.issues
  });
}
```

If valid:

```js
const user = result.data;
```

Now the application can work with validated data.

---

# 10. Why Schemas Are Useful

Instead of scattering rules throughout your controller:

```js
if (!name) ...
if (!email) ...
if (age < 18) ...
```

you define the expected structure in one place:

```text
User Input Schema
       ↓
 ┌───────────────┐
 │ name          │
 │ email         │
 │ age           │
 └───────────────┘
```

Benefits:

* Centralized rules
* Easier maintenance
* Better error messages
* Reusable schemas
* Type inference in some tools
* Easier testing

---

# 11. Validation Types

Validation isn't only about checking whether a field exists.

Common validation categories include:

### Required fields

```text
email is required
```

### Type validation

```text
age must be a number
```

### Length validation

```text
password must be at least 8 characters
```

### Format validation

```text
email must have valid format
```

### Range validation

```text
age must be between 18 and 100
```

### Enum validation

```text
status must be:
pending
approved
rejected
```

### Cross-field validation

```text
password === confirmPassword
```

### Business validation

```text
A user cannot withdraw more money than their balance.
```

The last one is especially important because not all validation belongs in the same layer.

---

# 12. Syntactic vs Semantic Validation

A useful distinction:

### Syntactic Validation

Checks whether the data has the correct basic form.

Example:

```text
email = "hello@example.com"
```

Valid email format.

---

### Semantic / Business Validation

Checks whether the value makes sense in the application's context.

Example:

```text
Account balance = ₹500

Withdrawal = ₹10,000
```

The number itself is valid.

But the operation may not be allowed.

So:

```text
Syntactic Validation
→ Is the data structurally valid?

Business Validation
→ Is the operation valid according to business rules?
```

---

# 13. Validation vs Business Logic

This distinction is important for backend architecture.

Suppose:

```text
age must be a number
```

This is input validation.

But:

```text
Only users over 18 can purchase this product.
```

This is a business rule.

Another example:

```text
email must be valid
```

Validation.

But:

```text
This email cannot register because an account already exists.
```

This requires application/database/business logic.

A useful mental model:

```text
Input Validation
      ↓
Business Rules
      ↓
Database
```

---

# 14. Transformation

Now let's focus on transformation.

Suppose the client sends:

```json
{
  "name": "  Lokendra  ",
  "age": "23"
}
```

The backend may want:

```js
{
  name: "Lokendra",
  age: 23
}
```

The transformations are:

```text
"  Lokendra  "
        ↓
trim()
        ↓
"Lokendra"

"23"
 ↓
Number()
 ↓
23
```

---

# 15. Manual Transformation in Node.js

```js
const name = req.body.name.trim();

const age = Number(req.body.age);
```

Then:

```js
const user = {
  name,
  age
};
```

Input:

```json
{
  "name": "  Lokendra  ",
  "age": "23"
}
```

Output:

```js
{
  name: "Lokendra",
  age: 23
}
```

---

# 16. Common Transformations

Backend systems commonly perform transformations such as:

```text
Trim strings
Convert strings to numbers
Convert strings to booleans
Normalize email addresses
Convert dates
Rename fields
Convert database models to DTOs
Remove unwanted fields
Convert units
Normalize phone numbers
```

Example:

```text
"  LOKENDRA@EXAMPLE.COM  "
            ↓
      trim + lowercase
            ↓
"lokendra@example.com"
```

---

# 17. Validation and Transformation Together

These operations often work together.

Input:

```json
{
  "name": "  Lokendra ",
  "age": "23"
}
```

Transformation:

```text
"  Lokendra " → "Lokendra"
"23" → 23
```

Then validation:

```text
name is string? ✓
age is number? ✓
age >= 18? ✓
```

Depending on the library and architecture, validation may happen before or during transformation.

The important principle is:

> **Never let untrusted, unvalidated data flow into sensitive business operations.**

---

# 18. Zod Transformation Example

Zod can perform transformations.

```js
const userSchema = z.object({
  name: z.string()
    .trim()
    .min(2),

  age: z.coerce.number()
    .int()
    .min(18)
});
```

Input:

```json
{
  "name": "  Lokendra ",
  "age": "23"
}
```

The parsed result can become:

```js
{
  name: "Lokendra",
  age: 23
}
```

Notice:

```text
Input
  ↓
Schema
  ↓
Transform + Validate
  ↓
Parsed Data
```

This is one reason schema libraries are useful.

---

# 19. Validation of Query Parameters

Remember from routing:

```text
GET /products?page=2&limit=20
```

Query parameters arrive as request data.

You should not blindly trust them.

Example:

```js
const page = Number(req.query.page);
const limit = Number(req.query.limit);
```

Then validate:

```text
page >= 1
limit >= 1
limit <= 100
```

Otherwise someone could send:

```text
?page=-999999&limit=999999999
```

which could cause bad behavior or expensive database queries.

---

# 20. Validation of Route Parameters

Suppose:

```text
GET /users/:id
```

Request:

```text
GET /users/101
```

You might convert:

```js
const id = Number(req.params.id);
```

Then validate:

```js
if (!Number.isInteger(id) || id <= 0) {
  return res.status(400).json({
    message: "Invalid user ID"
  });
}
```

So:

```text
Route Parameter
      ↓
Transformation
      ↓
Validation
      ↓
Database
```

---

# 21. Validation of Request Body

Example:

```js
app.post("/products", (req, res) => {
  const { name, price } = req.body;

  if (typeof name !== "string") {
    return res.status(400).json({
      message: "Name must be a string"
    });
  }

  if (
    typeof price !== "number" ||
    price <= 0
  ) {
    return res.status(400).json({
      message: "Price must be a positive number"
    });
  }

  res.status(201).json({
    message: "Valid product"
  });
});
```

This is basic input validation.

---

# 22. Whitelisting vs Blacklisting

This is an important security concept.

### Blacklist

Define what is forbidden:

```text
Reject:
password
admin
internalField
```

Problem:

> You may forget to block something new.

---

### Whitelist

Define exactly what is allowed:

```js
const userData = {
  name: req.body.name,
  email: req.body.email
};
```

Only expected fields are accepted.

Generally:

> **Prefer allowlists/whitelists for security-sensitive input.**

---

# 23. Example — Mass Assignment Problem

Suppose your database user has:

```js
{
  name,
  email,
  role
}
```

Client sends:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com",
  "role": "admin"
}
```

Dangerous code:

```js
await User.create(req.body);
```

The client may be able to modify fields it shouldn't control.

Better:

```js
const userData = {
  name: req.body.name,
  email: req.body.email
};

await User.create(userData);
```

This is an important real-world security principle.

> **Never blindly pass client input into database creation/update operations.**

---

# 24. Default Values

Transformation can also provide defaults.

Input:

```json
{
  "name": "Lokendra"
}
```

Application may transform it into:

```js
{
  name: "Lokendra",
  role: "user"
}
```

But be careful:

> Security-sensitive defaults such as roles should generally be assigned by trusted server-side logic, not accepted from the client.

---

# 25. Normalization

Normalization means converting equivalent inputs into a consistent representation.

Example:

```text
"LOKENDRA@EXAMPLE.COM"
"Lokendra@example.com"
" lokendra@example.com "
```

can be normalized to:

```text
"lokendra@example.com"
```

Example:

```js
const email = req.body.email
  .trim()
  .toLowerCase();
```

This makes comparisons and storage more consistent.

However, normalization rules should be appropriate for the data type. For example, blindly lowercasing every user-provided string is not correct.

---

# 26. Date Transformation

A client might send:

```json
{
  "date": "2026-08-24"
}
```

The backend might transform it into:

```js
const date = new Date(req.body.date);
```

But date handling needs care.

Things to consider:

```text
Timezone
Date format
Locale
UTC
Invalid dates
```

For APIs, prefer well-defined formats such as ISO 8601 and be explicit about timezone semantics.

---

# 27. Unit Transformation

Transformation isn't limited to strings and numbers.

Suppose an API receives:

```json
{
  "weight": 70,
  "unit": "kg"
}
```

The backend may internally convert everything to grams:

```text
70 kg
 ↓
70000 g
```

This creates a consistent internal representation.

---

# 28. Output Transformation

Transformation can happen on the response side too.

Suppose your database returns:

```js
{
  _id: "...",
  first_name: "Lokendra",
  passwordHash: "...",
  created_at: "..."
}
```

You probably don't want to expose that directly.

Transform it:

```js
const response = {
  id: user._id,
  name: user.first_name,
  createdAt: user.created_at
};
```

Then:

```js
res.json(response);
```

Flow:

```text
Database Model
      ↓
Response Transformation
      ↓
DTO
      ↓
Serialization
      ↓
HTTP Response
```

---

# 29. DTOs and Transformation

DTO stands for:

> **Data Transfer Object**

DTOs are commonly used to define what crosses a boundary.

Example:

```js
const userResponseDto = {
  id: user.id,
  name: user.name,
  email: user.email
};
```

The DTO prevents your internal database model from becoming your public API contract.

This is especially important in large applications.

---

# 30. Input DTO vs Output DTO

You can have separate DTOs.

### Input DTO

```js
{
  name,
  email,
  password
}
```

### Output DTO

```js
{
  id,
  name,
  email
}
```

Notice:

```text
password
```

exists in the input but not the output.

That's intentional.

```text
Client Request
      ↓
Input DTO
      ↓
Business Logic
      ↓
Output DTO
      ↓
Client Response
```

---

# 31. Validation Pipeline

A mature API can have a validation pipeline:

```text
HTTP Request
      ↓
Parse / Deserialize
      ↓
Extract Input
      ↓
Transform
      ↓
Validate
      ↓
Controller
      ↓
Service
```

Or:

```text
HTTP Request
      ↓
Parse
      ↓
Validate
      ↓
Transform
      ↓
Controller
```

Both patterns can be valid.

The exact ordering depends on whether validation rules operate on raw input or normalized/transformed values.

For example:

```text
"23"
```

might need to be converted to:

```text
23
```

before a numeric validation rule can run.

---

# 32. Validation Errors

A good API should return useful validation errors.

Bad:

```json
{
  "error": "Invalid input"
}
```

Better:

```json
{
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email address"
    },
    {
      "field": "age",
      "message": "Age must be at least 18"
    }
  ]
}
```

This is easier for clients to handle.

---

# 33. 400 vs 422

You may encounter both:

```text
400 Bad Request
422 Unprocessable Content
```

Different APIs use these differently.

A common convention is:

```text
400
→ Malformed or invalid request

422
→ Request is syntactically valid but semantically invalid
```

For example:

```json
{
  "email": "not-an-email"
}
```

could be represented with `422`.

However:

> There is no universal requirement that every API must use 422 for validation errors.

Consistency within your API is more important.

---

# 34. Validation and Database Constraints

Application validation is important, but database constraints are also important.

Example:

Application:

```text
email must be valid
```

Database:

```text
email UNIQUE
```

Why both?

Because application validation can have race conditions.

Imagine:

```text
Request A → email available
Request B → email available
```

Both requests pass the application check.

Then both try to insert.

A database unique constraint can guarantee:

```text
Only one succeeds.
```

This leads to an important principle:

> **Application validation improves user experience and early rejection; database constraints enforce data integrity at the storage boundary.**

---

# 35. Validation Is Not Sanitization

These concepts are related but different.

### Validation

Asks:

> Is this input acceptable?

Example:

```text
age = 23
```

### Sanitization / Normalization

Changes or cleans the input into a safer/consistent form.

Example:

```text
"  Lokendra  "
        ↓
"Lokendra"
```

Transformation is a broader concept that includes many types of data conversion.

Don't assume sanitizing arbitrary HTML or SQL-like strings is a universal security solution. Proper parameterization, output encoding, and context-specific security controls are still required.

---

# 36. SQL Injection and Validation

Validation alone does not prevent SQL injection.

Bad:

```js
const query = `
  SELECT * FROM users
  WHERE email = '${email}'
`;
```

Instead, use parameterized queries or a safe database abstraction.

Example concept:

```text
SQL Query
    +
Parameters
```

The database driver handles the values separately from the SQL structure.

So:

```text
Validation
≠
SQL Injection Protection
```

You need both proper validation and safe database query construction.

---

# 37. Validation and Security

Validation helps protect against:

```text
Unexpected types
Invalid values
Oversized input
Unexpected fields
Malformed requests
Some forms of abuse
```

But validation alone does not solve all security problems.

You also need:

```text
Authentication
Authorization
Rate limiting
Input/output controls
Secure database queries
HTTPS
Security headers
CSRF protection where applicable
Logging/monitoring
```

---

# 38. Validation and Transformation in a Layered Architecture

A clean architecture might look like:

```text
                    HTTP Request
                         ↓
                  Deserialization
                         ↓
               Validation / Transform
                         ↓
                       Router
                         ↓
                    Controller
                         ↓
                      Service
                         ↓
                    Repository
                         ↓
                     Database
```

The controller should ideally receive **clean, validated data** instead of repeatedly checking raw input.

---

# 39. Node.js Example — Better Structure

Instead of:

```js
app.post("/users", async (req, res) => {
  if (!req.body.email) {
    ...
  }

  if (!req.body.name) {
    ...
  }

  // 100 lines of logic...
});
```

Separate validation:

```js
const validateUser = (req, res, next) => {
  const { name, email } = req.body;

  if (!name || typeof name !== "string") {
    return res.status(400).json({
      message: "Valid name is required"
    });
  }

  if (!email || typeof email !== "string") {
    return res.status(400).json({
      message: "Valid email is required"
    });
  }

  next();
};
```

Then:

```js
app.post(
  "/users",
  validateUser,
  createUser
);
```

Flow:

```text
POST /users
     ↓
validateUser
     ↓
createUser
```

This keeps responsibilities separate.

---

# 40. Transformation Middleware

You can also transform data before the controller.

```js
const normalizeUser = (req, res, next) => {
  req.body.name = req.body.name?.trim();

  req.body.email = req.body.email
    ?.trim()
    .toLowerCase();

  next();
};
```

Then:

```js
app.post(
  "/users",
  normalizeUser,
  validateUser,
  createUser
);
```

Flow:

```text
Request
   ↓
Transform
   ↓
Validate
   ↓
Controller
```

Again, whether transformation occurs before or after validation depends on the transformation and validation rules.

---

# 41. Don't Mutate Raw Request Data Carelessly

Instead of modifying:

```js
req.body
```

everywhere, a cleaner pattern can be to create validated/normalized data:

```js
const data = {
  name: req.body.name.trim(),
  email: req.body.email.trim().toLowerCase()
};
```

Then pass:

```text
data
```

to the service.

This reduces unexpected side effects.

Schema libraries can also produce a parsed/validated result:

```js
const data = userSchema.parse(req.body);
```

Then:

```js
userService.createUser(data);
```

---

# 42. Validation of File Uploads

Validation also applies to files.

Suppose the client uploads:

```text
profile.jpg
```

The backend may validate:

```text
File size
MIME type
Extension
Image dimensions
File content
```

Do not trust only the filename or client-provided MIME type.

For example:

```text
photo.jpg
```

doesn't automatically mean the file is actually a valid JPEG.

File validation is especially important because uploaded files can create security risks.

---

# 43. Validation of Pagination

Suppose:

```text
GET /users?page=2&limit=20
```

You might transform:

```text
"2"  → 2
"20" → 20
```

Then validate:

```text
page >= 1
limit >= 1
limit <= 100
```

This prevents requests such as:

```text
?page=-5&limit=999999999
```

from causing unexpected behavior.

---

# 44. Validation of Enum Values

Suppose an order has:

```text
pending
paid
shipped
cancelled
```

You should reject:

```json
{
  "status": "banana"
}
```

Schema concept:

```text
status ∈ {
  pending,
  paid,
  shipped,
  cancelled
}
```

This is called enum/allowlist validation.

---

# 45. Cross-Field Validation

Sometimes one field depends on another.

Example:

```json
{
  "password": "mypassword",
  "confirmPassword": "mypassword"
}
```

Validation:

```text
password === confirmPassword
```

Another example:

```text
startDate < endDate
```

These cannot always be validated by checking each field independently.

---

# 46. Validation of Nested Objects

Request:

```json
{
  "name": "Lokendra",
  "address": {
    "city": "Bareilly",
    "pincode": "243001"
  }
}
```

You may need:

```text
name → string
address → object
address.city → string
address.pincode → valid format
```

Modern schema validators are useful for nested structures because they allow you to define the complete shape.

---

# 47. Unknown Fields

Suppose your API expects:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com"
}
```

But client sends:

```json
{
  "name": "Lokendra",
  "email": "lokendra@example.com",
  "isAdmin": true
}
```

You need to decide what your API should do.

Options:

```text
Reject unknown fields
Ignore unknown fields
Strip unknown fields
```

For security-sensitive APIs, explicitly controlling unexpected fields is often preferable.

---

# 48. Over-Validation

Validation can also become problematic if it is too strict.

Suppose your API rejects any future field:

```json
{
  "name": "Lokendra",
  "email": "...",
  "newField": "..."
}
```

This can make API evolution harder.

Therefore, validation should be:

```text
Strict enough for correctness/security
+
Flexible enough for planned evolution
```

The right behavior depends on the API contract.

---

# 49. Input Validation vs Output Validation

Most developers focus on input validation:

```text
Client → Backend
```

But output validation can also be valuable.

Example:

```text
Database
   ↓
Service
   ↓
Response DTO
   ↓
Output Schema
   ↓
Client
```

Output validation can catch bugs where your backend accidentally returns data that doesn't match the API contract.

This is particularly useful in strongly typed or contract-driven systems.

---

# 50. Example — Complete User Flow

Request:

```json
{
  "name": "  Lokendra ",
  "email": " LOKENDRA@EXAMPLE.COM ",
  "age": "23"
}
```

### Step 1 — Deserialize

```text
JSON
 ↓
JavaScript Object
```

### Step 2 — Transform

```text
"  Lokendra " → "Lokendra"
" LOKENDRA@EXAMPLE.COM " → "lokendra@example.com"
"23" → 23
```

### Step 3 — Validate

```text
name → valid
email → valid
age → integer
age >= 18
```

### Step 4 — Business Logic

```text
Does user already exist?
```

### Step 5 — Database

```text
Create user
```

### Step 6 — Response Transformation

```text
Database Model
 ↓
Response DTO
```

### Step 7 — Serialize

```text
Object
 ↓
JSON
```

### Step 8 — Response

```json
{
  "id": 101,
  "name": "Lokendra",
  "email": "lokendra@example.com"
}
```

---

# 51. Common Beginner Mistakes

## Mistake 1: Trusting frontend validation

Wrong:

```text
"Frontend validated it, so backend doesn't need validation."
```

Correct:

```text
Frontend validation → UX
Backend validation  → Security + correctness
```

---

## Mistake 2: Treating transformation as validation

This:

```js
Number(req.body.age)
```

does not automatically mean the value is valid.

For example:

```js
Number("hello")
```

results in:

```text
NaN
```

You still need validation.

---

## Mistake 3: Blindly accepting `req.body`

Avoid:

```js
await User.create(req.body);
```

when the client can control fields they shouldn't control.

Prefer explicit field selection or a validated schema.

---

## Mistake 4: Assuming valid JSON means valid application data

This is valid JSON:

```json
{
  "age": "hello"
}
```

But it may be invalid for your application.

Remember:

```text
Valid JSON
      ≠
Valid Application Data
```

---

## Mistake 5: Putting every business rule into the validator

Not every rule belongs in input validation.

For example:

```text
"User cannot purchase more than their account limit"
```

is usually business logic, not merely schema validation.

---

# 52. Interview-Friendly Answers

## Q1. What is validation?

> Validation is the process of checking whether incoming data satisfies the structural, type, format, and value constraints required by the application.

### Short answer

> Validation checks whether incoming data is acceptable.

---

## Q2. What is transformation?

> Transformation is the process of converting data from one representation or shape into another representation that the application expects.

Example:

```text
"23" → 23
" Lokendra " → "Lokendra"
```

---

## Q3. What is the difference between validation and transformation?

> Validation determines whether data is acceptable, while transformation changes valid or acceptable data into the format required by the application.

---

## Q4. Why is backend validation necessary if frontend validation already exists?

> Frontend validation improves user experience, but it cannot be trusted because clients can bypass it. Backend validation is necessary to enforce data integrity and security regardless of which client sends the request.

---

## Q5. What is schema validation?

> Schema validation means defining the expected structure, types, and constraints of data and checking incoming data against that schema.

Example:

```text
User:
name  → string
email → valid email
age   → integer >= 18
```

---

## Q6. What is normalization?

> Normalization is transforming equivalent input values into a consistent representation, such as trimming whitespace or converting an email address to lowercase when appropriate.

---

## Q7. What is the difference between validation and business logic?

> Validation usually checks whether input conforms to expected structural and value constraints, while business logic enforces domain-specific rules and decisions.

Example:

```text
email must be valid
→ Validation

User cannot withdraw more than their balance
→ Business Logic
```

---

## Q8. Why shouldn't we directly pass `req.body` to the database?

> Because the client can send unexpected or sensitive fields. Explicitly selecting allowed fields or using validated schemas prevents issues such as mass assignment and accidental modification of protected properties.

---

## Q9. What is DTO?

> DTO stands for Data Transfer Object. It defines the shape of data transferred between application boundaries or layers and helps separate internal models from external API contracts.

---

## Q10. Does validation prevent SQL injection?

> No. Validation alone does not prevent SQL injection. SQL queries should use parameterized queries or safe database abstractions. Validation and database query safety solve different problems.

---

# 53. Quick Revision

Remember:

```text
VALIDATION
→ Is the data acceptable?

TRANSFORMATION
→ Convert the data into the required form.
```

### Example

```text
Input:
{
  "name": "  Lokendra ",
  "age": "23"
}
```

Transformation:

```text
"  Lokendra " → "Lokendra"
"23" → 23
```

Validation:

```text
name → string ✓
age → number ✓
age >= 18 ✓
```

---

# 54. Complete Mental Model

```text
                  HTTP Request
                       ↓
                 Deserialization
                       ↓
                Raw Application Data
                       ↓
             ┌─────────────────────┐
             │ Transformation      │
             │ trim                │
             │ normalize           │
             │ convert types       │
             └──────────┬──────────┘
                        ↓
             ┌─────────────────────┐
             │ Validation          │
             │ type                │
             │ format              │
             │ range               │
             │ required fields     │
             └──────────┬──────────┘
                        ↓
                    Controller
                        ↓
                     Service
                        ↓
                    Database
```

---

# 55. What You Should Know Before Moving On

* [ ] What validation is
* [ ] What transformation is
* [ ] Validation vs transformation
* [ ] Why backend validation is necessary
* [ ] Client-side vs server-side validation
* [ ] Schema validation
* [ ] Required-field validation
* [ ] Type validation
* [ ] Format validation
* [ ] Range validation
* [ ] Enum validation
* [ ] Cross-field validation
* [ ] Nested object validation
* [ ] Query parameter validation
* [ ] Route parameter validation
* [ ] Input normalization
* [ ] Whitelisting/allowlisting
* [ ] Mass assignment
* [ ] DTOs
* [ ] Business validation vs input validation
* [ ] Database constraints
* [ ] Validation vs sanitization
* [ ] Validation vs SQL injection protection
* [ ] Input and output transformation

---

# One-Line Interview Summary

> **Validation checks whether incoming data satisfies the application's expected rules, while transformation converts that data into a normalized or application-friendly representation before it is used by the backend.**
