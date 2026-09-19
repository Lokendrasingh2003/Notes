# Logging, Monitoring and Observability in Backend Development

## 1. What are Logging, Monitoring and Observability?

These three concepts help us understand what is happening inside a running application.

A simple mental model:

    Logging
        ↓
    What happened?

    Monitoring
        ↓
    Is the system healthy?

    Observability
        ↓
    Why is the system behaving this way?

Example:

    Users report:
    "The application is slow."

Logging can tell us:

    Request /orders took 4.2 seconds

Monitoring can tell us:

    Average API latency increased from 200ms → 4s

Observability can help us investigate:

    API
      ↓
    Order Service
      ↓
    Database
      ↓
    Slow query
      ↓
    Database CPU increased

So:

    Logs      → Events and details
    Metrics   → Measurements and trends
    Traces    → Request journey across services

Together they provide visibility into the system.

---

# 2. Why Do We Need Them?

A backend can work perfectly on a developer's machine but behave differently in production.

Without proper visibility:

    User reports problem
          ↓
    Developer guesses
          ↓
    Search through code
          ↓
    Try to reproduce
          ↓
    Difficult debugging

With observability:

    User reports problem
          ↓
    Request ID
          ↓
    Logs
          ↓
    Metrics
          ↓
    Trace
          ↓
    Identify failing component
          ↓
    Fix problem

This becomes especially important when the application has:

    Multiple servers
    Microservices
    Background workers
    Queues
    Databases
    External APIs
    Load balancers
    Caches

---

# 3. Logging

Logging means recording important events that happen inside an application.

Example:

    User logged in
    Order created
    Payment failed
    Database connection failed
    Request completed

A log might contain:

    {
      level: "info",
      message: "Order created",
      orderId: "order-123",
      userId: "user-456"
    }

---

# 4. Why Logging is Important

Logs help developers:

- Debug errors
- Understand application behavior
- Investigate incidents
- Track important operations
- Identify failures
- Understand user/request flow
- Diagnose production issues

Example:

    12:10:01 Request received
    12:10:02 Order validation completed
    12:10:03 Payment initiated
    12:10:08 Payment timeout
    12:10:08 Order marked as failed

From these logs, we can understand what happened.

---

# 5. Log Levels

Different logs have different importance.

Common levels:

    DEBUG
    INFO
    WARN
    ERROR
    FATAL

---

## DEBUG

Detailed information useful during development/debugging.

Example:

    Database query parameters
    Cache lookup details
    Internal processing steps

DEBUG logs are usually not enabled at high volume in production.

---

## INFO

Normal important application events.

Examples:

    Server started
    User logged in
    Order created
    Job completed

Example:

    INFO: Order created successfully

---

## WARN

Something unusual happened, but the application can continue.

Examples:

    Cache unavailable
    Retry attempt
    High memory usage
    External API responding slowly

Example:

    WARN: Payment API response time exceeded threshold

---

## ERROR

Something failed.

Examples:

    Database query failed
    Payment failed
    External API request failed

Example:

    ERROR: Failed to process payment

---

## FATAL

A critical failure that may prevent the application from continuing.

Example:

    FATAL: Unable to initialize required database connection

---

# 6. Structured Logging

There are two common approaches.

## Unstructured logging

Example:

    Order 123 created by user 456

This is easy to read but difficult for machines to search and analyze.

---

## Structured logging

Example:

    {
      "level": "info",
      "event": "order_created",
      "orderId": "123",
      "userId": "456"
    }

Structured logs are easier to:

- Search
- Filter
- Aggregate
- Analyze
- Send to log-management systems

For production backend systems, structured logging is generally preferred.

---

# 7. Important Log Fields

A useful production log can contain:

    timestamp
    level
    message
    service
    environment
    requestId
    userId
    operation
    duration
    error
    stack

Example:

    {
      "timestamp": "2026-09-19T12:30:00Z",
      "level": "error",
      "service": "order-service",
      "environment": "production",
      "requestId": "req-123",
      "operation": "create_order",
      "duration": 4200,
      "message": "Payment service timeout"
    }

Not every log needs every field.

---

# 8. Request ID

A request ID uniquely identifies a request.

Example:

    Client
       ↓
    requestId = req-123
       ↓
    API Gateway
       ↓
    Backend
       ↓
    Order Service
       ↓
    Payment Service

If the request fails, logs across services can use:

    req-123

Then developers can search for:

    req-123

and reconstruct what happened.

---

# 9. Correlation ID

A correlation ID serves a similar purpose in distributed systems.

Suppose:

    Request
       ↓
    API Gateway
       ↓
    Order Service
       ↓
    Payment Service
       ↓
    Notification Service

All services can attach the same correlation ID.

Example:

    correlationId = abc-123

Then:

    API Gateway      → abc-123
    Order Service    → abc-123
    Payment Service  → abc-123
    Notification     → abc-123

This makes distributed debugging much easier.

---

# 10. What Should We Log?

Good things to log:

    Request started
    Request completed
    Important business events
    External API failures
    Database failures
    Authentication failures
    Background job failures
    Retry attempts
    Configuration errors
    System-level warnings

Examples:

    order_created
    payment_failed
    user_login_failed
    email_send_failed
    background_job_retry

---

# 11. What Should NOT Be Logged?

Never casually log sensitive information.

Avoid logging:

    Passwords
    JWT secrets
    Access tokens
    Refresh tokens
    API keys
    Private keys
    Credit card numbers
    Sensitive personal information

Bad:

    {
      "email": "...",
      "password": "secret123"
    }

Better:

    {
      "event": "login_failed",
      "userId": "123"
    }

Sensitive data should be masked or excluded.

---

# 12. Logging Request and Response

You may want to log:

    HTTP method
    URL
    status code
    duration
    request ID

Example:

    {
      "method": "GET",
      "path": "/api/users/123",
      "statusCode": 200,
      "duration": 120,
      "requestId": "req-123"
    }

But be careful about logging:

    Request body
    Authorization headers
    Cookies
    Sensitive response data

These may contain secrets or personal information.

---

# 13. Logging Errors

A good error log should provide context.

Example:

    {
      "level": "error",
      "event": "payment_failed",
      "requestId": "req-123",
      "orderId": "order-456",
      "error": {
        "message": "Payment service timeout",
        "type": "TimeoutError"
      }
    }

In server-side logs, the stack trace can also be recorded when appropriate.

The client should receive a safe error response instead of the internal stack trace.

---

# 14. Logging Libraries

In Node.js applications, common logging libraries include:

    Pino
    Winston

The important concept is not the library itself.

The important concepts are:

    Structured logs
    Log levels
    Context
    Request IDs
    Log aggregation
    Security
    Performance

---

# 15. Console.log vs Production Logging

For small development projects:

    console.log("Server started")

may be enough.

For production systems, a dedicated logger is generally better because it can provide:

    Log levels
    Structured output
    Timestamps
    Metadata
    Log formatting
    Transports
    Integration with log platforms

Example conceptual flow:

    Application
        ↓
    Logger
        ↓
    JSON Logs
        ↓
    Log Collector
        ↓
    Log Storage/Search
        ↓
    Dashboard

---

# 16. Log Aggregation

Imagine you have:

    Server 1
    Server 2
    Server 3
    Server 4

Each server produces logs.

Searching every server manually is difficult.

Instead:

    Server 1 ─┐
    Server 2 ─┤
    Server 3 ─┼──→ Log Collector → Central Log System
    Server 4 ─┘

Now developers can search logs from one place.

Examples of log platforms/technologies include:

    Elasticsearch + Kibana
    Loki + Grafana
    CloudWatch
    Datadog
    Splunk

---

# 17. Monitoring

Monitoring means continuously measuring the health and performance of a system.

Examples:

    CPU usage
    Memory usage
    Request rate
    Error rate
    Response time
    Database connections
    Queue depth
    Disk usage

Monitoring answers:

    "Is the system healthy?"

---

# 18. Metrics

Metrics are numerical measurements collected over time.

Examples:

    requests_per_second
    error_rate
    response_time
    cpu_usage
    memory_usage
    active_connections
    queue_length

Example:

    API Requests:
    10,000 requests/minute

    Error Rate:
    2%

    Average Latency:
    180ms

These values can be plotted on dashboards.

---

# 19. Four Important Golden Signals

A very useful monitoring concept is the four golden signals.

## 1. Latency

How long requests take.

Example:

    Average response time = 200ms

---

## 2. Traffic

How much demand the system is receiving.

Example:

    500 requests/second

---

## 3. Errors

How many requests are failing.

Example:

    2% HTTP 5xx errors

---

## 4. Saturation

How close the system is to its capacity.

Examples:

    CPU = 90%
    Memory = 85%
    Database connections = 95%

Simple mental model:

    Latency
    Traffic
    Errors
    Saturation

These four metrics provide a useful high-level view of system health.

---

# 20. Application Metrics

Backend applications can expose metrics such as:

    HTTP request count
    HTTP error count
    Request duration
    Active requests
    Database query duration
    Cache hit rate
    Queue length
    Job processing time
    External API latency

Example:

    POST /orders

Metrics:

    Requests = 10,000
    Errors = 120
    Average latency = 300ms
    p95 latency = 850ms

---

# 21. Percentiles

Average latency alone can be misleading.

Suppose:

    99 requests = 100ms
    1 request = 10 seconds

The average may not fully describe the user experience.

Common percentiles:

    p50
    p90
    p95
    p99

---

## p50

50% of requests are faster than this value.

Also called median.

---

## p95

95% of requests are faster than this value.

5% are slower.

---

## p99

99% of requests are faster than this value.

1% are slower.

Example:

    p50 = 100ms
    p95 = 500ms
    p99 = 2s

This tells us some requests are significantly slower than the median.

---

# 22. Monitoring Dashboards

A dashboard can show:

    Requests/sec
    Error rate
    p50 latency
    p95 latency
    p99 latency
    CPU
    Memory
    Database connections
    Queue depth

Example:

    API Health Dashboard

    Request Rate:     1,200 req/s
    Error Rate:       0.8%
    p95 Latency:      420ms
    CPU:              65%
    Memory:           72%
    DB Connections:   80/100

This gives engineers a quick view of system health.

---

# 23. Alerts

Monitoring becomes more useful when it can alert engineers.

Example:

    Error rate > 5%
          ↓
    Alert

Another:

    CPU > 90% for 10 minutes
          ↓
    Alert

Another:

    Database connections > 95%
          ↓
    Alert

Good alerts should indicate actionable problems.

---

# 24. Alert Fatigue

If the system sends hundreds of unnecessary alerts:

    ALERT
    ALERT
    ALERT
    ALERT
    ALERT

engineers may start ignoring them.

This is called alert fatigue.

Good alerts should be:

    Relevant
    Actionable
    Specific
    Properly thresholded

---

# 25. Health Checks

A health-check endpoint can tell whether a service is functioning.

Example:

    GET /health

Response:

    {
      "status": "ok"
    }

A more detailed health check might check:

    Application
       ↓
    Database
       ↓
    Redis
       ↓
    External dependencies

Example:

    {
      "status": "ok",
      "database": "ok",
      "redis": "ok"
    }

---

# 26. Liveness vs Readiness

This is especially important in containerized systems.

## Liveness

Answers:

    "Is the application process alive?"

If liveness fails, the platform may restart the application.

---

## Readiness

Answers:

    "Is the application ready to receive traffic?"

Example:

    Application started
          ↓
    Database not connected
          ↓
    Not ready

The application may be alive but should not receive requests yet.

Simple difference:

    Liveness → Should this process be restarted?

    Readiness → Should this process receive traffic?

---

# 27. Observability

Observability is the ability to understand the internal state of a system from the data it produces.

The three main pillars are:

    Logs
    Metrics
    Traces

Mental model:

    Logs
      ↓
    What happened?

    Metrics
      ↓
    How much / how often?

    Traces
      ↓
    Where did the request spend time?

Together:

    Logs + Metrics + Traces
              ↓
         Observability

---

# 28. Distributed Tracing

Suppose a request goes through:

    Client
      ↓
    API Gateway
      ↓
    Order Service
      ↓
    Payment Service
      ↓
    Database

A distributed trace tracks this request across the system.

Conceptually:

    Trace
      ├── API Gateway
      │      └── 20ms
      │
      ├── Order Service
      │      └── 80ms
      │
      ├── Payment Service
      │      └── 1500ms
      │
      └── Database
             └── 100ms

Now we can immediately see:

    Payment Service
          ↓
    Major latency

---

# 29. Trace and Span

A trace represents the complete journey of a request.

A span represents one operation within that trace.

Example:

    Trace: Create Order

        Span 1 → API Gateway
        Span 2 → Order Service
        Span 3 → Database
        Span 4 → Payment Service
        Span 5 → Notification Service

Mental model:

    Trace = complete journey

    Span = one step in the journey

---

# 30. Why Tracing Matters

Suppose:

    API latency = 3 seconds

Metrics tell us:

    p95 latency = 3s

Logs tell us:

    Order request took 3s

But tracing can tell us:

    API Gateway       = 20ms
    Order Service     = 100ms
    Database          = 200ms
    Payment Service   = 2.6s

Now we know where the problem is.

---

# 31. OpenTelemetry

OpenTelemetry is a widely used open-source observability framework.

It can collect and export:

    Traces
    Metrics
    Logs

Conceptual architecture:

    Application
         ↓
    OpenTelemetry
         ↓
    Collector
         ↓
    Observability Backend

Possible backends include:

    Grafana
    Jaeger
    Prometheus
    Datadog
    New Relic
    Elastic

OpenTelemetry is mainly about instrumentation and telemetry collection/export; the storage and visualization system can be separate.

---

# 32. Prometheus

Prometheus is commonly used for metrics collection and monitoring.

Conceptually:

    Application
       ↓
    Metrics
       ↓
    Prometheus
       ↓
    Time-series data
       ↓
    Dashboard / Alerting

Example metrics:

    http_requests_total
    http_request_duration
    process_cpu_usage

Prometheus is primarily focused on metrics, not general-purpose application logs.

---

# 33. Grafana

Grafana is commonly used to visualize metrics and other observability data.

Example:

    Prometheus
        ↓
    Grafana
        ↓
    Dashboard

Dashboard:

    Request Rate
    Error Rate
    CPU
    Memory
    Latency
    Database Metrics

Grafana can also work with multiple data sources.

---

# 34. ELK Stack

ELK commonly refers to:

    Elasticsearch
    Logstash
    Kibana

Conceptual flow:

    Application
        ↓
    Logstash
        ↓
    Elasticsearch
        ↓
    Kibana
        ↓
    Search / Visualization

Elasticsearch stores/indexes logs.

Logstash processes/transforms logs.

Kibana provides search and visualization.

Modern Elastic deployments can use other collection components as well, but the classic ELK concept remains important.

---

# 35. Logs vs Metrics vs Traces

| Type | Main Question | Example |
|---|---|---|
| Logs | What happened? | Payment failed |
| Metrics | How much/how often? | 5% error rate |
| Traces | Where did time go? | Payment service took 2.5s |

Another way:

    Logs    → Events
    Metrics → Numbers
    Traces  → Request journey

---

# 36. Logging vs Monitoring

Logging:

    Detailed events

Example:

    "Payment API timeout for order 123"

Monitoring:

    Aggregated health information

Example:

    Payment error rate = 4.2%

So:

    Logs → Detailed information

    Monitoring → System health and trends

---

# 37. Monitoring vs Observability

Monitoring usually focuses on known conditions.

Example:

    Alert when CPU > 90%

Observability helps investigate unknown problems.

Example:

    Users report:
    "Checkout is slow"

Observability helps answer:

    Which service?
    Which endpoint?
    Which database query?
    Which dependency?
    Which requests?
    Since when?
    Which deployment caused it?

---

# 38. Instrumentation

Instrumentation means adding mechanisms to an application so it produces useful telemetry.

Examples:

    Request metrics
    Database timings
    Traces
    Structured logs
    Error information

Conceptually:

    Application
       ↓
    Instrumentation
       ↓
    Telemetry
       ↓
    Logs / Metrics / Traces

---

# 39. Automatic vs Manual Instrumentation

## Automatic instrumentation

A framework/library automatically collects telemetry.

Example:

    HTTP requests
    Database calls

---

## Manual instrumentation

Developers explicitly create telemetry around important operations.

Example:

    Start span
       ↓
    Process payment
       ↓
    End span

Manual instrumentation is useful for important business operations that automatic instrumentation may not understand.

---

# 40. Business Metrics

Not all metrics are infrastructure metrics.

Business metrics are also important.

Examples:

    Orders per minute
    Successful payments
    Cart abandonment
    Signups
    Subscription cancellations
    Successful checkouts

Example:

    Infrastructure:
        CPU = 40%

    Business:
        Orders/minute = 120

A system can have healthy CPU while business functionality is failing.

---

# 41. SLI

SLI means:

    Service Level Indicator

It is a measurement of service performance.

Examples:

    Availability
    Latency
    Error rate

Example:

    99.8% of requests completed successfully

That is an SLI.

---

# 42. SLO

SLO means:

    Service Level Objective

It is the target for an SLI.

Example:

    SLI:
    Request availability

    SLO:
    99.9% availability

So:

    SLI = What we measure

    SLO = Target we want to achieve

---

# 43. SLA

SLA means:

    Service Level Agreement

It is a formal agreement, often with customers, defining service commitments.

Example:

    Service availability commitment = 99.9%

Simple distinction:

    SLI → Measurement

    SLO → Internal target

    SLA → Formal commitment/agreement

---

# 44. Error Budget

Suppose your SLO is:

    99.9% availability

Then allowed downtime/error budget is:

    0.1%

The error budget represents how much unreliability is acceptable under the SLO.

If the service consumes the budget quickly, the team may prioritize reliability work.

---

# 45. Performance Monitoring

Important backend performance metrics include:

    CPU
    Memory
    Disk
    Network
    Request rate
    Latency
    Error rate
    Database latency
    Cache hit rate
    Queue depth
    Worker utilization

Example:

    API latency increasing
          ↓
    Check database latency
          ↓
    Database query slow
          ↓
    Check query/index
          ↓
    Optimize query

---

# 46. Database Monitoring

Monitor:

    Connection count
    Query latency
    Slow queries
    CPU
    Memory
    Disk usage
    Locks
    Replication lag
    Connection failures

Example:

    API latency increased
          ↓
    Database latency increased
          ↓
    Slow query detected
          ↓
    Missing/inefficient index
          ↓
    Optimize query/index

---

# 47. Queue Monitoring

For background workers, monitor:

    Queue depth
    Job processing time
    Failed jobs
    Retry count
    Worker count
    Job age
    Dead-letter queue size

Example:

    Queue depth
        ↓
    100
        ↓
    5,000
        ↓
    Workers cannot keep up

This can indicate a scaling problem.

---

# 48. Cache Monitoring

For Redis or another cache, monitor:

    Cache hit rate
    Cache miss rate
    Memory usage
    Evictions
    Connection count
    Latency

Example:

    Cache hit rate = 95%

If it suddenly drops:

    95%
      ↓
    60%

investigate why.

---

# 49. External Dependency Monitoring

If your application depends on:

    Stripe
    Cloudinary
    Email provider
    Elasticsearch
    Payment gateway

monitor:

    Request count
    Success rate
    Failure rate
    Latency
    Timeout count

Example:

    Payment API
       ↓
    Error rate = 8%
       ↓
    Alert

---

# 50. Deployment Monitoring

After a deployment, monitor:

    Error rate
    Latency
    CPU
    Memory
    Request rate
    Database errors

Example:

    Before deployment:

    Error rate = 0.5%

    After deployment:

    Error rate = 7%

This can indicate a regression introduced by the deployment.

---

# 51. Observability During an Incident

Imagine users report:

    "Checkout is failing."

A useful investigation:

    Step 1:
    Check error rate

        ↓

    Step 2:
    Check affected endpoint

        ↓

    Step 3:
    Search logs

        ↓

    Step 4:
    Use request ID

        ↓

    Step 5:
    Open distributed trace

        ↓

    Step 6:
    Identify slow/failing dependency

        ↓

    Step 7:
    Check recent deployment

        ↓

    Step 8:
    Fix / rollback / mitigate

This is where observability becomes extremely valuable.

---

# 52. Example: Debugging a Slow API

Problem:

    GET /products
    sometimes takes 5 seconds

Monitoring shows:

    p50 = 100ms
    p95 = 4s
    p99 = 6s

This tells us the problem affects slower requests.

Logs show:

    requestId = req-789

Trace:

    API                = 20ms
    Product Service    = 50ms
    Elasticsearch      = 3.8s
    Database           = 100ms

Now we know:

    Elasticsearch
          ↓
    Main source of latency

Then investigate:

    Query
    Index
    Cluster health
    Shards
    Load
    Network

---

# 53. Example: Debugging High Error Rate

Monitoring:

    Error rate
       ↓
    0.5% → 8%

Logs:

    ERROR payment_service_timeout

Traces:

    Checkout
       ↓
    Payment Service
       ↓
    Timeout

External dependency metrics:

    Payment API latency increased

Conclusion:

    Payment dependency is failing/slow.

Possible actions:

    Retry where safe
    Circuit breaker
    Increase timeout only if appropriate
    Fail gracefully
    Contact dependency provider
    Monitor recovery

---

# 54. Production Observability Architecture

A typical architecture:

    ┌───────────────────────┐
    │      Application      │
    └───────────┬───────────┘
                │
        ┌───────┼────────┐
        ↓       ↓        ↓
      Logs    Metrics   Traces
        ↓       ↓        ↓
      Logger  Metrics  OpenTelemetry
        ↓       ↓        ↓
        └───────┼────────┘
                ↓
       Observability Platform
                ↓
       Dashboards / Alerts
                ↓
             Engineers

---

# 55. What Should Be Monitored?

## Application

    Request rate
    Error rate
    Latency
    Active requests

## Infrastructure

    CPU
    Memory
    Disk
    Network

## Database

    Connections
    Query latency
    Errors
    Replication lag

## Cache

    Hit rate
    Memory
    Evictions
    Latency

## Queue

    Queue depth
    Failed jobs
    Processing time
    Retry count

## External Services

    Success rate
    Error rate
    Latency
    Timeouts

## Business

    Orders
    Payments
    Signups
    Revenue-related events

---

# 56. Common Mistakes

## Mistake 1: Logging everything

Too many logs create:

    High storage cost
    Noise
    Difficult searching
    Performance overhead

Log useful information.

---

## Mistake 2: Logging sensitive data

Never log secrets or sensitive credentials.

---

## Mistake 3: No request IDs

Debugging distributed systems becomes much harder.

---

## Mistake 4: Monitoring only CPU

CPU can be normal while:

    Database is failing
    Payment service is down
    API latency is high
    Error rate is increasing

Monitor application-level metrics too.

---

## Mistake 5: Only monitoring averages

Average latency can hide slow requests.

Use:

    p50
    p95
    p99

where appropriate.

---

## Mistake 6: Too many alerts

This creates alert fatigue.

---

## Mistake 7: No business metrics

Infrastructure health does not always mean business health.

---

## Mistake 8: No tracing in distributed systems

With multiple services, tracing becomes very useful for understanding request flow.

---

# 57. Best Practices

## Logging

    Use structured logs
    Use log levels
    Include timestamps
    Include request/correlation IDs
    Include useful context
    Avoid sensitive information

## Monitoring

    Monitor latency
    Monitor traffic
    Monitor errors
    Monitor saturation
    Monitor dependencies
    Monitor business metrics

## Observability

    Collect logs
    Collect metrics
    Collect traces
    Correlate telemetry
    Create dashboards
    Create actionable alerts

---

# 58. Interview Question: What is Logging?

### Answer:

Logging is the process of recording application events and useful contextual information so developers can understand what happened inside the system and troubleshoot issues.

---

# 59. Interview Question: What is Monitoring?

### Answer:

Monitoring is the continuous collection and evaluation of system metrics to determine whether the application is healthy and to detect known problems through dashboards and alerts.

---

# 60. Interview Question: What is Observability?

### Answer:

Observability is the ability to understand the internal state and behavior of a system from the telemetry it produces, primarily logs, metrics, and traces.

---

# 61. Interview Question: Difference Between Logging, Monitoring and Observability?

### Answer:

    Logging:
    Records detailed events.

    Monitoring:
    Measures system health and alerts on known problems.

    Observability:
    Helps investigate and understand why the system behaves a certain way.

---

# 62. Interview Question: What are the Three Pillars of Observability?

### Answer:

    Logs
    Metrics
    Traces

Logs tell us what happened.

Metrics tell us how much/how often.

Traces show the journey of a request through the system.

---

# 63. Interview Question: What is Structured Logging?

### Answer:

Structured logging records logs in a machine-readable format, commonly JSON, with fields such as timestamp, level, request ID, service, event, and error details. This makes logs easier to search, filter, and analyze.

---

# 64. Interview Question: What is a Request ID?

### Answer:

A request ID is a unique identifier assigned to a request so that all related logs and operations can be correlated during debugging.

---

# 65. Interview Question: What are the Four Golden Signals?

### Answer:

    Latency
    Traffic
    Errors
    Saturation

They provide a useful high-level view of service health.

---

# 66. Interview Question: What is Distributed Tracing?

### Answer:

Distributed tracing tracks a request as it moves across multiple services and records individual operations as spans, making it easier to identify where latency or failures occur.

---

# 67. Interview Question: What is the difference between a Trace and a Span?

### Answer:

A trace represents the complete journey of a request, while a span represents one individual operation within that trace.

---

# 68. Interview Question: What is OpenTelemetry?

### Answer:

OpenTelemetry is an open-source observability framework used to instrument applications and collect/export telemetry such as traces, metrics, and logs.

---

# 69. Interview Scenario

### Interviewer:

Your API suddenly becomes slow in production. How would you investigate it?

### Answer:

I would first check monitoring metrics such as request rate, error rate, p95/p99 latency, CPU, memory, database latency, and dependency latency. Then I would use request IDs and structured logs to investigate affected requests. If the system is distributed, I would inspect traces to identify which service or dependency is consuming most of the request time. I would also check recent deployments and infrastructure changes.

---

# 70. Interview Scenario

### Interviewer:

Your error rate suddenly increases after a deployment. What would you do?

### Answer:

I would compare the error rate before and after the deployment, identify the affected endpoints and error types through metrics and logs, inspect traces for representative requests, and check whether the errors correlate with the new release. Depending on the severity, I would mitigate the issue through rollback or another safe recovery mechanism and then investigate the root cause.

---

# 71. Interview Scenario

### Interviewer:

You have 10 backend servers. How would you debug one failed request?

### Answer:

I would use a unique request or correlation ID that follows the request across services. I would search centralized structured logs using that ID and inspect the corresponding distributed trace. This allows me to identify which server, service, database, or external dependency caused the failure.

---

# 72. Interview Scenario

### Interviewer:

Your application CPU is normal, but users say the application is slow. What would you check?

### Answer:

I would not assume CPU is the problem. I would check request latency, p95/p99 latency, database latency, external API latency, cache performance, queue delays, network issues, and distributed traces to identify where the time is being spent.

---

# 73. SLI / SLO / SLA Quick Revision

    SLI
    ↓
    What we measure

    SLO
    ↓
    Target we want

    SLA
    ↓
    Formal commitment

Example:

    SLI:
    API availability = 99.95%

    SLO:
    Target availability = 99.9%

    SLA:
    Contractual availability commitment

---

# 74. Quick Revision

    Logging
       ↓
    Record events

    Monitoring
       ↓
    Measure system health

    Observability
       ↓
    Understand system behavior

Three pillars:

    Logs
    Metrics
    Traces

Important logging concepts:

    Log levels
    Structured logging
    Request IDs
    Correlation IDs
    Centralized logging
    Sensitive-data protection

Important monitoring concepts:

    Traffic
    Latency
    Errors
    Saturation
    p50
    p95
    p99
    Health checks
    Alerts
    Dashboards

Important observability concepts:

    Distributed tracing
    Trace
    Span
    Instrumentation
    OpenTelemetry
    Prometheus
    Grafana
    ELK
    SLI
    SLO
    SLA
    Error budget

---

# 75. One-Line Interview Summary

> Logging records what happened, monitoring tells us whether the system is healthy, and observability combines logs, metrics, and traces to help us understand why the system is behaving the way it is.