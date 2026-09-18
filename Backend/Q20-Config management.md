# Config Management in Backend Development

## 1. What is Config Management?

Config management is the process of storing, loading, validating, and managing application configuration separately from application code.

Configuration means values that control how the application behaves.

Examples:

    PORT
    DATABASE_URL
    JWT_SECRET
    NODE_ENV
    REDIS_URL
    CLOUDINARY_API_KEY
    CLOUDINARY_API_SECRET
    SMTP_HOST
    SMTP_PORT

Instead of hardcoding these values inside the source code, we keep them in configuration.

Simple idea:

    Application Code
          +
    Configuration
          ↓
    Running Application

---

# 2. Why Do We Need Config Management?

Imagine this code:

    const port = 5000;

    const dbUrl = "mongodb://localhost:27017/myapp";

    const jwtSecret = "my-super-secret-key";

This creates several problems.

### Problem 1: Different environments

Development:

    localhost database

Production:

    production database

Testing:

    test database

We don't want to modify application code every time the environment changes.

---

### Problem 2: Secrets

Things like:

    JWT_SECRET
    DATABASE_PASSWORD
    API_KEY
    CLOUDINARY_SECRET

should not be committed to GitHub.

---

### Problem 3: Deployment

The same application should ideally run in:

    Development
    Testing
    Staging
    Production

with different configuration values.

---

# 3. Environment Configuration

A common approach is using environment variables.

Example:

    PORT=5000
    NODE_ENV=development
    DATABASE_URL=mongodb://localhost:27017/myapp
    JWT_SECRET=some-secret

The application reads these values at runtime.

In Node.js:

    process.env.PORT

    process.env.DATABASE_URL

    process.env.JWT_SECRET

Mental model:

    Environment
        ↓
    Environment Variables
        ↓
    Config Module
        ↓
    Application

---

# 4. What is an Environment Variable?

An environment variable is a value provided to the application by the environment in which it runs.

Example:

    PORT=5000

Your Node.js application can access it through:

    process.env.PORT

Another example:

    NODE_ENV=production

Access:

    process.env.NODE_ENV

---

# 5. `.env` File

During local development, developers commonly use a `.env` file.

Example:

    PORT=5000
    NODE_ENV=development
    DATABASE_URL=mongodb://localhost:27017/shop
    JWT_SECRET=my-secret

The application loads these variables into `process.env`.

Important:

    .env

should normally NOT be committed to Git when it contains secrets.

Add it to:

    .gitignore

Example:

    .env
    .env.local

---

# 6. `.env.example`

If `.env` should not be committed, how do other developers know which variables are required?

Use:

    .env.example

Example:

    PORT=
    NODE_ENV=
    DATABASE_URL=
    JWT_SECRET=
    REDIS_URL=

This file contains the required configuration names but not real secrets.

Typical workflow:

    .env.example
          ↓
    Developer creates
          ↓
    .env
          ↓
    Adds actual local values

---

# 7. Never Commit Secrets

Bad:

    JWT_SECRET=my-secret-password

inside source code.

Bad:

    const stripeKey = "sk_live_...";

Bad:

    const password = "admin123";

Better:

    process.env.JWT_SECRET

and configure the actual secret outside the source code.

---

# 8. Configuration vs Secrets

These concepts are related but not exactly the same.

## Configuration

Controls application behavior.

Examples:

    PORT=5000
    NODE_ENV=production
    LOG_LEVEL=info
    MAX_UPLOAD_SIZE=10MB

---

## Secrets

Sensitive configuration values.

Examples:

    DATABASE_PASSWORD
    JWT_SECRET
    API_KEY
    PRIVATE_KEY
    CLOUDINARY_API_SECRET

Secrets require additional protection.

---

# 9. Different Environments

A backend commonly has:

    Development
    Test
    Staging
    Production

Each environment can have different configuration.

Example:

### Development

    NODE_ENV=development
    PORT=5000
    DATABASE_URL=mongodb://localhost:27017/app-dev

### Test

    NODE_ENV=test
    PORT=5001
    DATABASE_URL=mongodb://localhost:27017/app-test

### Production

    NODE_ENV=production
    PORT=8080
    DATABASE_URL=<production-database>

The application code remains mostly the same.

Only configuration changes.

---

# 10. Why Not Hardcode Configuration?

Bad:

    if (environment === "production") {
      database = "mongodb://production-db";
    } else {
      database = "mongodb://localhost";
    }

As the application grows, this becomes difficult to maintain.

Better:

    DATABASE_URL

is supplied by the environment.

Then:

    development → development DB
    staging → staging DB
    production → production DB

---

# 11. Config Module

Instead of using `process.env` everywhere:

    process.env.DATABASE_URL
    process.env.JWT_SECRET
    process.env.REDIS_URL
    process.env.PORT

create a centralized configuration module.

Example:

    config/
      index.js

Conceptually:

    const config = {
      port: process.env.PORT,
      nodeEnv: process.env.NODE_ENV,
      databaseUrl: process.env.DATABASE_URL,
      jwtSecret: process.env.JWT_SECRET
    };

Then the rest of the application uses:

    config.port
    config.databaseUrl
    config.jwtSecret

Mental model:

    process.env
         ↓
    Config Module
         ↓
    Application

---

# 12. Why Centralize Configuration?

Without centralized configuration:

    Controller → process.env.JWT_SECRET

    Service → process.env.REDIS_URL

    Worker → process.env.REDIS_URL

    Email Service → process.env.SMTP_HOST

Configuration becomes scattered throughout the codebase.

With centralized configuration:

    Environment Variables
           ↓
       Config Module
           ↓
    ┌──────┼──────┐
    ↓      ↓      ↓
    API   Worker  Services

This makes configuration easier to:

- Understand
- Validate
- Test
- Change
- Maintain

---

# 13. Configuration Validation

One of the most important production practices is validating configuration when the application starts.

Suppose:

    DATABASE_URL

is missing.

Without validation:

    Server starts
         ↓
    Request arrives
         ↓
    Database connection fails
         ↓
    Runtime error

Better:

    Application starts
         ↓
    Validate configuration
         ↓
    DATABASE_URL missing
         ↓
    Fail immediately
         ↓
    Clear startup error

This is called fail-fast behavior.

---

# 14. Required vs Optional Configuration

Not every variable is required.

Example:

    DATABASE_URL       → required
    JWT_SECRET         → required
    PORT               → optional, default 5000
    LOG_LEVEL          → optional, default info

Conceptually:

    PORT = process.env.PORT || 5000

But for critical values:

    DATABASE_URL

should be required.

---

# 15. Configuration Defaults

Some configuration values can have safe defaults.

Example:

    PORT=5000

If PORT isn't provided:

    use 5000

Other examples:

    LOG_LEVEL=info
    REQUEST_TIMEOUT=5000
    MAX_CONNECTIONS=10

But don't provide dangerous defaults for secrets.

Bad:

    JWT_SECRET = process.env.JWT_SECRET || "default-secret"

If this reaches production, it becomes a security problem.

---

# 16. Type Conversion

Environment variables are strings.

For example:

    PORT=5000

is read as:

    "5000"

not:

    5000

If your application needs a number:

    Number(process.env.PORT)

Example:

    const port = Number(process.env.PORT);

Similarly:

    ENABLE_CACHE=true

is still the string:

    "true"

You should explicitly convert it to a boolean.

Conceptually:

    const enableCache = process.env.ENABLE_CACHE === "true";

---

# 17. Configuration Schema

A configuration schema defines:

- Required variables
- Data types
- Allowed values
- Defaults
- Validation rules

Example concept:

    PORT → number
    NODE_ENV → development | test | production
    DATABASE_URL → required string
    JWT_SECRET → required string
    LOG_LEVEL → debug | info | warn | error

This prevents invalid configuration from reaching the application.

---

# 18. Configuration Validation Libraries

In Node.js applications, configuration can be validated using libraries such as:

    Zod
    Joi
    envalid

Example concept using a schema:

    PORT must be a number
    NODE_ENV must be one of:
        development
        test
        production

If configuration is invalid:

    Application startup fails

The exact library is less important than the principle:

    Validate configuration before starting the application.

---

# 19. Configuration Loading Flow

A production backend can follow this flow:

    Operating Environment
             ↓
    Environment Variables
             ↓
    Load Configuration
             ↓
    Validate Configuration
             ↓
    Convert Types
             ↓
    Apply Defaults
             ↓
    Freeze/Expose Config
             ↓
    Application Starts

---

# 20. Secrets Management

For production systems, secrets are often not stored directly in `.env` files on servers.

Instead, organizations may use secret-management systems.

Examples:

    AWS Secrets Manager
    AWS Systems Manager Parameter Store
    HashiCorp Vault
    Azure Key Vault
    Google Secret Manager

Mental model:

    Secret Manager
          ↓
    Application
          ↓
    Secret available at runtime

This reduces the risk of accidentally exposing secrets.

---

# 21. `.env` vs Secret Manager

## `.env`

Good for:

    Local development
    Small projects
    Testing

Example:

    DATABASE_URL=...

---

## Secret Manager

Better suited for:

    Production
    Large teams
    Cloud deployments
    Rotating secrets
    Access control
    Auditing

Example:

    AWS Secrets Manager
          ↓
    Application
          ↓
    DATABASE_PASSWORD

---

# 22. Secret Rotation

Suppose a database password is compromised.

If secrets are managed properly:

    Old Secret
       ↓
    Rotate
       ↓
    New Secret

The application can receive the new value without requiring source-code changes.

Secret rotation is an important production security practice.

---

# 23. Configuration Hierarchy

A useful conceptual hierarchy:

    Hardcoded defaults
           ↓
    Configuration file
           ↓
    Environment variables
           ↓
    Secret manager
           ↓
    Runtime-specific overrides

The exact hierarchy depends on the deployment system.

The important principle is that environment-specific configuration should not require modifying application code.

---

# 24. Configuration and Docker

Docker applications commonly receive configuration through environment variables.

Example:

    docker run \
      -e NODE_ENV=production \
      -e PORT=8080 \
      -e DATABASE_URL=... \
      my-backend

The container image remains the same.

Only the configuration changes.

This supports:

    Build once
        ↓
    Deploy many environments

---

# 25. Configuration and Kubernetes

In Kubernetes, configuration is commonly provided using:

    ConfigMaps
    Secrets

Conceptually:

    ConfigMap
       ↓
    Non-sensitive configuration

    Secret
       ↓
    Sensitive configuration

Then:

    Kubernetes
        ↓
    Pod
        ↓
    Environment Variables
        ↓
    Backend

---

# 26. Configuration and CI/CD

CI/CD systems need configuration too.

Example:

    GitHub Actions
          ↓
    Deployment
          ↓
    Production Environment
          ↓
    Secrets / Variables
          ↓
    Application

You should not put production credentials directly into the repository.

CI/CD platforms generally provide secure secret/variable storage.

---

# 27. Configuration and Git

A common setup:

    project/
    ├── .env
    ├── .env.example
    ├── .gitignore
    ├── src/
    └── package.json

`.gitignore`:

    .env
    .env.*
    
    # But keep the example file
    !.env.example

The exact pattern depends on the project's needs.

---

# 28. Configuration Naming

Use clear and consistent names.

Good:

    DATABASE_URL
    JWT_SECRET
    REDIS_URL
    SMTP_HOST
    SMTP_PORT
    NODE_ENV
    LOG_LEVEL

Avoid unclear names:

    DB1
    KEY1
    SECRET
    CONFIG_VALUE

Configuration names should communicate their purpose.

---

# 29. Configuration Grouping

Large applications may organize configuration logically.

Conceptually:

    config/
      app
      database
      auth
      redis
      email
      storage

Example:

    config.database.url
    config.database.poolSize

    config.auth.jwtSecret
    config.auth.tokenExpiry

    config.redis.url

    config.email.host
    config.email.port

This becomes useful as the application grows.

---

# 30. Configuration Immutability

Once configuration has been loaded and validated, application code generally shouldn't modify it.

Mental model:

    Load
      ↓
    Validate
      ↓
    Create Config
      ↓
    Application reads config

Not:

    Controller modifies config
    Service modifies config
    Worker modifies config

Configuration should generally be treated as read-only runtime state.

---

# 31. Configuration and Testing

Testing usually requires different configuration.

Example:

Production:

    DATABASE_URL=production-db

Test:

    DATABASE_URL=test-db

You may also use:

    NODE_ENV=test

and separate:

    JWT_SECRET
    Redis configuration
    External API mocks

Tests should never accidentally connect to production resources.

---

# 32. Configuration and External Services

Suppose your backend uses:

    MongoDB
    Redis
    Cloudinary
    Stripe
    SMTP
    Elasticsearch

Configuration might contain:

    DATABASE_URL
    REDIS_URL
    CLOUDINARY_CLOUD_NAME
    CLOUDINARY_API_KEY
    CLOUDINARY_API_SECRET
    STRIPE_SECRET_KEY
    SMTP_HOST
    ELASTICSEARCH_URL

The application code should consume these through the configuration layer.

---

# 33. Configuration and Feature Flags

Configuration can also control features.

Example:

    ENABLE_NEW_CHECKOUT=true

or:

    ENABLE_RECOMMENDATIONS=false

This allows behavior to change without modifying application code.

However, large-scale feature flag systems often require dedicated tooling and management rather than simply using environment variables.

---

# 34. Configuration vs Feature Flags

They overlap but are different concepts.

## Configuration

Usually controls how the application operates.

Examples:

    PORT
    DATABASE_URL
    LOG_LEVEL

## Feature Flag

Controls whether a feature is enabled.

Examples:

    ENABLE_NEW_CHECKOUT
    ENABLE_AI_SEARCH

Simple distinction:

    Configuration → How should the application run?

    Feature Flag → Which functionality should be enabled?

---

# 35. Configuration Security

Important rules:

    Never commit secrets to Git
    Never expose secrets to frontend code
    Never log secrets
    Validate required variables
    Rotate compromised secrets
    Use secret managers in production
    Give services only required access
    Separate environments
    Avoid dangerous defaults

---

# 36. Backend vs Frontend Environment Variables

This is very important.

Backend:

    process.env.DATABASE_URL

can contain sensitive values because the backend runs on a trusted server environment.

Frontend:

    environment variables are often bundled into JavaScript
        ↓
    Users can inspect them

Therefore:

    NEVER put backend secrets into frontend-exposed variables.

For example:

    DATABASE_PASSWORD
    JWT_SECRET
    STRIPE_SECRET_KEY

must remain server-side.

Public frontend configuration is different.

Example:

    API_BASE_URL
    PUBLIC_ANALYTICS_ID

Even then, treat anything shipped to the browser as public.

---

# 37. Configuration Failure Strategy

Suppose:

    DATABASE_URL is missing

A good application should fail during startup.

Example conceptual output:

    Configuration error:
    DATABASE_URL is required

Instead of:

    Server started successfully

followed by random failures later.

This is another example of:

    Fail Fast

---

# 38. Config Management in a Node.js Project

A practical project structure:

    src/
    ├── config/
    │   ├── index.js
    │   ├── database.js
    │   ├── redis.js
    │   └── email.js
    │
    ├── controllers/
    ├── services/
    ├── repositories/
    ├── middleware/
    ├── routes/
    └── app.js

Conceptually:

    config/index.js
          ↓
    Loads environment variables
          ↓
    Validates configuration
          ↓
    Exports config
          ↓
    Other modules consume config

---

# 39. Example Configuration Flow

Suppose `.env` contains:

    PORT=5000
    NODE_ENV=development
    DATABASE_URL=mongodb://localhost:27017/shop
    JWT_SECRET=some-secret

Application:

    Environment Variables
            ↓
    Config Module
            ↓
    Validate
            ↓
    Convert PORT to number
            ↓
    Export configuration
            ↓
    Server + Database + Services

Then application code uses:

    config.port

instead of repeatedly accessing:

    process.env.PORT

---

# 40. Production Architecture

A more production-oriented setup:

    Developer
       ↓
    Source Code
       ↓
    CI/CD
       ↓
    Container/Image
       ↓
    Deployment Environment
       ↓
    Environment Variables
       +
    Secret Manager
       ↓
    Config Loader
       ↓
    Config Validation
       ↓
    Application

Important principle:

    Configuration should be supplied at deployment/runtime,
    not hardcoded into the application source.

---

# 41. Common Mistakes

## Mistake 1: Committing `.env`

Never commit real production secrets.

---

## Mistake 2: Hardcoding secrets

Bad:

    const JWT_SECRET = "123456";

---

## Mistake 3: No validation

Application starts with:

    DATABASE_URL = undefined

and fails later.

---

## Mistake 4: Treating environment variables as typed values

Remember:

    process.env.PORT

is a string.

---

## Mistake 5: Using production secrets in development

Keep environments separated.

---

## Mistake 6: Exposing backend secrets to frontend

Anything sent to the browser should be considered public.

---

## Mistake 7: Logging environment variables

Bad:

    console.log(process.env);

This could expose secrets.

---

## Mistake 8: Using unsafe defaults

Bad:

    JWT_SECRET || "default-secret"

---

## Mistake 9: Scattering `process.env` everywhere

Centralize configuration where practical.

---

# 42. Best Practices

### 1. Keep configuration outside business logic

Business logic shouldn't care where configuration came from.

### 2. Centralize configuration

Use a dedicated config module.

### 3. Validate at startup

Fail fast when required configuration is missing or invalid.

### 4. Separate environments

Keep:

    development
    test
    staging
    production

separate.

### 5. Protect secrets

Use secret managers for production-sensitive values.

### 6. Use `.env.example`

Document required variables without exposing actual secrets.

### 7. Convert types

Explicitly convert:

    strings → numbers
    strings → booleans

### 8. Use safe defaults

Only where appropriate.

### 9. Never expose secrets

Especially to frontend or logs.

### 10. Keep configuration immutable

Load once and treat it as read-only.

---

# 43. Config Management vs Hardcoding

| Hardcoding | Config Management |
|---|---|
| Values inside source code | Values outside source code |
| Difficult to change | Easy to change |
| Environment-specific code | Same code across environments |
| Secrets may leak | Secrets can be managed securely |
| Difficult deployment | Easier deployment |
| Poor scalability | Better production practice |

---

# 44. Config Management vs `.env`

These are not the same thing.

`.env` is one mechanism for providing configuration.

Config management is the broader concept.

    Config Management
          |
          +-- Environment Variables
          +-- .env files
          +-- Config Modules
          +-- Config Validation
          +-- Secret Managers
          +-- ConfigMaps
          +-- CI/CD Variables
          +-- Runtime Configuration

---

# 45. Interview Questions

## Q1. What is config management?

Config management is the practice of storing, loading, validating, and managing application configuration separately from application code so the same application can run across different environments safely.

---

## Q2. Why shouldn't we hardcode configuration?

Because configuration varies between environments and hardcoding can expose secrets, make deployments difficult, and require code changes for environment-specific behavior.

---

## Q3. What are environment variables?

They are runtime-provided values that applications can use for configuration.

Example:

    process.env.DATABASE_URL

---

## Q4. Why use `.env`?

It provides a convenient way to manage local environment variables during development.

It should not contain production secrets committed to source control.

---

## Q5. What is `.env.example`?

It documents the required environment variable names without containing real secret values.

---

## Q6. Why validate configuration at startup?

To fail fast and detect missing or invalid configuration before the application starts serving requests.

---

## Q7. Are environment variables strings?

Yes. Values from `process.env` are strings, so numbers and booleans should be explicitly converted.

---

## Q8. What is a secret manager?

A system designed to securely store, control access to, audit, and often rotate sensitive configuration such as passwords, API keys, and tokens.

---

## Q9. Where should production secrets be stored?

Typically in a secure secret-management system or secure deployment environment, rather than in source code or Git.

---

## Q10. Why centralize configuration?

It prevents configuration from being scattered throughout the application and gives you one place to validate, transform, and expose configuration.

---

# 46. Real Interview Scenario

### Interviewer:

Your application works locally but fails in production because the database connection is undefined. How would you prevent this?

### Answer:

I would centralize configuration and validate required environment variables during application startup. `DATABASE_URL` would be marked as required. If it is missing or invalid, the application should fail fast with a clear configuration error instead of starting and failing later when requests arrive.

---

# 47. Another Interview Scenario

### Interviewer:

How would you manage secrets such as JWT secrets and database passwords?

### Answer:

I would keep them outside the source code. For local development I could use a `.env` file that is excluded from Git. In production I would preferably use a secret-management system or secure deployment environment. I would also avoid logging or exposing those values.

---

# 48. Another Interview Scenario

### Interviewer:

How can the same Docker image run in development, staging, and production?

### Answer:

I would keep the application code and image the same and provide environment-specific configuration at runtime. For example, each environment can supply a different `DATABASE_URL`, API credentials, logging configuration, and other settings.

---

# 49. Another Interview Scenario

### Interviewer:

Why shouldn't we access `process.env` everywhere?

### Answer:

Directly accessing `process.env` throughout the codebase scatters configuration concerns. I would centralize configuration in a config module, validate it once, convert types there, and let the rest of the application consume the validated configuration.

---

# 50. Production Checklist

    [ ] Configuration is separate from application code
    [ ] `.env` is not committed
    [ ] `.env.example` documents required variables
    [ ] Secrets are protected
    [ ] Production uses secure secret management where appropriate
    [ ] Required variables are validated
    [ ] Configuration is validated at startup
    [ ] Environment variables are type-converted
    [ ] Safe defaults are used where appropriate
    [ ] Development/test/production are separated
    [ ] Secrets are never logged
    [ ] Backend secrets are never exposed to frontend
    [ ] Configuration is centralized
    [ ] Configuration is treated as read-only
    [ ] CI/CD secrets are stored securely
    [ ] Production credentials are not present in source control

---

# 51. Quick Revision

    Config Management
          ↓
    Store configuration separately
          ↓
    Load configuration
          ↓
    Validate configuration
          ↓
    Convert types
          ↓
    Apply safe defaults
          ↓
    Expose through Config Module
          ↓
    Application uses config

Important concepts:

    Environment Variables
    .env
    .env.example
    Config Module
    Config Validation
    Required Variables
    Optional Variables
    Defaults
    Type Conversion
    Secret Management
    Secret Rotation
    Development / Test / Staging / Production
    Docker Configuration
    Kubernetes ConfigMaps / Secrets
    CI/CD Variables
    Feature Flags
    Fail Fast

Most important rule:

    Never hardcode secrets.

---

# 52. One-Line Interview Summary

> Config management is the practice of keeping environment-specific settings and secrets outside application code, loading and validating them at startup, and providing them to the application through a centralized configuration layer.