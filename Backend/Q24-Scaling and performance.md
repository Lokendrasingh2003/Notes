# Backend Scaling and Performance

## 1. What is Performance?

Backend performance is about how efficiently a system handles requests.

Important performance metrics include:

- Response time
- Throughput
- Latency
- CPU usage
- Memory usage
- Database query time
- Network usage
- Error rate

### Simple example

If:

    Request → API → Database → Response

takes 2 seconds, we want to understand:

    Where are the 2 seconds being spent?

Maybe:

    API processing       → 100 ms
    Database query       → 1500 ms
    External API         → 300 ms
    Network              → 100 ms

The database is the main bottleneck.

The goal of performance optimization is not to make everything faster blindly.

The goal is:

> Find the bottleneck and optimize the bottleneck.

---

# 2. What is Scaling?

Scaling means increasing a system's capacity so it can handle more:

- Users
- Requests
- Data
- Connections
- Background jobs
- Traffic

Example:

    1,000 requests/minute
           ↓
    System handles it

Later:

    100,000 requests/minute
           ↓
    Existing architecture struggles

We need to scale the system.

---

# 3. Vertical Scaling

Vertical scaling means increasing the resources of a single machine.

Example:

    4 CPU
    8 GB RAM

becomes:

    16 CPU
    64 GB RAM

### Advantages

- Simple
- Usually easy to implement
- No major architecture changes initially

### Disadvantages

- Hardware has limits
- Can become expensive
- Single-machine failure can still affect the system
- Eventually you hit a ceiling

---

# 4. Horizontal Scaling

Horizontal scaling means adding more machines/instances.

Example:

    Before:

    Client
      ↓
    Server


    After:

                ┌── Server 1
    Client → LB ├── Server 2
                └── Server 3

Load Balancer = Load Balancer

The load balancer distributes traffic across multiple servers.

### Advantages

- Handles more traffic
- Better fault tolerance
- Can scale gradually
- Works well with cloud infrastructure

### Challenges

- Distributed systems become more complex
- Shared state needs special handling
- Session management becomes important
- Distributed caching/queues may be required

---

# 5. Vertical vs Horizontal Scaling

| Feature | Vertical | Horizontal |
|---|---|---|
| Method | Bigger machine | More machines |
| Complexity | Lower | Higher |
| Scalability | Limited by machine | Usually much higher |
| Fault tolerance | Lower | Higher |
| Distributed system | Not necessarily | Usually |
| Example | 4 CPU → 16 CPU | 1 server → 5 servers |

### Interview answer

Vertical scaling increases the resources of an existing machine, while horizontal scaling adds more machines to distribute the workload.

---

# 6. Stateless Backend

Horizontal scaling works best when backend servers are stateless.

Stateless means:

> The server does not depend on local memory to maintain important user/session state.

Example:

    Server 1
    Server 2
    Server 3

A user can send:

    Request 1 → Server 1
    Request 2 → Server 3
    Request 3 → Server 2

and everything still works.

---

# 7. Why Local Memory Becomes a Problem

Suppose:

    Server 1:
    loggedInUsers = {...}

User logs in through Server 1.

Then the next request goes to:

    Server 2

Server 2 does not know the state stored in Server 1.

This causes problems.

### Better approach

Store shared state in:

- Redis
- Database
- Distributed session store

Architecture:

    Server 1 ──┐
    Server 2 ──┼── Redis
    Server 3 ──┘

---

# 8. Load Balancer

A load balancer distributes incoming traffic across backend servers.

Example:

    Client
       ↓
    Load Balancer
       ↓
    ┌──────┬──────┬──────┐
    ↓      ↓      ↓
    API 1  API 2  API 3

Common load balancing algorithms:

- Round robin
- Weighted round robin
- Least connections
- IP hash
- Consistent hashing

---

# 9. Round Robin

Requests are distributed sequentially.

    Request 1 → Server 1
    Request 2 → Server 2
    Request 3 → Server 3
    Request 4 → Server 1
    Request 5 → Server 2

Simple and useful when servers have similar capacity.

---

# 10. Least Connections

Send the request to the server with fewer active connections.

Example:

    Server 1 → 100 connections
    Server 2 → 40 connections
    Server 3 → 70 connections

New request:

    → Server 2

This can be useful when request processing times vary.

---

# 11. Health Checks

A load balancer should avoid sending traffic to unhealthy servers.

Example:

    GET /health

Response:

    200 OK

If Server 2 becomes unhealthy:

    Client
       ↓
    Load Balancer
       ↓
    ┌──────┬──────┐
    ↓      ↓
    S1     S3

Server 2 is temporarily removed from traffic.

---

# 12. Performance vs Scalability

These are related but different.

### Performance

How quickly and efficiently one system handles work.

Example:

    API response = 100 ms

### Scalability

How well the system handles increasing load.

Example:

    1,000 users
        ↓
    10,000 users
        ↓
    1,000,000 users

A system can be fast but not scalable.

A system can also scale horizontally but still have slow requests.

---

# 13. Latency

Latency is the time taken to complete an operation.

Example:

    Client
      ↓
    API
      ↓
    Database
      ↓
    Response

If total time = 200 ms:

    Latency = 200 ms

Lower latency generally means faster responses.

---

# 14. Throughput

Throughput is the amount of work a system can process in a given time.

For APIs, it is often expressed as:

    Requests per second (RPS)

Example:

    Server handles 500 requests/second

Throughput is:

    500 RPS

For other systems it could be:

    jobs/second
    messages/second
    transactions/second

---

# 15. Concurrency

Concurrency means handling multiple operations that are in progress at the same time.

Example:

    Request A ──────────────
    Request B ───────
    Request C ───────────
    Request D ───────────────

A backend may handle many requests concurrently.

Node.js is particularly effective for I/O-heavy workloads because its event-driven architecture allows it to handle many concurrent I/O operations without creating one OS thread per request.

---

# 16. CPU-Bound vs I/O-Bound

This distinction is very important.

## I/O-bound

The application spends most of its time waiting for:

- Database
- Network
- File system
- External APIs

Example:

    API → MongoDB
          ↓
        waiting

Node.js handles this type of workload well.

## CPU-bound

The application spends most of its time doing computation.

Examples:

- Large image processing
- Video encoding
- Heavy mathematical calculations
- Complex data processing

For CPU-heavy work, consider:

- Worker threads
- Background jobs
- Separate services
- Horizontal scaling

---

# 17. Find the Bottleneck First

Do not optimize randomly.

Typical bottlenecks:

    CPU
    Memory
    Database
    Network
    External API
    Disk
    Locks
    Connection pool
    Queue
    Cache misses

Use monitoring and profiling to identify the actual bottleneck.

---

# 18. Database Performance

Databases are one of the most common backend bottlenecks.

Example:

    API
      ↓
    Database
      ↓
    2-second query

Even if your Node.js code takes only 20 ms, the API is still slow.

---

# 19. Database Indexing

Indexes allow databases to find data efficiently.

Suppose you frequently query:

    find user by email

Without an index:

    Database scans many records

With an index:

    Database can locate the matching record efficiently

Example:

    users
      email: "user@example.com"

Create an index on:

    email

### Important

Indexes improve reads but also:

- Consume storage
- Increase write/update overhead
- Require maintenance

Do not create indexes blindly.

---

# 20. Query Optimization

Bad query:

    SELECT *
    FROM users;

if you only need:

    id
    name

Better:

    SELECT id, name
    FROM users;

Avoid fetching unnecessary data.

Also:

- Use proper indexes
- Avoid unnecessary joins
- Analyze query plans
- Paginate large results
- Avoid N+1 queries

---

# 21. N+1 Query Problem

Suppose you fetch 100 users:

    Query 1:
    Get 100 users

Then for every user:

    Query 2
    Query 3
    ...
    Query 101

Total:

    101 queries

This is the N+1 problem.

Possible solutions:

- Joins
- Eager loading
- Batch queries
- Data loaders
- Aggregation pipelines
- Better query design

---

# 22. Connection Pooling

Creating a new database connection for every request is expensive.

Instead, use a connection pool.

Example:

    Backend
       ↓
    Connection Pool
       ↓
    ┌────┬────┬────┬────┐
    DB connections

Requests reuse connections.

Benefits:

- Lower connection overhead
- Better performance
- Controlled database connections

---

# 23. Caching

Caching stores frequently used data closer to the application.

Example:

    Without cache:

    API → Database → Response

    With cache:

    API → Redis → Response

If data is available in Redis:

    Database is not queried.

---

# 24. Cache-Aside Pattern

Common caching strategy:

    Request
      ↓
    Check Cache
      ↓
    ┌───────────────┐
    │ Cache exists? │
    └───────────────┘
       ↓ Yes     ↓ No
    Return      Database
                  ↓
                Cache
                  ↓
                Return

Example:

    GET /products/123

First request:

    Redis miss
       ↓
    MongoDB
       ↓
    Store in Redis
       ↓
    Return

Next request:

    Redis hit
       ↓
    Return immediately

---

# 25. Cache Invalidation

One of the hardest caching problems is keeping cached data correct.

Example:

    Database:
    price = 100

    Redis:
    price = 100

Product price changes:

    Database:
    price = 120

If Redis still contains:

    price = 100

the API returns stale data.

Common strategies:

- TTL
- Delete cache after update
- Update cache
- Event-driven invalidation

---

# 26. TTL

TTL = Time To Live.

Example:

    Cache entry
    TTL = 5 minutes

After 5 minutes:

    Cache expires

TTL reduces the amount of stale data.

---

# 27. CDN

CDN = Content Delivery Network.

A CDN caches content geographically closer to users.

Useful for:

- Images
- CSS
- JavaScript
- Videos
- Static assets
- Some cacheable API responses

Architecture:

    User
      ↓
    CDN
      ↓
    Backend

If the CDN already has the requested resource:

    User → CDN → Response

Backend is not contacted.

---

# 28. Compression

Compress responses to reduce network transfer size.

Common compression methods:

- gzip
- Brotli

Example:

    Before:
    1 MB response

    After compression:
    200 KB

This can reduce bandwidth and improve transfer time.

Do not blindly compress everything; already-compressed formats such as JPEG, PNG, and many video formats may gain little.

---

# 29. Pagination

Never return millions of records in a single API response.

Bad:

    GET /users

returning:

    10,000,000 users

Use pagination.

### Offset pagination

    GET /users?page=2&limit=20

### Cursor pagination

    GET /users?cursor=abc123&limit=20

Cursor pagination is often more suitable for large or frequently changing datasets.

---

# 30. Lazy Loading

Load data only when it is actually needed.

Example:

Instead of loading:

    1000 products

initially load:

    20 products

Then load more when required.

Useful for:

- APIs
- Images
- Large datasets
- UI resources

---

# 31. API Response Optimization

Avoid returning unnecessary data.

Bad:

    {
      user: {
        ...huge object...
      }
    }

If client only needs:

    {
      id,
      name
    }

return only those fields.

Benefits:

- Less database work
- Less serialization
- Smaller network response
- Faster client processing

---

# 32. Serialization Performance

Serialization converts application data into a transferable format.

Example:

    JavaScript object
          ↓
        JSON
          ↓
       Network

Large JSON objects increase:

- CPU usage
- Memory usage
- Network transfer
- Parsing time

Return only required fields.

---

# 33. Asynchronous Processing

Do not make the user wait for work that does not need to happen immediately.

Example:

User signs up.

Bad:

    Signup
      ↓
    Create account
      ↓
    Generate report
      ↓
    Send email
      ↓
    Process analytics
      ↓
    Response

Better:

    Signup
      ↓
    Create account
      ↓
    Queue background jobs
      ↓
    Return response

Workers handle:

    Email
    Analytics
    Report generation

---

# 34. Task Queues for Scaling

Example:

    API Servers
        ↓
      Queue
        ↓
    ┌───────┬───────┬───────┐
    Worker  Worker  Worker
      ↓       ↓       ↓
    Jobs    Jobs    Jobs

You can add workers when job volume increases.

Useful for:

- Emails
- Image processing
- Reports
- Notifications
- Data imports
- Video processing

---

# 35. Rate Limiting and Performance

Rate limiting is not only a security mechanism.

It also protects system resources.

Example:

    Without rate limit:

    1 client
       ↓
    100,000 requests
       ↓
    Backend overloaded

With rate limit:

    1 client
       ↓
    100 requests/minute
       ↓
    Remaining requests rejected/throttled

This protects CPU, database, and network resources.

---

# 36. Backpressure

Backpressure occurs when producers generate work faster than consumers can process it.

Example:

    Producer
      ↓
    10,000 jobs/sec
      ↓
    Worker
      ↓
    1,000 jobs/sec

Queue grows rapidly.

Solutions:

- Rate limiting
- More workers
- Batch processing
- Queue limits
- Load shedding
- Backpressure mechanisms

---

# 37. Load Shedding

When the system is overloaded, it may intentionally reject or degrade low-priority work.

Example:

    System overloaded

    Keep:
    Payment API
    Order creation

    Temporarily degrade:
    Recommendations
    Analytics
    Non-critical reports

This protects critical functionality.

---

# 38. Timeouts

Never allow external calls to wait forever.

Bad:

    API → External Service
              ↓
           hangs forever

Better:

    API → External Service
              ↓
          3-second timeout
              ↓
           Failure

Use timeouts for:

- HTTP requests
- Database operations where supported
- Queue jobs
- External services

---

# 39. Retries

Temporary failures can sometimes be retried.

Example:

    API → Payment Service
              ↓
           Timeout
              ↓
           Retry

But retries must be controlled.

Use:

- Limited retry count
- Exponential backoff
- Jitter
- Idempotency where necessary

Otherwise retries can create a retry storm.

---

# 40. Exponential Backoff

Instead of retrying immediately:

    Retry 1 → 100 ms
    Retry 2 → 200 ms
    Retry 3 → 400 ms
    Retry 4 → 800 ms

Add jitter to avoid many clients retrying simultaneously.

---

# 41. Circuit Breaker

Circuit breaker protects your system when a dependency is failing repeatedly.

Example:

    API
      ↓
    Payment Service
      ↓
    failing

Instead of continuously sending requests:

    Circuit opens
      ↓
    Requests fail fast
      ↓
    Dependency gets time to recover

States commonly include:

    Closed
      ↓
    Open
      ↓
    Half-Open
      ↓
    Closed

---

# 42. Bulkhead Pattern

Bulkhead isolation prevents one failing workload from consuming all resources.

Example:

    Application
       ├── Payment resources
       ├── Order resources
       └── Reporting resources

If reporting becomes overloaded:

    Reporting fails

but:

    Payment
    Order

can continue working.

---

# 43. Connection Pool Limits

Too many database connections can overload the database.

Example:

    100 backend instances
          ↓
    Each creates 100 DB connections
          ↓
    10,000 DB connections

The database may become overwhelmed.

Scaling application servers therefore requires thinking about database connection limits too.

---

# 44. Read Replicas

If reads greatly exceed writes, database read replicas can distribute read traffic.

Example:

              Primary
             /       \
            ↓         ↓
        Replica 1  Replica 2

Writes:

    → Primary

Reads:

    → Replicas

Potential issue:

> Replicas may have replication lag.

So some operations requiring the newest data may need to read from the primary.

---

# 45. Database Sharding

Sharding distributes data across multiple database servers.

Example:

    Users 1–1M
       ↓
    Shard 1

    Users 1M–2M
       ↓
    Shard 2

    Users 2M–3M
       ↓
    Shard 3

This can support very large datasets and traffic.

But sharding adds significant complexity.

---

# 46. Replication vs Sharding

### Replication

Copies the same data to multiple servers.

Main goals:

- Read scaling
- Availability
- Failover

### Sharding

Splits data across multiple servers.

Main goal:

- Distribute very large datasets/workloads

Simple mental model:

    Replication:
    Same data → multiple servers

    Sharding:
    Different data → different servers

---

# 47. Database Denormalization

Sometimes read performance can be improved by storing duplicated/derived data.

Example:

Instead of repeatedly joining:

    users
    orders
    products

you might store frequently required information in a read-optimized structure.

Trade-off:

    Faster reads
       vs
    More storage
       +
    More complex updates

Use denormalization based on actual workload requirements.

---

# 48. Search Systems

For advanced search, databases may not be the best tool.

Example:

    Product search
    Full-text search
    Autocomplete
    Relevance ranking

Use a search engine such as Elasticsearch when appropriate.

Architecture:

    Database
       ↓
    Source of truth

    Elasticsearch
       ↓
    Search projection

This can improve search performance without putting all search workload on the primary database.

---

# 49. Object Storage

Do not store large files directly inside your application server's memory or local disk when unnecessary.

Use object storage for:

- Images
- Videos
- PDFs
- Backups
- Large files

Example:

    Client
      ↓
    Backend
      ↓
    Object Storage

Or, for suitable systems:

    Client
      ↓
    Direct upload
      ↓
    Object Storage

The backend handles authorization and upload policy rather than transferring every large file itself.

---

# 50. Stateless Application Servers

A scalable backend generally avoids storing important state only in local memory.

Instead:

    Application servers
       ↓
    Shared Redis
    Shared Database
    Shared Object Storage
    Shared Queue

This allows instances to be added or removed easily.

---

# 51. Auto Scaling

Cloud infrastructure can automatically add/remove instances based on load.

Example:

    Low traffic
       ↓
    2 servers

    High traffic
       ↓
    8 servers

    Traffic decreases
       ↓
    3 servers

Scaling signals may include:

- CPU utilization
- Memory
- Request count
- Queue length
- Latency

CPU alone is not always the best scaling signal.

---

# 52. Caching Layers

Large systems may use multiple caching layers:

    Browser Cache
          ↓
    CDN
          ↓
    Application Cache / Redis
          ↓
    Database

Each layer can reduce load on the next layer.

---

# 53. Performance Profiling

Profiling helps identify where time or resources are being spent.

For a Node.js backend, investigate:

- CPU usage
- Memory usage
- Event loop delay
- Slow functions
- Database queries
- External API calls
- Garbage collection
- Network operations

Do not optimize based only on assumptions.

---

# 54. Node.js Event Loop Performance

Node.js uses an event-driven architecture.

A CPU-heavy synchronous operation can block the event loop.

Example concept:

    Request A
       ↓
    Heavy CPU computation
       ↓
    Event loop blocked
       ↓
    Request B waits
    Request C waits
    Request D waits

Avoid expensive synchronous operations in the request path.

For CPU-heavy work, consider:

- Worker threads
- Background workers
- Separate services

---

# 55. Memory Leaks

A memory leak occurs when memory that is no longer needed remains referenced and cannot be reclaimed.

Symptoms:

    Memory usage
       ↑
       ↑
       ↑
    Application crashes

Possible causes:

- Global objects growing indefinitely
- Unremoved event listeners
- Unbounded caches
- Long-lived references
- Improper resource cleanup

Monitor memory usage and use profiling tools to investigate.

---

# 56. Performance Monitoring

Important metrics:

### Latency

    p50
    p95
    p99

### Throughput

    Requests/sec

### Errors

    Error rate

### Resources

    CPU
    Memory
    Network

### Database

    Query latency
    Connection usage

### Queue

    Queue depth
    Processing latency

---

# 57. p50, p95, p99

Suppose 1000 requests are processed.

### p50

50% of requests are faster than this value.

### p95

95% of requests are faster than this value.

### p99

99% of requests are faster than this value.

Example:

    p50 = 80 ms
    p95 = 300 ms
    p99 = 1200 ms

This tells us that some users are experiencing significantly slower requests.

---

# 58. Throughput vs Latency

Suppose:

    System A:
    100 ms latency
    100 RPS

    System B:
    150 ms latency
    1,000 RPS

Neither metric alone tells the entire story.

You need to understand:

- Latency
- Throughput
- Error rate
- Resource usage
- User requirements

---

# 59. Performance Budget

Define acceptable limits.

Example:

    API p95 latency < 300 ms
    Error rate < 1%
    CPU < 70%
    Database query < 100 ms

These targets help teams detect performance regressions.

---

# 60. Caching Trade-offs

Caching improves performance but introduces:

- Stale data
- Cache invalidation complexity
- Memory usage
- Cache stampede risk
- Operational complexity

Never ask only:

> Can we cache this?

Also ask:

> How stale can this data safely be?

---

# 61. Cache Stampede

Suppose a popular cache entry expires.

Thousands of requests arrive simultaneously:

    Cache miss
       ↓
    10,000 requests
       ↓
    10,000 database queries

Database becomes overloaded.

Possible solutions:

- Locking
- Request coalescing
- Stale-while-revalidate
- Early refresh
- Jittered TTLs

---

# 62. Database Bottleneck Example

Suppose:

    10 API servers
       ↓
    Database

Traffic increases:

    10,000 RPS

Database can only handle:

    3,000 RPS

Adding more API servers will not solve the problem.

This is a critical system-design principle:

> Scaling one layer does not help when another layer is the bottleneck.

Possible solutions:

- Query optimization
- Indexes
- Caching
- Read replicas
- Connection pool tuning
- Database scaling
- Sharding where justified

---

# 63. Complete Scalable Backend Architecture

A common high-level architecture:

    Users
       ↓
    CDN / WAF
       ↓
    Load Balancer
       ↓
    ┌────────┬────────┬────────┐
    │ API 1  │ API 2  │ API 3  │
    └────────┴────────┴────────┘
       ↓
    ┌───────────────┐
    │ Redis Cache   │
    └───────────────┘
       ↓
    Database
       ↓
    Read Replicas

    API
       ↓
    Queue
       ↓
    ┌─────────┬─────────┐
    │ Worker  │ Worker  │
    └─────────┴─────────┘

    Large Files
       ↓
    Object Storage

    Search
       ↓
    Elasticsearch

    Across everything:
    Logging + Monitoring + Tracing

---

# 64. Scaling Strategy

When traffic increases, don't immediately add servers.

Follow a process:

    1. Measure
          ↓
    2. Identify bottleneck
          ↓
    3. Optimize
          ↓
    4. Cache where appropriate
          ↓
    5. Scale the bottleneck
          ↓
    6. Monitor again

Example:

    Slow API
       ↓
    Profiling
       ↓
    Slow DB query discovered
       ↓
    Add index
       ↓
    Query improves
       ↓
    Monitor

---

# 65. Scaling an E-Commerce Application

Suppose an e-commerce application has:

    10,000 users
       ↓
    100,000 users
       ↓
    1,000,000 users

Potential architecture:

    Users
      ↓
    CDN
      ↓
    Load Balancer
      ↓
    Multiple API servers
      ↓
    Redis
      ↓
    Database
      ↓
    Read replicas

For asynchronous work:

    API
      ↓
    Queue
      ↓
    Workers
      ↓
    Email / Notifications / Reports

For search:

    Product DB
       ↓
    Elasticsearch

For files:

    Product images
       ↓
    Object Storage + CDN

---

# 66. Performance Optimization Checklist

## API

- Avoid unnecessary processing
- Return only required fields
- Use pagination
- Set timeouts
- Compress appropriate responses
- Cache suitable data

## Database

- Use indexes
- Optimize queries
- Avoid N+1 queries
- Use connection pooling
- Monitor slow queries
- Use replicas where appropriate

## Application

- Avoid blocking the event loop
- Avoid unnecessary computations
- Use asynchronous processing
- Prevent memory leaks
- Profile before optimizing

## Infrastructure

- Load balancing
- Horizontal scaling
- Auto scaling
- CDN
- WAF
- Health checks

## Distributed systems

- Queues
- Caching
- Retries
- Timeouts
- Circuit breakers
- Backpressure

---

# 67. Common Scaling Mistakes

### Mistake 1

Adding servers without identifying the bottleneck.

### Mistake 2

Using unlimited database connections.

### Mistake 3

Caching everything.

### Mistake 4

Ignoring cache invalidation.

### Mistake 5

Returning huge API responses.

### Mistake 6

Running CPU-heavy work inside request handlers.

### Mistake 7

Not using pagination.

### Mistake 8

Retrying requests indefinitely.

### Mistake 9

Ignoring database indexes.

### Mistake 10

Keeping important state only in local server memory.

### Mistake 11

Ignoring external service latency.

### Mistake 12

Scaling the application layer while the database remains the bottleneck.

---

# 68. Interview Questions

## Q1. What is horizontal scaling?

Adding more servers/instances and distributing traffic between them.

---

## Q2. What is vertical scaling?

Increasing the CPU, memory, storage, or other resources of an existing machine.

---

## Q3. How do you scale a Node.js application?

I would first identify the bottleneck using metrics and profiling. Then I would optimize the application and database, use caching and asynchronous processing where appropriate, and horizontally scale stateless application instances behind a load balancer.

---

## Q4. Why should backend servers be stateless?

Because stateless servers can handle requests independently, making horizontal scaling and load balancing easier.

---

## Q5. How would you handle 1 million users?

I would not simply add servers. I would design for:

- Load balancing
- Stateless application servers
- Caching
- Database optimization
- Read replicas where appropriate
- Queues for background work
- CDN for static content
- Object storage for large files
- Search infrastructure if needed
- Monitoring and autoscaling

The exact architecture depends on traffic patterns and workload.

---

## Q6. How do you find a performance bottleneck?

Use:

- Metrics
- Logs
- Distributed traces
- Database query analysis
- Profiling
- CPU/memory monitoring
- Load testing

Then optimize the actual bottleneck.

---

## Q7. What is the difference between latency and throughput?

Latency is the time required for an individual operation.

Throughput is how much work the system processes over a period of time.

---

## Q8. How does Redis improve performance?

Redis can serve frequently accessed data from memory, reducing database queries and lowering latency.

---

## Q9. What happens if Redis goes down?

The system should have a defined fallback strategy.

For cache-aside caching:

    Redis unavailable
        ↓
    Read from database
        ↓
    Continue serving requests

However, this can suddenly increase database load, so production systems should consider failure behavior carefully.

---

## Q10. How do you scale a database?

Depending on the workload:

- Optimize queries
- Add indexes
- Cache reads
- Use read replicas
- Increase database resources
- Partition data
- Shard when necessary

---

## Q11. What is a cache stampede?

When a popular cache entry expires and many requests simultaneously query the underlying database.

---

## Q12. Why are queues useful for scaling?

Queues decouple request processing from background work and allow workers to process jobs independently.

They also help absorb traffic spikes.

---

## Q13. What is backpressure?

Backpressure occurs when incoming work is produced faster than the system can process it.

---

## Q14. What is a circuit breaker?

A circuit breaker temporarily stops calls to a repeatedly failing dependency so the failure does not cascade through the system.

---

## Q15. What is a read replica?

A read replica is a copy of a database used primarily to handle read traffic, allowing the primary database to focus on writes and other operations.

---

# 69. System Design Mental Model

When designing a scalable backend, think in this order:

    Traffic
       ↓
    Load Balancer
       ↓
    Stateless API Servers
       ↓
    Cache
       ↓
    Database
       ↓
    Read Replicas / Sharding if needed

Then separate slow/background operations:

    API
       ↓
    Queue
       ↓
    Workers

And separate specialized workloads:

    Search → Elasticsearch
    Files → Object Storage
    Static assets → CDN

Finally add:

    Monitoring
    Logging
    Tracing
    Alerts

---

# 70. Quick Revision

    Performance
    → How efficiently/quickly does the system work?

    Scalability
    → How well does it handle increasing load?

    Vertical Scaling
    → Bigger machine

    Horizontal Scaling
    → More machines

    Load Balancer
    → Distributes traffic

    Stateless
    → No critical state stored only on one server

    Cache
    → Faster access to frequently used data

    CDN
    → Serve content closer to users

    Index
    → Faster database lookup

    Connection Pool
    → Reuse database connections

    Read Replica
    → Scale database reads

    Sharding
    → Split data across database nodes

    Queue
    → Move background work away from request path

    Backpressure
    → Producer is faster than consumer

    Timeout
    → Don't wait forever

    Retry
    → Recover from temporary failures

    Circuit Breaker
    → Stop repeatedly calling failing dependencies

    p95/p99
    → Understand tail latency

    Profiling
    → Find expensive code

    Bottleneck
    → The component limiting overall performance

---

# 71. One-Line Interview Summary

> Backend scaling is the process of increasing system capacity through techniques such as horizontal scaling, load balancing, caching, database optimization, read replicas, queues, CDNs, and distributed architecture, while performance focuses on reducing latency, improving throughput, and efficiently using system resources.