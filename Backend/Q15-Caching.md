# Caching in Backend

## 1. What is Caching?

Caching means temporarily storing frequently used data in a faster storage layer so that we can retrieve it quickly instead of performing the expensive operation again.

Simple idea:

    Without cache:
    Client → Server → Database → Response

    With cache:
    Client → Server → Cache → Response

If data exists in the cache, we don't need to query the database.

### Simple real-world example

Suppose 10,000 users request:

    GET /products

Without caching:

    10,000 requests → 10,000 database queries

With caching:

    First request → Database → Store result in cache
    Next 9,999 requests → Cache

This reduces database load and improves response time.

---

# 2. Why Do We Need Caching?

Caching is mainly used for:

- Faster response time
- Reducing database load
- Reducing expensive computations
- Handling high traffic
- Improving scalability
- Reducing calls to external APIs
- Improving user experience

Example:

    Database query = 200ms
    Redis lookup = 5ms

If the same data is requested frequently, caching can significantly improve performance.

---

# 3. Where Can We Cache Data?

Caching can happen at multiple levels.

    Client
       ↓
    CDN
       ↓
    Load Balancer / Reverse Proxy
       ↓
    Application
       ↓
    Redis / Cache Server
       ↓
    Database

Common caching layers:

1. Browser cache
2. CDN cache
3. Reverse proxy cache
4. Application memory cache
5. Distributed cache such as Redis
6. Database cache

---

# 4. In-Memory Cache

The simplest cache is memory inside the application.

Example:

    const cache = new Map();

    cache.set("products", products);

    const products = cache.get("products");

The data is stored in the application's RAM.

### Advantages

- Very fast
- Easy to implement
- No external service required

### Problems

Suppose we have 3 backend servers:

    Server 1 → cache contains products
    Server 2 → cache does not contain products
    Server 3 → cache does not contain products

Each server has its own memory.

Also, when the server restarts:

    Server restart
         ↓
    Memory cleared
         ↓
    Cache lost

Therefore, in-memory caching is usually not suitable for large distributed applications.

---

# 5. Redis

Redis is one of the most commonly used technologies for backend caching.

Redis stores data primarily in memory, making reads and writes very fast.

Architecture:

    Client
       ↓
    Node.js API
       ↓
    Redis
       ↓
    Database

Redis can also be used for:

- Caching
- Sessions
- Rate limiting
- Distributed locks
- Queues
- Pub/Sub
- Temporary data

---

# 6. Basic Cache Flow

Suppose we have:

    GET /products/123

### Step 1: Check cache

    GET product:123

### Step 2: If cache exists

Return cached data.

    Client
       ↓
    API
       ↓
    Redis
       ↓
    Response

No database query is required.

### Step 3: If cache does not exist

Query the database.

    Client
       ↓
    API
       ↓
    Redis → MISS
       ↓
    Database
       ↓
    Redis ← Store result
       ↓
    Response

This is called the Cache-Aside pattern.

---

# 7. Cache Hit

A cache hit means the requested data exists in the cache.

Example:

    User requests product 123

    Redis:
    product:123 → FOUND

    Result:
    Cache HIT

Flow:

    Client
       ↓
    API
       ↓
    Redis
       ↓
    Data returned

This is fast because the database is not accessed.

---

# 8. Cache Miss

A cache miss means the requested data is not present in the cache.

Example:

    User requests product 123

    Redis:
    product:123 → NOT FOUND

    Result:
    Cache MISS

Then:

    Redis MISS
        ↓
    Database query
        ↓
    Store result in Redis
        ↓
    Return response

---

# 9. Cache-Aside Pattern

Cache-Aside is one of the most common caching strategies.

Application controls when data is loaded into the cache.

Flow:

    Request
       ↓
    Check cache
       ↓
    ┌───────────────┐
    │ Cache exists? │
    └───────────────┘
       ↓          ↓
      YES         NO
       ↓          ↓
    Return     Database
    data          ↓
                  ↓
             Store in cache
                  ↓
             Return data

Example:

    async function getProduct(id) {
        const cachedProduct = await redis.get(`product:${id}`);

        if (cachedProduct) {
            return JSON.parse(cachedProduct);
        }

        const product = await Product.findById(id);

        await redis.set(
            `product:${id}`,
            JSON.stringify(product),
            { EX: 300 }
        );

        return product;
    }

Here:

    EX: 300

means the cache expires after 300 seconds.

---

# 10. TTL

TTL means:

    Time To Live

TTL determines how long cached data should remain valid.

Example:

    product:123
    TTL = 300 seconds

After 300 seconds:

    Cache expires
         ↓
    Data removed/invalidated
         ↓
    Next request queries database

Common TTL examples:

    User profile → 5 minutes
    Product list → 5 minutes
    Weather → 10 minutes
    Exchange rates → 1 minute
    Static configuration → 1 hour

The correct TTL depends on how frequently the data changes.

---

# 11. Why TTL Is Important

Imagine caching product information forever.

Database:

    price = ₹50,000

Cache:

    price = ₹45,000

If the product price changes in the database but the cache still contains the old value, users receive stale data.

TTL limits how long stale data can remain.

---

# 12. Cache Invalidation

Cache invalidation means removing or updating cached data when the original data changes.

One famous engineering problem is:

    "There are only two hard things in Computer Science:
     cache invalidation and naming things."

Example:

Current data:

    Database:
    product.price = ₹50,000

    Cache:
    product:123 → ₹50,000

User updates price:

    Database:
    product.price = ₹55,000

Now cache still contains:

    product:123 → ₹50,000

We need to invalidate or update it.

Example:

    await Product.findByIdAndUpdate(id, {
        price: 55000
    });

    await redis.del(`product:${id}`);

Now the next GET request will fetch the latest data from the database.

---

# 13. Write-Through Cache

In Write-Through caching, data is written to the cache and database together.

Flow:

    Application
       ↓
    Cache
       ↓
    Database

When data changes:

    Update cache
    Update database

Advantage:

- Cache stays relatively fresh

Disadvantage:

- Every write may involve cache + database
- More write overhead

---

# 14. Write-Behind / Write-Back Cache

In Write-Back caching:

    Application
       ↓
    Cache

The application first writes to the cache.

The cache later writes data to the database asynchronously.

Flow:

    Application
       ↓
    Cache
       ↓
    Later
       ↓
    Database

Advantage:

- Very fast writes

Disadvantage:

- More complexity
- Data can potentially be lost if the cache fails before persistence
- Requires reliable infrastructure

---

# 15. Read-Through Cache

In Read-Through caching, the application asks the cache for data and the cache is responsible for loading missing data from the database.

Conceptually:

    Application
       ↓
    Cache
       ↓
    Database

If cache misses:

    Cache → Database → Cache → Application

This differs from Cache-Aside because the application itself doesn't directly handle the cache miss logic.

---

# 16. Cache-Aside vs Read-Through

### Cache-Aside

Application handles cache miss.

    Application → Cache

    Cache MISS

    Application → Database

### Read-Through

Cache handles fetching missing data.

    Application → Cache

    Cache MISS

    Cache → Database

Cache-Aside is very common in backend applications because it is simple and flexible.

---

# 17. Cache Eviction

A cache has limited memory.

Eventually we need to remove old entries.

This is called eviction.

Common strategies:

### LRU

    Least Recently Used

Remove the data that hasn't been used recently.

Example:

    A → recently used
    B → recently used
    C → not used for long time

C may be removed first.

### LFU

    Least Frequently Used

Remove data that is accessed least frequently.

### FIFO

    First In First Out

Remove the oldest cached entry first.

---

# 18. Cache Key

Every cached item needs a key.

Example:

    product:123

Other examples:

    user:101
    orders:user:101
    products:page:1
    products:category:mobile
    session:abc123

Good cache keys should be:

- Predictable
- Unique
- Easy to invalidate
- Consistent

A common naming convention is:

    resource:identifier

Example:

    user:123
    product:456

---

# 19. Caching API Responses

Suppose:

    GET /products

returns:

    [
      { id: 1, name: "iPhone" },
      { id: 2, name: "Samsung" }
    ]

We can cache the response.

Example key:

    products:all

Flow:

    GET /products
          ↓
    Redis GET products:all
          ↓
       HIT?
      /    \
    YES     NO
     ↓       ↓
  Return   Database
             ↓
          Redis SET
             ↓
           Return

---

# 20. Caching With Query Parameters

Suppose:

    GET /products?page=1&limit=10

Cache key can be:

    products:page:1:limit:10

For:

    GET /products?page=2&limit=10

Use:

    products:page:2:limit:10

Different requests require different cache keys.

---

# 21. Caching Search Results

Suppose users frequently search:

    GET /products?search=iphone

Cache key:

    products:search:iphone

If the same search happens repeatedly, we can return the cached result.

But be careful with:

- Different filters
- Sorting
- Pagination
- User-specific results

All information that affects the response should generally be represented in the cache key.

---

# 22. What Should We Cache?

Good candidates:

- Frequently accessed data
- Expensive database queries
- Product catalogs
- Public API responses
- Configuration
- Frequently accessed user data
- Expensive computations
- External API responses

Example:

    Product categories
    Countries list
    Popular products
    Exchange rates
    Public configuration

---

# 23. What Should NOT Be Cached?

Be careful with:

- Highly sensitive information
- Frequently changing data
- User-specific information without proper isolation
- Payment information
- Data where stale values can cause serious problems

For example:

    Bank account balance

may require much stronger freshness guarantees than:

    Product categories

Caching decisions depend on business requirements.

---

# 24. Stale Data

Cached data can become outdated.

Example:

    Database:
    stock = 5

    Cache:
    stock = 10

If users see:

    "10 items available"

when only 5 exist, the application may behave incorrectly.

Therefore, caching always involves a trade-off:

    Performance ↔ Freshness

---

# 25. Cache Invalidation Strategies

Common strategies:

### TTL-based

Let data expire automatically.

    TTL = 5 minutes

### Delete on update

When database changes:

    UPDATE database
    DELETE cache

### Update cache on write

When database changes:

    UPDATE database
    UPDATE cache

### Versioned keys

Example:

    product:v1:123
    product:v2:123

This can help with controlled cache changes.

---

# 26. Cache Stampede

Cache stampede happens when a popular cache entry expires and many requests simultaneously try to regenerate it.

Example:

    10,000 users request product list

Cache expires.

Now:

    10,000 requests
          ↓
    Cache MISS
          ↓
    10,000 database queries

This can overload the database.

---

# 27. Preventing Cache Stampede

Common techniques:

### Locking

Allow only one request to regenerate the cache.

    Request 1 → gets lock → database
    Request 2 → waits
    Request 3 → waits
    Request 4 → waits

After Request 1 updates cache:

    Request 2 → gets cached data
    Request 3 → gets cached data

### Randomized TTL / jitter

Instead of:

    TTL = exactly 300 seconds

use slightly different expiration times.

Example:

    295–315 seconds

This reduces synchronized expiration.

### Background refresh

Refresh popular data before it expires.

---

# 28. Cache Penetration

Cache penetration happens when requests repeatedly ask for data that doesn't exist.

Example:

    GET /users/999999999

Database:

    User doesn't exist

If we don't cache the "not found" result, every request can hit the database.

Solution:

    Cache negative results temporarily.

Example:

    user:999999999 → null

with a short TTL.

---

# 29. Cache Breakdown

Cache breakdown usually refers to a very popular cache entry expiring while many users request it at the same time.

Example:

    product:popular
          ↓
      expires
          ↓
    thousands of requests
          ↓
    database overload

Solutions:

- Distributed locking
- Early refresh
- Background refresh
- Randomized TTL

---

# 30. Distributed Cache

In a distributed application:

    Client
       ↓
    Load Balancer
       ↓
    ┌─────────┬─────────┬─────────┐
    │ Server 1│ Server 2│ Server 3│
    └─────────┴─────────┴─────────┘
              ↓
            Redis
              ↓
           Database

All servers can access the same Redis instance/cluster.

This is why Redis is more suitable than local memory when multiple application instances need shared cache state.

---

# 31. Local Cache vs Distributed Cache

| Feature | Local Memory | Redis |
|---|---|---|
| Location | Application RAM | Separate server/service |
| Speed | Extremely fast | Very fast |
| Shared between servers | No | Yes |
| Survives app restart | No | Depends on Redis persistence/config |
| Distributed systems | Limited | Excellent |
| Setup | Very easy | Requires infrastructure |

---

# 32. HTTP Caching

Caching isn't limited to Redis.

Browsers and CDNs can cache HTTP responses.

Important headers include:

    Cache-Control
    ETag
    Last-Modified
    Expires

Example:

    Cache-Control: public, max-age=3600

This tells a cache that the response can be reused for up to 3600 seconds.

---

# 33. Browser Cache

Suppose a website loads:

    logo.png

The browser can store it locally.

Next time:

    Browser → Local Cache

instead of:

    Browser → Server

This reduces:

- Network requests
- Server load
- Loading time

---

# 34. CDN Caching

A CDN can cache static or cacheable content closer to users.

Example:

    User in India
        ↓
    CDN edge server in India
        ↓
    Cached image

Instead of:

    India → Server in US

CDN caching is especially useful for:

- Images
- JavaScript
- CSS
- Videos
- Static files
- Public cacheable API responses

---

# 35. Redis vs CDN

### Redis

Usually caches backend/application data.

    API → Redis → Database

### CDN

Usually caches content closer to users.

    User → CDN → Origin Server

They solve different problems and can be used together.

---

# 36. Caching and Database

Caching should not replace the database.

Think of it as:

    Database = source of truth
    Cache = fast temporary copy

Example:

    Database
       ↓
    Source of truth

    Redis
       ↓
    Fast temporary representation

If Redis is lost, the application should generally be able to rebuild the cache from the database.

---

# 37. Caching and Consistency

There is an important trade-off:

    Strong consistency
          vs
    Better performance

If we cache data for a long time:

    Performance ↑
    Freshness ↓

If we rarely cache:

    Freshness ↑
    Database load ↑

The correct strategy depends on the business.

---

# 38. User-Specific Cache

Be careful when caching user-specific responses.

Example:

    GET /profile

User A:

    name = Lokendra

User B:

    name = Rahul

If the cache key is:

    profile

there can be a serious bug.

Instead:

    profile:user:101
    profile:user:102

The cache key must identify the user when the response is user-specific.

---

# 39. Cache Security

Never blindly cache sensitive responses.

Potential problems:

- One user's data returned to another user
- Sensitive information stored in cache
- Unauthorized access
- Incorrect cache key design

Always consider:

- Authentication
- Authorization
- User identity
- Data sensitivity
- Cache visibility

---

# 40. Cache With Authentication

Suppose:

    GET /my-orders

This response depends on the authenticated user.

Bad:

    orders

Better:

    orders:user:101

Even better, design the cache key based on all relevant dimensions.

For example:

    orders:user:101:page:1

---

# 41. Cache and Database Updates

A common pattern is:

    Write database
        ↓
    Delete cache

Example:

    async function updateProduct(id, data) {
        const product = await Product.findByIdAndUpdate(id, data, {
            new: true
        });

        await redis.del(`product:${id}`);

        return product;
    }

Next GET:

    Redis MISS
        ↓
    Database
        ↓
    Redis SET
        ↓
    Response

This is simple and often safer than trying to update every related cached representation.

---

# 42. Multiple Related Cache Keys

Suppose product 123 appears in:

    product:123
    products:all
    products:category:mobile
    products:search:iphone

Updating the product may make several cached responses stale.

This is one of the difficult parts of caching.

You need a clear invalidation strategy.

---

# 43. Cache Tags

Some caching systems support tagging/grouping cached entries.

Conceptually:

    product:123
    product:124
    product:125

Tag:

    products

Then invalidating the "products" group can remove related entries.

This is useful when many cache keys depend on the same data.

---

# 44. Cache TTL Selection

Don't randomly choose TTL.

Ask:

1. How frequently does data change?
2. How expensive is the database query?
3. How bad is stale data?
4. How frequently is the data requested?
5. Can we invalidate the cache when data changes?

Example:

    Static country list
    → long TTL

    Product price
    → short TTL or explicit invalidation

    Payment status
    → generally avoid relying on stale cache for correctness

---

# 45. Caching Expensive Computation

Caching isn't only for database queries.

Suppose:

    calculateRecommendation(user)

takes 2 seconds.

If the same result can safely be reused:

    First request
        ↓
    Calculate
        ↓
    Cache result

Next request:

    Cache → result

This can dramatically reduce CPU usage.

---

# 46. Caching External API Responses

Suppose your application calls:

    Weather API

External API:

    Request = slow
    Rate limit = limited
    Cost = potentially expensive

You can cache:

    weather:city:delhi

for a short period.

Flow:

    Client
       ↓
    Your API
       ↓
    Redis
       ↓
    External Weather API

This reduces external API calls.

---

# 47. Cache and Rate Limiting

Redis is also commonly used for rate limiting.

Example:

    User can make 100 requests/minute

Redis can maintain:

    rate:user:101

with counters and expiration.

This is another use of Redis, but it is not exactly the same as application-data caching.

---

# 48. Cache Serialization

Redis commonly stores strings/bytes.

For objects, we often serialize them.

Example:

    const product = {
        id: 123,
        name: "iPhone"
    };

Store:

    JSON.stringify(product)

Read:

    JSON.parse(cachedProduct)

Conceptually:

    JavaScript Object
          ↓
    JSON.stringify()
          ↓
    Redis
          ↓
    JSON.parse()
          ↓
    JavaScript Object

---

# 49. Example Backend Architecture

A typical production architecture might look like:

    Client
       ↓
    Load Balancer
       ↓
    Node.js / Express Servers
       ↓
    ┌──────────────┐
    │    Redis     │
    │    Cache     │
    └──────────────┘
       ↓
    PostgreSQL / MongoDB

Request:

    GET /products/123

Flow:

    1. Request reaches backend.
    2. Backend creates cache key.
    3. Redis is checked.
    4. If HIT → return cached data.
    5. If MISS → query database.
    6. Store result in Redis.
    7. Return response.

---

# 50. Complete Node.js Example

Conceptual Express + Redis example:

    app.get("/products/:id", async (req, res) => {
        const { id } = req.params;

        const cacheKey = `product:${id}`;

        // 1. Check cache
        const cachedProduct = await redis.get(cacheKey);

        if (cachedProduct) {
            return res.json({
                source: "cache",
                data: JSON.parse(cachedProduct)
            });
        }

        // 2. Cache miss → database
        const product = await Product.findById(id);

        if (!product) {
            return res.status(404).json({
                message: "Product not found"
            });
        }

        // 3. Store in cache
        await redis.set(
            cacheKey,
            JSON.stringify(product),
            { EX: 300 }
        );

        // 4. Return response
        return res.json({
            source: "database",
            data: product
        });
    });

Important idea:

    Check cache
        ↓
    HIT → return
        ↓
    MISS
        ↓
    Database
        ↓
    Store cache
        ↓
    Return

---

# 51. Cache Invalidation Example

Suppose product is updated.

    app.put("/products/:id", async (req, res) => {
        const { id } = req.params;

        const product = await Product.findByIdAndUpdate(
            id,
            req.body,
            { new: true }
        );

        await redis.del(`product:${id}`);

        return res.json(product);
    });

Why delete cache?

Because:

    Database = new data
    Cache = old data

Deleting the cache forces the next GET request to fetch fresh data.

---

# 52. Important Caching Problems

When designing caching, think about:

- Cache invalidation
- Stale data
- Cache stampede
- Cache penetration
- Cache breakdown
- Memory limits
- Eviction policies
- Serialization
- Cache key design
- User isolation
- Security
- Distributed systems
- Failure handling

---

# 53. What Happens If Redis Goes Down?

Your application should have a fallback strategy.

Example:

    Request
       ↓
    Redis unavailable
       ↓
    Database

Depending on the application's requirements, the cache should generally be treated as an optimization rather than the only source of truth.

But this depends on what Redis is being used for. If Redis is also being used for sessions, locks, queues, or rate limiting, failure handling becomes more application-specific.

---

# 54. Cache Failure Should Not Always Mean Application Failure

Good architecture:

    Redis available:
        Cache → Fast response

    Redis unavailable:
        Database → Slower response

This gives graceful degradation for applications where Redis is only an optimization.

---

# 55. Cache Monitoring

In production, monitor:

- Cache hit rate
- Cache miss rate
- Redis memory usage
- Evictions
- Latency
- Error rate
- Number of keys
- Connection count

Important metric:

    Cache Hit Rate

Formula:

    Hit Rate =
    Cache Hits / Total Cache Requests × 100

Example:

    900 cache hits
    100 cache misses

    Hit Rate = 90%

A low hit rate may indicate:

- Wrong TTL
- Poor cache keys
- Data not frequently reused
- Cache too small
- Incorrect invalidation strategy

---

# 56. Cache Hit Ratio

Another common term is:

    Cache Hit Ratio

Example:

    95% hit ratio

means most requests are being served from cache.

But a high hit ratio is not automatically good.

You also need to consider:

- Correctness
- Freshness
- Memory usage
- Latency
- Database load

---

# 57. Caching in System Design

In system design interviews, caching is often introduced when:

    Database is becoming a bottleneck
             ↓
    Read traffic is very high
             ↓
    Same data is requested repeatedly
             ↓
    Add cache

Example:

    1 million requests
          ↓
    Redis
          ↓
    Only a smaller number of requests
          ↓
    Database

Caching reduces read pressure on the database.

---

# 58. Typical Production Flow

    User
      ↓
    CDN
      ↓
    Load Balancer
      ↓
    Backend Servers
      ↓
    Redis
      ↓
    Database

For a popular public product:

    User
      ↓
    CDN / Backend
      ↓
    Cache HIT
      ↓
    Response

Database is accessed only when necessary.

---

# 59. When Should You Add Caching?

Don't automatically cache everything.

First identify:

    What is slow?

Then measure:

    Database latency
    API latency
    CPU usage
    Request frequency
    Database load

Then determine:

    Is the same data requested repeatedly?

If yes, caching may help.

Caching should solve a measured performance/scalability problem rather than being added everywhere blindly.

---

# 60. Common Caching Mistakes

### Mistake 1: No invalidation strategy

    Database updated
    Cache still contains old data

### Mistake 2: Bad cache keys

    user profile stored under:
    "profile"

instead of:

    "profile:user:123"

### Mistake 3: Caching sensitive data carelessly

Can cause data leaks.

### Mistake 4: Very long TTL

Can result in stale data.

### Mistake 5: Very short TTL

Cache becomes almost useless.

### Mistake 6: Caching everything

Increases complexity and memory usage.

### Mistake 7: Ignoring cache failures

Application crashes when Redis is unavailable.

### Mistake 8: Ignoring stampede

Popular keys can cause database overload when they expire.

---

# 61. Cache vs Database

| Cache | Database |
|---|---|
| Very fast | Relatively slower |
| Temporary/derived copy | Source of truth |
| Usually memory-based | Persistent storage |
| Reduces database load | Stores application data |
| Can expire | Usually persists |
| May contain stale data | Authoritative data |

Mental model:

    Database = Source of Truth

    Cache = Fast Copy

---

# 62. Cache vs Queue

These are completely different.

### Cache

Purpose:

    Read data faster

Example:

    Redis → product information

### Queue

Purpose:

    Process work asynchronously

Example:

    Redis/RabbitMQ/Kafka
         ↓
    Email worker

So:

    Cache → speed up reads

    Queue → process work asynchronously

---

# 63. Redis Is More Than a Cache

Redis can be used for:

    Caching
    Sessions
    Rate limiting
    Distributed locks
    Pub/Sub
    Queues
    Temporary data

So don't say:

    "Redis is only a cache."

Better:

    "Redis is an in-memory data store commonly used for caching and several other distributed-system use cases."

---

# 64. Interview Scenario

### Question:

Your API receives 100,000 requests for the same product every minute. The database is becoming overloaded. What would you do?

Answer:

    I would introduce a caching layer such as Redis.

    On each request, I would first check Redis using a
    product-specific cache key.

    If the data exists, I would return it directly.

    On a cache miss, I would query the database, store the
    result in Redis with an appropriate TTL, and return it.

    For updates, I would invalidate or update the corresponding
    cache entry to avoid serving stale data.

---

# 65. Interview Scenario: Cache Stampede

### Question:

A popular cache key expires and thousands of requests hit the database simultaneously. How would you solve it?

Answer:

    I would use techniques such as distributed locking,
    request coalescing, background refresh, or randomized TTLs
    so that only a controlled number of requests regenerate the
    cache.

---

# 66. Interview Scenario: User Data Leak

### Question:

You cached /profile but one user received another user's profile. What went wrong?

Answer:

    The cache key was probably not user-specific.

    Instead of using:

    profile

    I would use something like:

    profile:user:123

    so each user's cached response is isolated.

---

# 67. Interview Scenario: Product Update

### Question:

Product price changed in the database but API still returns the old price. Why?

Answer:

    The cache contains stale data.

    The solution is to invalidate or update the relevant
    cache entry whenever the product changes, and also use
    an appropriate TTL as a fallback.

---

# 68. Interview Questions

### Beginner

1. What is caching?
2. Why do we need caching?
3. What is Redis?
4. What is a cache hit?
5. What is a cache miss?
6. What is TTL?
7. What is cache invalidation?
8. What is cache eviction?
9. What is LRU?
10. What is a cache key?

### Intermediate

11. What is Cache-Aside?
12. What is Read-Through caching?
13. What is Write-Through caching?
14. What is Write-Back caching?
15. Local cache vs distributed cache?
16. Redis vs database?
17. How do you cache an API response?
18. How do you invalidate cache after an update?
19. How do you cache user-specific data?
20. What happens when Redis goes down?

### Advanced

21. What is cache stampede?
22. How do you prevent cache stampede?
23. What is cache penetration?
24. What is cache breakdown?
25. How do you maintain cache consistency?
26. How do you design cache keys?
27. How would you cache a high-traffic API?
28. How would you handle stale data?
29. How do you monitor caching?
30. How would you design caching for a distributed system?

---

# 69. Quick Revision

    Caching
    ↓
    Store frequently used data temporarily
    ↓
    Faster response
    ↓
    Lower database load
    ↓
    Redis is commonly used
    ↓
    Check cache first
    ↓
    HIT → return data
    ↓
    MISS → database
    ↓
    Store result in cache
    ↓
    Return response

Important concepts:

    Cache Hit
    Cache Miss
    TTL
    Cache Invalidation
    Cache Eviction
    LRU
    Cache-Aside
    Read-Through
    Write-Through
    Write-Back
    Cache Stampede
    Cache Penetration
    Cache Breakdown
    Distributed Cache
    Cache Consistency
    Cache Key

---

# 70. One-Line Interview Summary

Caching is a technique of storing frequently accessed data in a faster temporary storage layer, such as Redis, to reduce latency, decrease database load, and improve application scalability while carefully managing freshness and cache invalidation.