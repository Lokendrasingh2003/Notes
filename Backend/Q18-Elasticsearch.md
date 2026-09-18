# Elasticsearch

## 1. What Is Elasticsearch?

Elasticsearch is a distributed search and analytics engine used to quickly search, filter, sort, and analyze large amounts of data.

It is built for fast search.

Simple mental model:

    Database
       ↓
    Stores application data

    Elasticsearch
       ↓
    Optimized for searching and analyzing data

Example:

Suppose an e-commerce application has:

    10 million products

A user searches:

    "iphone 15 pro max"

Instead of performing a complex database query every time, we can maintain a searchable index in Elasticsearch.

    User
      ↓
    Search API
      ↓
    Elasticsearch
      ↓
    Search Results

---

# 2. Why Do We Need Elasticsearch?

Traditional databases are excellent for:

    Create
    Read
    Update
    Delete
    Transactions
    Relationships

But search can become more complex when we need:

    Full-text search
    Fuzzy search
    Typo tolerance
    Relevance ranking
    Autocomplete
    Filtering
    Faceted search
    Search analytics
    Log analysis

Elasticsearch is designed specifically for these types of workloads.

---

# 3. Real-World Example

Imagine Amazon-like search.

User searches:

    "wireless headphones"

We may want:

    Headphones
    Bluetooth headphones
    Wireless earbuds
    Noise-cancelling headphones

We also want to consider:

    Product name
    Description
    Brand
    Category
    Price
    Rating

and rank the most relevant products first.

Elasticsearch is very useful for this.

---

# 4. Elasticsearch vs Database

Think of them as serving different purposes.

    PostgreSQL / MongoDB
        ↓
    Source of truth

    Elasticsearch
        ↓
    Search index

Typical architecture:

    Client
       ↓
    Backend API
       ↓
    ┌───────────────┐
    │   Database    │
    │ Source Truth  │
    └───────────────┘
       ↓
    Elasticsearch
       ↓
    Search

The database remains the authoritative source in many architectures.

Elasticsearch contains a searchable representation of the data.

---

# 5. Why Not Use MongoDB/PostgreSQL for Everything?

Databases can perform text searches, but advanced search requirements can become expensive or complex at large scale.

For example:

    Search "iphone"

Then support:

    Typo tolerance
    "iphon"

    Prefix search
    "iph"

    Relevance ranking
    Synonyms
    Highlighting
    Multiple fields
    Aggregations

Elasticsearch provides search-oriented capabilities for these workloads.

This does not mean Elasticsearch is always better than a database.

Use the right tool for the requirement.

---

# 6. Full-Text Search

Full-text search means searching text based on its meaning/terms rather than requiring an exact string match.

Example document:

    {
        "name": "Apple iPhone 15 Pro Max",
        "description": "Latest Apple smartphone with powerful camera"
    }

Search:

    iphone

Can match:

    Apple iPhone 15 Pro Max

Search:

    powerful camera

Can match the description.

---

# 7. Exact Match vs Full-Text Search

### Exact match

    name = "iPhone 15"

Usually expects a precise value.

### Full-text search

    Search:
    "best iphone camera"

The search engine analyzes the text and determines which documents are relevant.

This distinction is very important in Elasticsearch.

---

# 8. Core Elasticsearch Concepts

Important terms:

    Document
    Index
    Field
    Mapping
    Shard
    Replica
    Node
    Cluster
    Query
    Analyzer
    Token
    Inverted Index
    Aggregation

We will understand each one.

---

# 9. Document

A document is a JSON object stored in Elasticsearch.

Example:

    {
        "id": 101,
        "name": "iPhone 15",
        "brand": "Apple",
        "price": 70000,
        "category": "smartphones"
    }

A document is similar to a record in a database.

---

# 10. Index

An index is a collection of related documents.

For example:

    products

can contain:

    Product 1
    Product 2
    Product 3
    Product 4

Conceptually:

    Database
       ↓
    products index
       ├── Document 1
       ├── Document 2
       ├── Document 3
       └── Document 4

An Elasticsearch index is conceptually similar to a table/collection, but its internal architecture and behavior are different.

---

# 11. Field

A field is a property inside a document.

Example:

    {
        "name": "iPhone 15",
        "brand": "Apple",
        "price": 70000
    }

Fields:

    name
    brand
    price

Different fields can have different mappings and search behavior.

---

# 12. Mapping

Mapping defines how Elasticsearch should understand fields.

Example:

    name → text
    brand → keyword
    price → integer
    createdAt → date

Conceptually:

    Field
       ↓
    Data type + indexing behavior

Mapping is important because Elasticsearch needs to know how a field should be indexed and searched.

---

# 13. Text Field

A `text` field is generally used for full-text search.

Example:

    "name": "Apple iPhone 15 Pro Max"

Elasticsearch analyzes the text into searchable terms.

Then a query like:

    "iphone pro"

can search the analyzed content.

---

# 14. Keyword Field

A `keyword` field is generally used for exact values.

Examples:

    brand
    category
    status
    product ID

Example:

    "brand": "Apple"

We might want:

    brand = Apple

rather than full-text analysis.

Keyword fields are also commonly used for:

    Filtering
    Sorting
    Aggregations

---

# 15. Text vs Keyword

| Text | Keyword |
|---|---|
| Full-text search | Exact value |
| Analyzed | Generally not analyzed |
| Search relevance | Filtering |
| Search phrases | Sorting |
| Search descriptions | Aggregations |

Example:

    name → text

    brand → keyword

    price → numeric

---

# 16. Inverted Index

The inverted index is one of the most important concepts in Elasticsearch.

Instead of scanning every document for a word, Elasticsearch creates an index mapping terms to documents.

Example documents:

    Document 1:
    "Apple iPhone"

    Document 2:
    "Samsung Phone"

    Document 3:
    "Apple MacBook"

Conceptually inverted index:

    apple → Document 1, Document 3

    iphone → Document 1

    samsung → Document 2

    phone → Document 2

    macbook → Document 3

Now searching:

    apple

can quickly identify:

    Document 1
    Document 3

This is a major reason search can be fast.

---

# 17. Analyzer

An analyzer processes text before it is indexed/searchable.

It can perform operations such as:

    Character filtering
    Tokenization
    Lowercasing
    Stop-word processing
    Stemming

Example:

    "The Best iPhone"

could be transformed conceptually into:

    the
    best
    iphone

Depending on the analyzer configuration, some terms may be removed or transformed.

---

# 18. Token

A token is an individual searchable term produced during analysis.

Example:

    "Apple iPhone 15"

might produce:

    apple
    iphone
    15

These tokens are then indexed.

---

# 19. Search Query

Elasticsearch provides a Query DSL for searching documents.

Conceptually:

    GET /products/_search

    {
        "query": {
            "match": {
                "name": "iphone"
            }
        }
    }

This asks Elasticsearch to search the `name` field for the term `iphone`.

---

# 20. Match Query

`match` is commonly used for full-text search.

Example:

    {
        "query": {
            "match": {
                "name": "wireless headphones"
            }
        }
    }

Elasticsearch analyzes the search text and finds relevant documents.

---

# 21. Term Query

`term` is generally used for exact, unanalyzed values.

Example:

    {
        "query": {
            "term": {
                "brand": "Apple"
            }
        }
    }

This is useful for keyword fields.

Important:

    match → analyzed full-text search

    term → exact term lookup

---

# 22. Boolean Query

Boolean queries combine multiple conditions.

Example requirement:

    Search for "iphone"
    AND brand = "Apple"
    AND price <= 80000

Conceptually:

    {
        "query": {
            "bool": {
                "must": [
                    {
                        "match": {
                            "name": "iphone"
                        }
                    }
                ],
                "filter": [
                    {
                        "term": {
                            "brand": "Apple"
                        }
                    }
                ]
            }
        }
    }

Boolean queries are extremely important for real applications.

---

# 23. Must

`must` means the condition should match.

Example:

    must:
    name contains iphone

Documents must satisfy the condition.

`must` can contribute to relevance scoring.

---

# 24. Filter

`filter` is used when we only need to filter documents based on a condition.

Example:

    brand = Apple
    price <= 80000
    category = smartphone

Filters are generally not intended to affect relevance scoring.

They are useful for structured conditions.

---

# 25. Must Not

`must_not` excludes documents.

Example:

    Exclude:
    brand = Apple

Conceptually:

    {
        "bool": {
            "must_not": [
                {
                    "term": {
                        "brand": "Apple"
                    }
                }
            ]
        }
    }

---

# 26. Should

`should` can express optional/preferred conditions.

Example:

    Prefer:
    Apple
    Samsung

A document matching more preferred conditions can receive higher relevance depending on the query structure.

---

# 27. Relevance Scoring

Elasticsearch can assign a score to search results.

Example:

    Search:
    "iphone camera"

Result:

    Product A → score 12.4
    Product B → score 8.7
    Product C → score 4.2

Higher score generally means the document is considered more relevant for that query.

Search results are then often returned in relevance order.

---

# 28. Full-Text Search Example

Suppose products contain:

    iPhone 15 Pro
    iPhone 15
    Samsung Galaxy S24
    Google Pixel 9

Search:

    iphone

Elasticsearch may return:

    iPhone 15 Pro
    iPhone 15

because those documents contain the search term.

---

# 29. Fuzzy Search

Fuzzy search helps handle spelling mistakes.

User types:

    iphnoe

But the actual data contains:

    iphone

Fuzzy matching can find:

    iphone

This is useful for user-facing search.

However, fuzzy search should be used carefully because it can increase query cost.

---

# 30. Autocomplete

Suppose the user types:

    iph

We may want suggestions:

    iPhone 15
    iPhone 15 Pro
    iPhone 15 Pro Max

Elasticsearch supports several approaches for autocomplete/search-as-you-type behavior.

Autocomplete can improve the search experience significantly.

---

# 31. Search Suggestions

Another feature is suggestions.

Example:

    User searches:
    "iphon"

System may suggest:

    "iphone"

or product suggestions such as:

    iPhone 15
    iPhone 16

This can be implemented using Elasticsearch's suggestion/search capabilities or application-side logic.

---

# 32. Filtering

Suppose the user searches:

    iphone

and applies:

    Brand = Apple
    Price < ₹80,000
    Rating >= 4

Elasticsearch can combine:

    Full-text search
    +
    Filters

Architecture:

    Search text
        +
    Filters
        +
    Sorting
        ↓
    Elasticsearch
        ↓
    Results

---

# 33. Sorting

Results can be sorted by fields.

Examples:

    Price low → high

    Price high → low

    Newest first

    Rating high → low

Example concept:

    sort:
      price ascending

Sorting generally requires appropriate field mappings.

For exact sorting of text-like values, keyword fields are commonly used.

---

# 34. Pagination

Suppose search returns:

    100,000 products

We don't return all of them.

We return:

    Page 1 → 20 results
    Page 2 → 20 results
    Page 3 → 20 results

Elasticsearch supports different pagination approaches.

For shallow pagination:

    from + size

For deep pagination:

    search_after

For large exports/scans:

    Point-in-time + search_after
    or other appropriate APIs depending on the use case.

---

# 35. Aggregations

Aggregations allow Elasticsearch to perform analytics over data.

Example:

    How many products per brand?

Result:

    Apple → 1200
    Samsung → 900
    Google → 500

Another:

    Average product price

Result:

    ₹62,500

Aggregations are useful for dashboards and faceted search.

---

# 36. Faceted Search

E-commerce filters are a common example.

User searches:

    laptops

Sidebar:

    Brand
      Lenovo
      HP
      Dell

    Price
      < ₹50k
      ₹50k–₹100k
      > ₹100k

    RAM
      8 GB
      16 GB
      32 GB

These filter counts can be generated using aggregations.

---

# 37. Example Aggregation

Conceptually:

    {
        "aggs": {
            "brands": {
                "terms": {
                    "field": "brand"
                }
            }
        }
    }

This can return counts grouped by brand.

---

# 38. Shards

Elasticsearch distributes an index across shards.

Suppose:

    products index

contains millions of documents.

It can be divided into:

    Shard 1
    Shard 2
    Shard 3
    Shard 4

Conceptually:

    Products Index
       ├── Shard 1
       ├── Shard 2
       ├── Shard 3
       └── Shard 4

This allows data and search work to be distributed.

---

# 39. Why Sharding?

Sharding helps with:

- Scaling data
- Distributing search workload
- Parallel processing
- Handling large indexes

Instead of one machine handling everything:

    One huge index

we can distribute data across multiple shards/nodes.

---

# 40. Replica Shards

A replica is a copy of a primary shard.

Example:

    Primary Shard 1
         ↓
    Replica Shard 1

Replicas help provide:

- High availability
- Fault tolerance
- Additional read/search capacity

---

# 41. Primary and Replica

Conceptually:

    Index
      ↓
    Primary Shard
      ↓
    Replica Shard

If a node containing the primary fails, a replica can potentially be promoted depending on cluster state.

---

# 42. Node

A node is an Elasticsearch server/process that participates in a cluster.

Example:

    Node 1
    Node 2
    Node 3

Together:

    Elasticsearch Cluster

Different node roles can be configured depending on architecture and version.

---

# 43. Cluster

A cluster is a collection of Elasticsearch nodes working together.

Example:

    Elasticsearch Cluster
       ├── Node 1
       ├── Node 2
       └── Node 3

The cluster distributes indexes, shards, and workloads across nodes.

---

# 44. Distributed Architecture

Large Elasticsearch deployment:

    Application
        ↓
    Load Balancer
        ↓
    Elasticsearch Cluster
       ├── Node 1
       ├── Node 2
       ├── Node 3
       └── Node 4
        ↓
    Shards + Replicas

This allows Elasticsearch to scale beyond a single machine.

---

# 45. Elasticsearch and MongoDB

Suppose your MERN application uses:

    MongoDB

MongoDB stores:

    Users
    Products
    Orders

Elasticsearch can maintain a searchable copy of selected data.

Architecture:

    React
      ↓
    Node.js / Express
      ↓
    ┌──────────────┐
    │   MongoDB    │
    │ Source Truth │
    └──────────────┘
          ↓
    ┌──────────────┐
    │ Elasticsearch│
    │ Search Index │
    └──────────────┘

When a product changes:

    MongoDB updated
        ↓
    Elasticsearch index updated

---

# 46. Keeping Database and Elasticsearch in Sync

This is an important production problem.

Suppose:

    MongoDB:
    price = 50,000

    Elasticsearch:
    price = 45,000

Now search returns stale information.

Ways to synchronize include:

    Application events
    Message queues
    Change streams
    CDC
    Outbox pattern
    Scheduled reconciliation

The exact approach depends on the architecture.

---

# 47. Event-Driven Synchronization

Example:

    Product Service
        ↓
    MongoDB
        ↓
    ProductUpdated event
        ↓
    Queue / Kafka
        ↓
    Elasticsearch Consumer
        ↓
    Update search index

This decouples the main database operation from search-index updates.

---

# 48. Elasticsearch Is Not Usually the Source of Truth

A common architecture is:

    MongoDB/PostgreSQL
          ↓
    Source of Truth

    Elasticsearch
          ↓
    Search Projection

If Elasticsearch data is lost, it can often be rebuilt from the primary database.

This makes the architecture safer.

---

# 49. Search Indexing

Indexing means adding documents to Elasticsearch so they become searchable.

Example:

    Product created
        ↓
    Create Elasticsearch document
        ↓
    products index
        ↓
    Document searchable

Example document:

    {
        "id": "123",
        "name": "iPhone 15",
        "brand": "Apple",
        "price": 70000
    }

---

# 50. Updating a Document

When product information changes:

    Product updated in MongoDB
        ↓
    Update Elasticsearch document

Example:

    Old:
    price = 70000

    New:
    price = 65000

Search index should eventually reflect:

    price = 65000

---

# 51. Deleting a Document

If a product is deleted:

    MongoDB
       ↓
    Product deleted

Then:

    Elasticsearch
       ↓
    Corresponding search document deleted

Otherwise users may search and find a product that no longer exists.

---

# 52. Eventual Consistency

Database and Elasticsearch may not update at exactly the same time.

Example:

    T1:
    MongoDB updated

    T2:
    Event generated

    T3:
    Elasticsearch updated

Between T1 and T3:

    Database = new value
    Elasticsearch = old value

This is called eventual consistency.

For search systems, this is often acceptable depending on the application's requirements.

---

# 53. Search API Architecture

Example:

    GET /api/products/search?q=iphone

Flow:

    Client
       ↓
    Express Route
       ↓
    Controller
       ↓
    Search Service
       ↓
    Elasticsearch
       ↓
    Search Results
       ↓
    Client

The controller should not contain complex Elasticsearch queries.

Put search logic in a service/repository layer.

---

# 54. Node.js + Elasticsearch

A Node.js backend can communicate with Elasticsearch using an official Elasticsearch client.

Conceptually:

    import { Client } from "@elastic/elasticsearch";

    const client = new Client({
        node: process.env.ELASTICSEARCH_URL
    });

Then:

    const result = await client.search({
        index: "products",
        query: {
            match: {
                name: "iphone"
            }
        }
    });

The exact client API can vary by Elasticsearch/client version, so check the version-specific documentation when implementing it.

---

# 55. Search Service

A clean architecture could be:

    src/
    ├── controllers/
    │   └── product.controller.js
    │
    ├── services/
    │   └── product-search.service.js
    │
    ├── repositories/
    │   └── product.repository.js
    │
    └── config/
        └── elasticsearch.js

Controller:

    Receive search request

Service:

    Build search query

Elasticsearch client:

    Execute query

This keeps search infrastructure separate from HTTP logic.

---

# 56. Example Search Flow

Request:

    GET /products/search?q=iphone&brand=Apple&maxPrice=80000

Controller receives:

    q = iphone
    brand = Apple
    maxPrice = 80000

Service builds:

    Full-text query
    +
    Brand filter
    +
    Price filter

Then:

    Search Service
        ↓
    Elasticsearch
        ↓
    Results
        ↓
    Controller
        ↓
    JSON response

---

# 57. Search Result

Conceptually Elasticsearch may return:

    {
        "hits": {
            "total": {
                "value": 2
            },
            "hits": [
                {
                    "_source": {
                        "id": "101",
                        "name": "iPhone 15",
                        "price": 70000
                    }
                }
            ]
        }
    }

Your backend usually transforms this into a cleaner API response.

For example:

    {
        "data": [...],
        "total": 2
    }

Don't expose internal Elasticsearch response structure unnecessarily.

---

# 58. Highlighting

Search engines can highlight matched terms.

Example:

    Search:
    iphone

Result might conceptually show:

    Apple <em>iPhone</em> 15 Pro

This can be useful in search interfaces.

---

# 59. Geo Search

Elasticsearch can also search based on geographical location.

Example:

    Find restaurants within 5 km.

Document:

    {
        "name": "Restaurant A",
        "location": {
            "lat": 21.14,
            "lon": 79.08
        }
    }

Query:

    Find documents within a certain distance.

This is useful for:

    Nearby stores
    Restaurants
    Delivery services
    Logistics
    Location-based applications

---

# 60. Elasticsearch for Logs

Elasticsearch is also widely used for log search and analytics.

Architecture:

    Application
       ↓
    Logs
       ↓
    Log pipeline
       ↓
    Elasticsearch
       ↓
    Visualization / Dashboard

You can search:

    ERROR
    userId
    requestId
    endpoint
    timestamp

This is useful for debugging production systems.

---

# 61. ELK Stack

A well-known logging stack is:

    E = Elasticsearch
    L = Logstash
    K = Kibana

### Elasticsearch

Stores and searches data.

### Logstash

Processes/transforms and ships data.

### Kibana

Provides visualization and dashboards.

Architecture:

    Application
       ↓
    Logstash
       ↓
    Elasticsearch
       ↓
    Kibana

Modern Elastic Stack deployments can also use Elastic Agent/Beats and other components, depending on the architecture.

---

# 62. Elasticsearch for Analytics

Elasticsearch can perform aggregations over large datasets.

Examples:

    Requests per minute
    Sales by country
    Orders by category
    Average order value
    Error count
    Users by subscription type

This makes Elasticsearch useful for search + analytics workloads.

---

# 63. Search vs Caching

Don't confuse Elasticsearch with Redis.

### Redis

Mainly:

    Fast temporary data access
    Caching
    Sessions
    Queues
    Rate limiting

### Elasticsearch

Mainly:

    Full-text search
    Filtering
    Relevance
    Search analytics
    Log search

Architecture:

    Redis:
    API → Redis → Database

    Elasticsearch:
    API → Elasticsearch → Search Index

They can be used together.

---

# 64. Elasticsearch vs SQL LIKE

Example SQL:

    SELECT *
    FROM products
    WHERE name LIKE '%iphone%';

This may be fine for small/simple workloads.

But advanced search may require:

    Fuzzy matching
    Relevance ranking
    Autocomplete
    Multiple fields
    Synonyms
    Highlighting
    Faceted search

Elasticsearch is designed specifically around these search requirements.

---

# 65. Search Relevance

Suppose:

    Product A:
    "iPhone 15 Pro Max"

    Product B:
    "Phone case for iPhone"

Search:

    iPhone 15 Pro

A search engine should generally rank Product A above Product B because it is more relevant.

Relevance scoring helps produce this ordering.

---

# 66. Synonyms

Users may use different terms for the same concept.

Example:

    mobile
    smartphone
    cellphone

A search system can be configured with synonyms so that searches can match related terms.

This requires deliberate analyzer/index configuration.

---

# 67. Case Sensitivity

Search systems often normalize text.

Example:

    iPhone
    iphone
    IPHONE

Depending on the analyzer, these may be normalized to the same searchable form.

For example, lowercase analysis can convert:

    "iPhone"

to:

    "iphone"

---

# 68. Index Lifecycle

Large search systems may have many indexes over time.

For logs, for example:

    logs-2026-09-16
    logs-2026-09-17
    logs-2026-09-18

Older indexes can be:

    Deleted
    Archived
    Moved to cheaper storage

This helps control storage costs.

---

# 69. Important Elasticsearch Performance Considerations

Consider:

- Number of shards
- Number of replicas
- Query complexity
- Mapping design
- Index size
- Field types
- Aggregation cost
- Pagination strategy
- Refresh frequency
- Hardware resources
- Heap/memory
- Cache behavior

Avoid blindly creating many shards.

Shard strategy should be based on expected data size and workload.

---

# 70. Refresh

Elasticsearch indexes documents so they become searchable.

There can be a small delay between:

    Document indexed

and:

    Document searchable

This relates to Elasticsearch's refresh mechanism.

Therefore, Elasticsearch search visibility is not necessarily identical to immediate transactional database consistency.

---

# 71. Near Real-Time Search

Elasticsearch is often described as near real-time.

Meaning:

    Document update
        ↓
    Small refresh interval
        ↓
    Document becomes searchable

It is designed for fast search rather than strict immediate transactional consistency.

---

# 72. Elasticsearch Security

Production Elasticsearch deployments should consider:

- Authentication
- Authorization
- TLS
- Network access control
- Secrets management
- Encryption
- Audit logging
- Role-based access

Never expose an unsecured Elasticsearch cluster directly to the public internet.

---

# 73. Common Elasticsearch Architecture

For an e-commerce application:

    React Frontend
          ↓
    Node.js API
          ↓
    ┌───────────────┐
    │   MongoDB     │
    │ Source Truth  │
    └───────────────┘
          ↓
    ┌──────────────────┐
    │ Elasticsearch    │
    │ Search Index     │
    └──────────────────┘

Search request:

    React
      ↓
    GET /products/search?q=iphone
      ↓
    Node.js
      ↓
    Elasticsearch
      ↓
    Results
      ↓
    React

Write request:

    React
      ↓
    Node.js
      ↓
    MongoDB
      ↓
    Event / Queue
      ↓
    Elasticsearch
      ↓
    Update index

---

# 74. Why Use a Queue Between Database and Elasticsearch?

Suppose:

    10,000 products updated

Instead of synchronously updating Elasticsearch for every request:

    Database
       ↓
    Elasticsearch

we can use:

    Database
       ↓
    ProductUpdated event
       ↓
    Queue
       ↓
    Elasticsearch Worker
       ↓
    Elasticsearch

Benefits:

- Asynchronous processing
- Retry capability
- Better failure isolation
- Smoother traffic
- Easier scaling

---

# 75. What Happens If Elasticsearch Goes Down?

If Elasticsearch is only used for search:

    Elasticsearch DOWN
         ↓
    Main database still works

But:

    Search functionality may be unavailable/degraded.

A robust application should decide whether to:

    Return an error
    Use a fallback search
    Show limited results
    Temporarily disable advanced search

The correct choice depends on business requirements.

---

# 76. Elasticsearch Failure vs Database Failure

If database is the source of truth:

    Database DOWN
        ↓
    Critical application functionality affected

If Elasticsearch is a search projection:

    Elasticsearch DOWN
        ↓
    Search affected
        ↓
    Primary data may remain safe

This separation is one reason not to treat Elasticsearch as the only copy of critical application data.

---

# 77. Common Mistakes

### Mistake 1: Treating Elasticsearch as the primary database

For many applications:

    Database = source of truth
    Elasticsearch = search index

### Mistake 2: Poor mappings

Incorrect field types can make search/filtering difficult.

### Mistake 3: Using `term` for analyzed text

For full-text fields, `match` is usually more appropriate.

### Mistake 4: Ignoring synchronization

Database and search index can become inconsistent.

### Mistake 5: Too many shards

Excessive shards consume resources.

### Mistake 6: Deep pagination with inefficient approaches

Use appropriate pagination strategies such as `search_after` for deep result navigation.

### Mistake 7: Sending complex Elasticsearch queries directly from controllers

Keep search logic in a service layer.

### Mistake 8: Exposing Elasticsearch publicly

Secure the cluster and restrict network access.

---

# 78. When Should You Use Elasticsearch?

Good use cases:

    Product search
    E-commerce search
    Log search
    Full-text search
    Autocomplete
    Fuzzy search
    Location-based search
    Search analytics
    Large-scale filtering

It may be unnecessary when:

    Application is small
    Search requirements are simple
    Database search is already sufficient

Don't introduce Elasticsearch without a real search requirement.

---

# 79. Elasticsearch Interview Question

## What is Elasticsearch?

Answer:

    Elasticsearch is a distributed search and analytics engine
    optimized for fast full-text search, filtering, relevance
    ranking, and analytics over large amounts of data.

---

# 80. Interview Question

## Why use Elasticsearch instead of MongoDB/PostgreSQL?

Answer:

    Databases are generally the source of truth for application
    data, while Elasticsearch is optimized for advanced search
    workloads such as full-text search, fuzzy matching,
    relevance ranking, autocomplete, and search aggregations.

---

# 81. Interview Question

## What is an index?

Answer:

    An Elasticsearch index is a logical collection of related
    documents that are stored and searched together.

---

# 82. Interview Question

## What is a document?

Answer:

    A document is a JSON object representing a searchable record
    in Elasticsearch.

---

# 83. Interview Question

## What is a shard?

Answer:

    A shard is a partition of an Elasticsearch index that allows
    data and search workloads to be distributed across nodes.

---

# 84. Interview Question

## What is a replica?

Answer:

    A replica is a copy of a primary shard used mainly for
    fault tolerance and can also provide additional search
    capacity.

---

# 85. Interview Question

## What is the difference between `text` and `keyword`?

Answer:

    `text` is generally analyzed and used for full-text search,
    while `keyword` is generally used for exact matching,
    filtering, sorting, and aggregations.

---

# 86. Interview Question

## What is an inverted index?

Answer:

    An inverted index maps searchable terms to the documents
    containing those terms, allowing Elasticsearch to locate
    matching documents efficiently without scanning every
    document.

---

# 87. Interview Question

## What is a match query?

Answer:

    A match query is generally used for full-text search because
    the search text is analyzed before matching.

---

# 88. Interview Question

## What is a term query?

Answer:

    A term query is generally used for exact, unanalyzed term
    matching, commonly against keyword fields.

---

# 89. Interview Question

## What are aggregations?

Answer:

    Aggregations allow Elasticsearch to calculate analytics such
    as counts, averages, ranges, and grouped statistics over
    documents.

---

# 90. Interview Scenario

## Design product search for an e-commerce application.

Requirements:

    Search product name
    Search description
    Filter by brand
    Filter by category
    Filter by price
    Sort by price
    Support typo tolerance
    Show brand counts

Architecture:

    React
      ↓
    Node.js API
      ↓
    Search Service
      ↓
    Elasticsearch

Database:

    MongoDB/PostgreSQL
       ↓
    Source of truth

Synchronization:

    Database
       ↓
    Event / Queue
       ↓
    Elasticsearch

Search:

    User query
       +
    Filters
       +
    Sorting
       +
    Aggregations
       ↓
    Elasticsearch
       ↓
    Results

---

# 91. Interview Scenario

## Product updated in MongoDB but search still shows old price. Why?

Answer:

    Elasticsearch is a separate search index and may not have been
    updated yet. This can happen because of asynchronous indexing,
    queue delays, failed synchronization, or eventual consistency.

I would investigate:

    Event generation
    Queue
    Elasticsearch worker
    Indexing errors
    Document mapping
    Synchronization status

---

# 92. Interview Scenario

## Elasticsearch is down. Should the entire application go down?

Answer:

    Not necessarily.

    If Elasticsearch is only used as a search projection, the
    primary database and core application functionality can
    continue while search is degraded or unavailable.

    The exact fallback behavior should be defined according to
    business requirements.

---

# 93. Interview Scenario

## Search API is slow. What would you investigate?

Check:

    Query complexity
    Number of documents
    Number of shards
    Aggregations
    Sorting
    Pagination
    Mapping
    Cluster health
    Node CPU
    Memory/heap
    Disk performance
    Cache behavior
    Network latency

Do not immediately add more servers without measuring the bottleneck.

---

# 94. Quick Revision

    Elasticsearch
        ↓
    Distributed search and analytics engine
        ↓
    Optimized for:
        Full-text search
        Fuzzy search
        Autocomplete
        Filtering
        Sorting
        Relevance
        Aggregations
        Log search

Core concepts:

    Document
    Index
    Field
    Mapping
    Analyzer
    Token
    Inverted Index
    Query
    Shard
    Replica
    Node
    Cluster
    Aggregation

Important query concepts:

    match
    term
    bool
    must
    filter
    must_not
    should

Architecture:

    Database
        ↓
    Source of truth

    Event / Queue
        ↓
    Elasticsearch
        ↓
    Search API
        ↓
    Client

Remember:

    Database = source of truth

    Elasticsearch = searchable projection/index

---

# 95. One-Line Interview Summary

Elasticsearch is a distributed search and analytics engine that uses structures such as inverted indexes, analyzers, shards, and replicas to provide fast full-text search, filtering, relevance ranking, and analytics at scale.