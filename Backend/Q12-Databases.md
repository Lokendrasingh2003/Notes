# Backend Development — Databases

## 1. What is a Database?

A **database** is an organized system used to store, manage, retrieve, and modify data.

For example, an e-commerce application may store:

    Users
    Products
    Orders
    Payments
    Reviews

Instead of storing this information directly inside application code, we store it in a database.

Mental model:

    Application
         ↓
      Database
         ↓
       Data

---

# 2. Why Do We Need Databases?

Imagine an application with 1 million users.

We cannot realistically store all user data inside:

    JavaScript variables
    JSON files
    Local files

A database provides:

    Persistent storage
    Fast data retrieval
    Data modification
    Data relationships
    Data consistency
    Security
    Concurrency handling
    Transactions
    Indexing
    Scalability

For example:

    User registers
         ↓
    Backend receives request
         ↓
    Database stores user
         ↓
    User data remains available
         ↓
    User can log in later

---

# 3. Database Types

The two major categories you should understand are:

    1. Relational Databases
    2. NoSQL Databases

Examples:

    Relational:
    PostgreSQL
    MySQL
    SQL Server
    Oracle

    NoSQL:
    MongoDB
    Redis
    Cassandra
    DynamoDB

Mental model:

    SQL
    → Tables
    → Rows
    → Columns
    → Relationships

    NoSQL
    → Different data models
    → Documents / Key-Value / Wide-Column / Graph

---

# 4. Relational Database

A relational database stores data in **tables**.

Example:

    users

    +----+----------+----------------------+
    | id | name     | email                |
    +----+----------+----------------------+
    | 1  | Lokendra | lokendra@example.com |
    | 2  | Rahul    | rahul@example.com    |
    +----+----------+----------------------+

Each table contains:

    Rows
    Columns

A row represents a record.

A column represents an attribute.

---

# 5. Tables, Rows and Columns

Example:

    users

    id | name     | email
    ---|----------|--------------------
    1  | Lokendra | lokendra@example.com
    2  | Rahul    | rahul@example.com

Here:

    Table
    → users

    Row
    → One user

    Column
    → id, name, email

    Cell
    → One individual value

For example:

    Lokendra

is a value inside the `name` column.

---

# 6. NoSQL Database

NoSQL databases don't necessarily use tables and rows.

MongoDB, for example, stores data as documents.

Example:

    {
      "_id": 101,
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

A collection can contain many documents:

    users
       ↓
    Document
    Document
    Document

MongoDB uses:

    Database
       ↓
    Collection
       ↓
    Document
       ↓
    Fields

---

# 7. SQL vs NoSQL

### SQL

Usually uses:

    Tables
    Rows
    Columns
    Relationships
    SQL queries

Examples:

    PostgreSQL
    MySQL

### NoSQL

Can use models such as:

    Documents
    Key-value
    Wide-column
    Graph

Examples:

    MongoDB
    Redis
    Cassandra
    DynamoDB

Important:

> **NoSQL does not simply mean "no SQL." It generally refers to database systems that use non-relational or non-tabular data models, although some NoSQL databases can support SQL-like querying.**

---

# 8. When to Use SQL?

SQL databases are a strong choice when:

    Data has clear relationships
    Transactions are important
    Data consistency is critical
    Complex queries are required
    Structured schema is useful

Examples:

    Banking
    Accounting
    Order management
    Inventory
    ERP systems

Example relationship:

    Customer
       ↓
    Orders
       ↓
    Products

Relational databases are very good at representing these relationships.

---

# 9. When to Use NoSQL?

NoSQL databases can be useful when:

    Data structure changes frequently
    Flexible schemas are useful
    Large-scale distributed systems are needed
    Document-oriented data fits naturally
    Very high throughput is required for the workload

Examples:

    Content management
    Product catalogs
    Event data
    Real-time applications
    Large distributed systems

The correct choice depends on the application's access patterns and consistency requirements.

---

# 10. SQL

SQL stands for:

    Structured Query Language

It is used to interact with relational databases.

Common operations:

    SELECT
    INSERT
    UPDATE
    DELETE

These correspond closely to CRUD.

Example:

    SELECT * FROM users;

This retrieves users.

---

# 11. INSERT

Used to create records.

Example:

    INSERT INTO users (name, email)
    VALUES ('Lokendra', 'lokendra@example.com');

This creates a new user.

---

# 12. SELECT

Used to read data.

Example:

    SELECT * FROM users;

Get specific columns:

    SELECT name, email
    FROM users;

Get a specific user:

    SELECT *
    FROM users
    WHERE id = 101;

---

# 13. UPDATE

Used to modify existing records.

Example:

    UPDATE users
    SET name = 'Lokendra Singh'
    WHERE id = 101;

The `WHERE` clause is extremely important.

Without it:

    UPDATE users
    SET name = 'Lokendra Singh';

could update every row.

---

# 14. DELETE

Used to remove records.

Example:

    DELETE FROM users
    WHERE id = 101;

Again, the `WHERE` clause is important.

Without it:

    DELETE FROM users;

could delete every record in the table.

---

# 15. Primary Key

A **primary key** uniquely identifies each row in a table.

Example:

    users

    id | name
    ---|--------
    1  | Lokendra
    2  | Rahul
    3  | Amit

Here:

    id

can be the primary key.

Properties:

    Unique
    Not NULL
    Identifies one record

Example:

    PRIMARY KEY (id)

---

# 16. Foreign Key

A **foreign key** is a column that references a key in another table.

Example:

    users

    id | name
    ---|--------
    1  | Lokendra
    2  | Rahul

    orders

    id | user_id | amount
    ---|---------|-------
    10 | 1       | 500
    11 | 1       | 1000
    12 | 2       | 700

Here:

    orders.user_id

references:

    users.id

This creates a relationship between the tables.

---

# 17. Database Relationships

Common relationships are:

    One-to-One
    One-to-Many
    Many-to-Many

Example:

    User → Profile
    One-to-One

    User → Orders
    One-to-Many

    Students → Courses
    Many-to-Many

Understanding relationships is very important when designing relational databases.

---

# 18. One-to-One

One record in Table A is related to one record in Table B.

Example:

    User
       ↓
    Profile

A user has one profile.

Example:

    users
    id | name

    profiles
    id | user_id | bio

---

# 19. One-to-Many

One record is related to many records.

Example:

    User
       ↓
    Orders
       ↓
    Order 1
    Order 2
    Order 3

One user can have many orders.

Database:

    users

    id | name
    ---|--------
    1  | Lokendra

    orders

    id | user_id | amount
    ---|---------|-------
    10 | 1       | 500
    11 | 1       | 1000

---

# 20. Many-to-Many

Many records from one table can relate to many records from another table.

Example:

    Students
       ↕
    Courses

One student can take multiple courses.

One course can have multiple students.

This is commonly implemented using a junction table.

Example:

    students
    id | name

    courses
    id | name

    student_courses
    student_id | course_id

---

# 21. JOIN

A JOIN combines related data from multiple tables.

Example:

    users

    id | name
    ---|--------
    1  | Lokendra

    orders

    id | user_id | amount
    ---|---------|-------
    10 | 1       | 500

Query:

    SELECT users.name, orders.amount
    FROM users
    JOIN orders
      ON users.id = orders.user_id;

Result:

    name     | amount
    ----------|-------
    Lokendra | 500

---

# 22. Types of JOIN

Common SQL joins:

    INNER JOIN
    LEFT JOIN
    RIGHT JOIN
    FULL OUTER JOIN

### INNER JOIN

Returns records that have matching records in both tables.

### LEFT JOIN

Returns all records from the left table and matching records from the right table.

### RIGHT JOIN

Returns all records from the right table and matching records from the left table.

### FULL OUTER JOIN

Returns matching records plus non-matching records from both sides.

In practical backend development, `INNER JOIN` and `LEFT JOIN` are especially common.

---

# 23. Normalization

Normalization is a database design technique used to reduce unnecessary duplication and improve data integrity.

Bad design:

    orders

    id | user_name | user_email | product
    ---------------------------------------
    1  | Lokendra  | email      | iPhone
    2  | Lokendra  | email      | Laptop

User information is repeated.

Better design:

    users
    id | name | email

    orders
    id | user_id | product_id

Now user information is stored separately.

Benefits:

    Less duplication
    Better consistency
    Easier updates
    Better data integrity

---

# 24. Denormalization

Denormalization intentionally duplicates some data to improve read performance or simplify queries.

Example:

    orders

    id | user_id | user_name | amount

Instead of always joining with:

    users

The user name is stored directly in the order.

Advantages:

    Faster reads in some workloads
    Fewer joins

Disadvantages:

    Duplicate data
    More complex updates
    Potential inconsistency

Denormalization should be based on actual application requirements.

---

# 25. Indexes

An **index** is a database data structure that helps the database find rows more efficiently.

Without an index:

    Database
       ↓
    Scan many rows
       ↓
    Find matching row

With an index:

    Query
       ↓
    Index
       ↓
    Relevant rows

Example:

    SELECT *
    FROM users
    WHERE email = 'lokendra@example.com';

If `email` is indexed, the database can locate matching rows much more efficiently.

---

# 26. Why Not Index Every Column?

Indexes improve reads but have costs.

Every index can require:

    Additional storage
    Additional write work
    Maintenance

When data changes:

    INSERT
    UPDATE
    DELETE

the database may also need to update indexes.

Therefore:

> **Indexes should be created based on query patterns, not blindly on every column.**

---

# 27. Composite Index

A composite index contains multiple columns.

Example:

    CREATE INDEX idx_users_name_email
    ON users(name, email);

This can help queries that use the indexed columns in patterns compatible with the index.

The order of columns matters.

For example:

    (name, email)

is different from:

    (email, name)

Index design should be based on real query patterns.

---

# 28. Transactions

A transaction groups multiple database operations into one logical unit.

Example:

    Transfer ₹1000

    Account A
       ↓
    Deduct ₹1000

    Account B
       ↓
    Add ₹1000

Both operations should succeed together.

If one operation fails:

    Rollback

The database returns to the previous consistent state.

---

# 29. ACID

Transactions are commonly discussed using ACID properties.

    A → Atomicity
    C → Consistency
    I → Isolation
    D → Durability

---

# 30. Atomicity

Atomicity means:

> **A transaction is treated as one unit: either all required operations succeed or the transaction is rolled back.**

Example:

    Deduct money
       ↓
    Add money

If adding money fails:

    Deduct money
       ↓
    ROLLBACK

The partial operation should not remain committed.

---

# 31. Consistency

Consistency means a transaction should move the database from one valid state to another valid state while respecting defined rules and constraints.

Example:

    Account balance cannot become invalid according to the application's/database's constraints.

Before transaction:

    Valid state

After successful transaction:

    Valid state

---

# 32. Isolation

Isolation controls how concurrent transactions interact with each other.

Imagine:

    Transaction A
    Transaction B

running at the same time.

Isolation helps prevent one transaction from incorrectly seeing or interfering with another transaction's intermediate state.

---

# 33. Durability

Once a transaction is successfully committed, the database should preserve that committed data even if there is a subsequent failure such as a system restart.

Mental model:

    COMMIT
       ↓
    Data is durable

---

# 34. Isolation Levels

Common SQL transaction isolation levels are:

    Read Uncommitted
    Read Committed
    Repeatable Read
    Serializable

Higher isolation generally provides stronger consistency guarantees but may reduce concurrency or increase contention.

Different databases implement and optimize these levels differently.

---

# 35. Connection Pooling

Creating a new database connection for every request can be expensive.

Instead, applications commonly use a **connection pool**.

Example:

    Application
       ↓
    Connection Pool
       ↓
    Connection 1
    Connection 2
    Connection 3
    Connection 4
       ↓
    Database

A request can borrow a connection from the pool and return it when finished.

Benefits:

    Better performance
    Lower connection overhead
    Controlled database connections
    Better concurrency

---

# 36. Database Connection Flow

Typical backend application:

    Server starts
       ↓
    Database connection/pool initialized
       ↓
    Application starts accepting requests
       ↓
    Request arrives
       ↓
    Database connection acquired
       ↓
    Query executed
       ↓
    Connection returned to pool

---

# 37. ORM

ORM stands for:

    Object-Relational Mapping

An ORM allows developers to work with database records using programming-language objects/models instead of writing every SQL query manually.

Examples:

    Prisma
    Sequelize
    TypeORM
    Hibernate

Instead of:

    SELECT * FROM users
    WHERE id = 101;

you might write something conceptually like:

    User.findById(101)

The exact syntax depends on the ORM.

---

# 38. Advantages of ORM

ORMs can provide:

    Faster development
    Type safety in some tools
    Model abstractions
    Relationship handling
    Migrations
    Query building
    Reusable database logic

However, developers should still understand SQL and database concepts.

An ORM does not eliminate the need to understand:

    Indexes
    Joins
    Transactions
    Query performance
    Data modeling

---

# 39. ODM

ODM stands for:

    Object-Document Mapping

It is commonly used with document databases.

For example:

    MongoDB
       ↓
    Mongoose
       ↓
    JavaScript objects/models

Example Mongoose schema:

    const userSchema = new mongoose.Schema({
      name: String,
      email: String
    });

Mongoose is an ODM rather than a traditional relational ORM.

---

# 40. MongoDB Structure

MongoDB commonly follows:

    Database
       ↓
    Collection
       ↓
    Document
       ↓
    Fields

Example:

    ecommerce
       ↓
    users
       ↓
    {
      "_id": 101,
      "name": "Lokendra",
      "email": "lokendra@example.com"
    }

SQL equivalent mental model:

    Database
       ↓
    Table
       ↓
    Row
       ↓
    Columns

MongoDB:

    Database
       ↓
    Collection
       ↓
    Document
       ↓
    Fields

---

# 41. Embedding vs Referencing in MongoDB

MongoDB allows related data to be modeled in different ways.

### Embedding

Store related data inside the same document.

Example:

    {
      "_id": 101,
      "name": "Lokendra",
      "addresses": [
        {
          "city": "Delhi",
          "type": "home"
        }
      ]
    }

Useful when the related data is:

    Frequently accessed together
    Tightly related
    Reasonably bounded in size

### Referencing

Store a reference to another document.

Example:

    {
      "_id": 101,
      "name": "Lokendra",
      "addressId": 500
    }

Useful when:

    Related data is large
    Data is shared
    Independent access is common
    Relationships are complex

---

# 42. Schema

A schema defines the expected structure and rules for data.

Example:

    User

    id
    name
    email
    age

A schema can define:

    Data types
    Required fields
    Defaults
    Constraints
    Relationships

SQL databases generally enforce schemas at the database level.

MongoDB is flexible by default, although applications can enforce structure using tools such as Mongoose or validation rules.

---

# 43. Schema Migration

A migration is a controlled change to database structure.

Example:

    Version 1
    users:
      id
      name

    Version 2
    users:
      id
      name
      phone

A migration can add:

    phone

to the database schema.

Migrations are important because production databases evolve over time.

---

# 44. Database Constraints

Constraints help maintain data integrity.

Common SQL constraints:

    PRIMARY KEY
    FOREIGN KEY
    UNIQUE
    NOT NULL
    CHECK
    DEFAULT

Example:

    email VARCHAR(255) UNIQUE NOT NULL

This means:

    email cannot be NULL
    email cannot be duplicated

---

# 45. Unique Constraint

Suppose every user must have a unique email.

Database constraint:

    UNIQUE(email)

Then:

    lokendra@example.com

cannot appear twice.

This is important because application-level checks alone can suffer from race conditions.

---

# 46. Database Query Optimization

Suppose this query is slow:

    SELECT *
    FROM users
    WHERE email = 'lokendra@example.com';

Possible optimization:

    Create an index on email

But optimization should be based on actual evidence.

Useful techniques:

    EXPLAIN
    Query profiling
    Index analysis
    Slow query logs
    Query optimization

Do not assume an index is always the correct solution.

---

# 47. N+1 Query Problem

The N+1 problem happens when an application performs one query to fetch a list and then one additional query for each item.

Example:

    Query 1
    → Get 100 users

Then:

    Query 2 → Orders for user 1
    Query 3 → Orders for user 2
    Query 4 → Orders for user 3
    ...
    Query 101 → Orders for user 100

Total:

    101 queries

Possible solutions:

    JOINs
    Eager loading
    Batch queries
    Data loaders
    Better query design

---

# 48. Database Replication

Replication means maintaining copies of database data on multiple database servers.

Example:

    Primary
       ↓
    Replica 1
    Replica 2

The primary may handle writes.

Replicas can handle some read traffic depending on the database and application design.

Benefits:

    High availability
    Read scaling
    Disaster recovery

Replication introduces considerations such as:

    Replication lag
    Failover
    Consistency

---

# 49. Read Replicas

A common architecture is:

    Application
       ↓
    Write
       ↓
    Primary Database

    Application
       ↓
    Read
       ↓
    Read Replica

This can distribute read-heavy workloads.

However, if replication is asynchronous, a recently written value may not immediately appear on a replica.

---

# 50. Sharding

Sharding means splitting data across multiple database servers.

Example:

    Users 1–1,000,000
       ↓
    Shard 1

    Users 1,000,001–2,000,000
       ↓
    Shard 2

    Users 2,000,001–3,000,000
       ↓
    Shard 3

Each shard contains part of the overall dataset.

Sharding can help with very large-scale systems but adds significant operational and application complexity.

---

# 51. Database Backup

Production databases should have a backup strategy.

Backups protect against:

    Accidental deletion
    Data corruption
    Hardware failures
    Operational mistakes
    Certain security incidents

Common concepts:

    Full backup
    Incremental backup
    Point-in-time recovery
    Backup retention
    Restore testing

A backup is only useful if it can actually be restored.

---

# 52. Database Security

Important database security practices include:

    Strong authentication
    Least-privilege access
    Encryption in transit
    Encryption at rest where appropriate
    Secure credential management
    Network restrictions
    Regular backups
    Auditing
    Parameterized queries
    Dependency and database patching

Never hardcode database credentials:

    DB_PASSWORD = "mysecretpassword"

Instead, use:

    Environment variables
    Secret managers
    Secure configuration systems

---

# 53. SQL Injection

SQL injection occurs when untrusted user input is incorrectly incorporated into SQL statements.

Unsafe concept:

    "SELECT * FROM users WHERE email = '" + email + "'"

If input is malicious, it may alter the intended query.

Use:

    Parameterized queries
    Prepared statements
    Safe query builders
    Proper ORM mechanisms

The database layer should never blindly trust user input.

---

# 54. Database and Backend Architecture

A typical backend can be structured as:

    Client
       ↓
    API
       ↓
    Controller
       ↓
    Service
       ↓
    Repository / Data Access Layer
       ↓
    Database

Example:

    GET /users/101
       ↓
    UserController
       ↓
    UserService
       ↓
    UserRepository
       ↓
    PostgreSQL
       ↓
    UserRepository
       ↓
    UserService
       ↓
    UserController
       ↓
    JSON Response

This keeps database-specific logic separated from HTTP handling and business logic.

---

# 55. Database Best Practices

Follow these principles:

    1. Choose the database based on application requirements.

    2. Design the data model carefully.

    3. Use primary keys.

    4. Use foreign keys where appropriate.

    5. Use constraints for data integrity.

    6. Create indexes based on real query patterns.

    7. Avoid unnecessary indexes.

    8. Use transactions when multiple operations must succeed together.

    9. Use connection pooling.

    10. Validate input before database operations.

    11. Use parameterized queries.

    12. Avoid N+1 queries.

    13. Use pagination for large datasets.

    14. Monitor slow queries.

    15. Maintain backups.

    16. Test database restore procedures.

    17. Use migrations for schema changes.

    18. Follow least-privilege database access.

    19. Never expose database credentials.

    20. Monitor database health and capacity.

---

# 56. SQL vs NoSQL — Interview Comparison

| Feature | SQL | NoSQL |
|---|---|---|
| Data model | Relational | Various non-relational models |
| Common structure | Tables | Documents / key-value / other models |
| Schema | Usually structured | Often flexible, depending on database |
| Relationships | Strong relational support | Varies by database |
| Transactions | Strong support | Varies by database |
| Query language | SQL | Database-specific APIs/query languages |
| Scaling | Often strong vertical + some horizontal options | Often designed with distributed scaling in mind |
| Best for | Structured relational data | Flexible/distributed workloads |
| Examples | PostgreSQL, MySQL | MongoDB, Redis, Cassandra |

Important:

> **There is no universal "SQL is better" or "NoSQL is better" rule. The choice depends on data relationships, consistency requirements, access patterns, scale, and operational needs.**

---

# 57. PostgreSQL vs MongoDB

### PostgreSQL

Good choice when:

    Relationships are important
    Strong transactional guarantees are needed
    Complex SQL queries are required
    Structured data is appropriate

### MongoDB

Good choice when:

    Document-oriented data fits naturally
    Flexible document structures are useful
    Related data can often be accessed as documents
    Distributed document workloads fit the database

For a MERN application:

    React
       ↓
    Node.js
       ↓
    Express
       ↓
    MongoDB

MongoDB is commonly used as the database layer.

---

# 58. Database Selection Example

Suppose we are building a banking application.

Requirements:

    Account balances
    Money transfers
    Transactions
    Strong consistency
    Complex relationships

A relational database such as PostgreSQL can be a strong choice.

Now suppose we are building a content application with flexible document structures.

Requirements:

    Frequently changing document fields
    Document-oriented content
    Large-scale reads

A document database such as MongoDB may be a suitable choice.

The final decision should be based on the actual workload and system requirements.

---

# 59. Database Mental Model

Remember:

    Application
         ↓
    Database Driver / ORM / ODM
         ↓
    Connection Pool
         ↓
    Database
         ↓
    Query
         ↓
    Data
         ↓
    Response

For SQL:

    Database
       ↓
    Table
       ↓
    Row
       ↓
    Column

For MongoDB:

    Database
       ↓
    Collection
       ↓
    Document
       ↓
    Field

---

# 60. Interview Questions

## Q1. What is a database?

> **A database is a system used to persist, manage, retrieve, and modify structured or unstructured data efficiently.**

## Q2. What is the difference between SQL and NoSQL?

> **SQL databases are relational and organize data primarily into tables with defined relationships, while NoSQL databases use different data models such as documents, key-value, wide-column, or graph models.**

## Q3. What is a primary key?

> **A primary key uniquely identifies a row in a relational table.**

## Q4. What is a foreign key?

> **A foreign key references a key in another table and is used to represent relationships between tables.**

## Q5. What is an index?

> **An index is a data structure that helps the database locate matching records more efficiently, at the cost of additional storage and write maintenance.**

## Q6. Why don't we index every column?

> **Indexes improve some read queries but consume storage and add overhead to inserts, updates, and deletes, so they should be created based on actual query patterns.**

## Q7. What is normalization?

> **Normalization is a database design technique that reduces unnecessary data duplication and improves data integrity.**

## Q8. What is a transaction?

> **A transaction is a group of database operations treated as one logical unit so that the required changes can succeed or fail together.**

## Q9. What is ACID?

> **ACID stands for Atomicity, Consistency, Isolation, and Durability, which describe important transaction properties.**

## Q10. What is connection pooling?

> **Connection pooling maintains a reusable set of database connections so applications don't need to create a new connection for every request.**

## Q11. What is an ORM?

> **ORM stands for Object-Relational Mapping. It allows applications to interact with relational databases through programming-language abstractions such as models and objects.**

## Q12. What is the N+1 query problem?

> **It occurs when an application performs one query to fetch a collection and then performs an additional query for each item, resulting in unnecessarily many database queries.**

## Q13. When would you choose SQL over NoSQL?

> **I would generally prefer SQL when the application has strong relationships, transactional requirements, structured data, and complex relational queries.**

## Q14. When would you choose NoSQL?

> **I would consider NoSQL when its data model fits the workload well, especially for flexible document structures or certain large-scale distributed access patterns.**

## Q15. What is database replication?

> **Replication maintains copies of database data on multiple servers, which can improve availability, read scalability, or recovery depending on the architecture.**

---

# 61. Interview Scenario

### Interviewer:

> "How would you choose a database for a new application?"

A strong answer:

> **"I would first understand the application's data model and access patterns. I would look at relationships between entities, transaction and consistency requirements, read/write patterns, expected scale, query complexity, availability requirements, and operational constraints. If the data is strongly relational and transactional, PostgreSQL or another relational database may be appropriate. If a document-oriented model and flexible schema fit the workload better, I would consider MongoDB or another suitable NoSQL database."**

---

# 62. Interview Scenario — Slow Query

### Interviewer:

> "A query is taking 5 seconds. What would you do?"

A strong approach:

    1. Reproduce the slow query.

    2. Inspect the query execution plan.

    3. Check whether appropriate indexes exist.

    4. Check whether the query scans too many rows.

    5. Select only required columns.

    6. Check joins and filters.

    7. Check for N+1 queries.

    8. Check database load.

    9. Consider query/data-model changes.

    10. Measure again after optimization.

Important:

> **Don't blindly add indexes. First understand why the query is slow.**

---

# 63. Interview Scenario — Database Scaling

### Interviewer:

> "Your database is receiving too many read requests. What can you do?"

Possible approaches:

    Query optimization
       ↓
    Proper indexes
       ↓
    Caching
       ↓
    Read replicas
       ↓
    Database partitioning/sharding if necessary

The correct solution depends on the bottleneck.

---

# 64. Quick Revision

    Database
    → Persistent data storage and management

    SQL
    → Relational database

    NoSQL
    → Non-relational database models

    SQL examples
    → PostgreSQL
    → MySQL

    NoSQL examples
    → MongoDB
    → Redis
    → Cassandra

    SQL
    → Table
    → Row
    → Column

    MongoDB
    → Collection
    → Document
    → Field

    Primary Key
    → Uniquely identifies a row

    Foreign Key
    → References another table's key

    JOIN
    → Combines related data

    Index
    → Faster lookups
    → Extra storage/write cost

    Normalization
    → Reduce unnecessary duplication

    Denormalization
    → Intentionally duplicate data for specific performance/design needs

    Transaction
    → Group of operations treated as one unit

    ACID
    → Atomicity
    → Consistency
    → Isolation
    → Durability

    Connection Pool
    → Reuse database connections

    ORM
    → Object-Relational Mapping

    ODM
    → Object-Document Mapping

    N+1
    → Too many individual database queries

    Replication
    → Multiple copies of database data

    Sharding
    → Split data across multiple database nodes

    Migration
    → Controlled database schema change

    SQL Injection
    → Malicious query manipulation
    → Prevent with parameterized queries

---

# 65. One-Line Interview Summary

> **A database provides persistent and reliable data storage; backend developers choose between relational and non-relational systems based on data relationships, consistency, transactions, access patterns, and scale, while using indexes, transactions, connection pooling, constraints, and proper query design for performance and reliability.**