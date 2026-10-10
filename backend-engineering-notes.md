# Backend Engineering Notes

## 1. HTTP and Networking
- HTTP defines communication between clients and servers.
- Common methods: GET, POST, PUT, PATCH, DELETE.
- Understand status codes, headers, cookies, HTTPS, TLS, CORS, and caching.
- HTTPS protects data in transit using TLS.

## 2. Routing and APIs
- Routing maps a URL and HTTP method to application logic.
- Path parameters identify resources; query parameters support filtering, sorting, and pagination.
- REST APIs use resource-oriented URLs and HTTP semantics.
- Document APIs with OpenAPI and version them carefully.

## 3. Serialization and Validation
- Serialization converts application data into formats such as JSON.
- Deserialization converts incoming data into application structures.
- Validate types, formats, required fields, and business rules on the server.
- Normalize inputs where appropriate and handle invalid data safely.

## 4. Authentication and Authorization
- Authentication verifies who a user is.
- Authorization determines what the user can access or do.
- Common mechanisms include sessions, cookies, JWT, API keys, and OAuth 2.0.
- Apply least privilege and protect credentials and tokens.
- Use secure cookie settings and defend against common web vulnerabilities.

## 5. Middleware and Request Context
- Middleware performs shared work around requests, such as logging, authentication, validation, and error handling.
- Middleware order matters.
- Request context can carry request IDs, identity information, deadlines, and cancellation signals.
- Keep context small and specific to the request lifecycle.

## 6. Application Layers
- Controller/handler: handles the HTTP request and response.
- Service: implements business rules and use cases.
- Repository/data access layer: reads and writes data.
- Separation of concerns improves testing and maintainability.

## 7. Databases
- Learn SQL, joins, constraints, indexes, transactions, and schema design.
- ACID stands for atomicity, consistency, isolation, and durability.
- Indexes can improve reads but add storage and write overhead.
- Use connection pooling and inspect slow queries.
- Understand ORM behavior and avoid N+1 query problems.

## 8. Caching
- Caching stores frequently used data for faster access.
- Common approaches: cache-aside, read-through, write-through, and write-back.
- TTL controls how long a cached value remains valid.
- Cache invalidation and stale data are important design concerns.
- Redis is commonly used for distributed caching.

## 9. Background Jobs and Queues
- Use background workers for tasks that should not block an HTTP response.
- Examples include email delivery, image processing, and bulk operations.
- A queue typically connects producers with consumers/workers.
- Design retries carefully and make repeated processing safe when possible.
- Scheduling supports recurring or delayed tasks.

## 10. Error Handling
- Handle errors at appropriate boundaries and use centralized error handling.
- Return consistent, safe error responses to clients.
- Log enough context to diagnose problems without exposing secrets.
- Avoid silently swallowing errors.

## 11. Configuration and Secrets
- Keep environment-specific configuration separate from application code.
- Use environment variables or configuration files as appropriate.
- Never commit credentials, tokens, or private keys.
- Restrict access to secrets and rotate them when necessary.

## 12. Logging and Observability
- Logs record events; metrics measure behavior; traces follow requests across components.
- Together, logs, metrics, and traces help explain system behavior.
- Use meaningful log levels and structured logs.
- Monitor latency, error rates, throughput, and resource usage.
- Do not log passwords, tokens, or sensitive data unnecessarily.

## 13. Security
- Common risks include SQL injection, XSS, CSRF, broken access control, and unsafe deserialization.
- Validate untrusted input and use parameterized database queries.
- Enforce authorization for individual resources and operations.
- Apply rate limiting, secure headers, HTTPS, and least privilege.
- Use defense in depth rather than relying on one security control.

## 14. Performance and Scalability
- Measure before optimizing.
- Investigate slow database queries, N+1 queries, large payloads, and unnecessary network calls.
- Use pagination, batching, caching, and compression where appropriate.
- Vertical scaling adds resources to one machine; horizontal scaling adds more instances.
- Design for timeouts, dependency failures, and graceful degradation.

## 15. Concurrency and Real-Time Communication
- Concurrency means tasks make overlapping progress.
- Parallelism means tasks execute at the same time.
- I/O-bound and CPU-bound workloads may require different approaches.
- WebSockets support bidirectional communication; server-sent events stream updates from server to client.

## 16. File and Object Storage
- Store large files in object storage rather than keeping them in application memory.
- Use streaming or multipart uploads for large files.
- Validate file type and size and enforce access controls.

## 17. Testing and Code Quality
- Unit tests cover individual units; integration tests cover collaborating components; end-to-end tests cover complete workflows.
- Test error cases, edge cases, and security-sensitive behavior.
- Automated tests, linting, and formatting help maintain code quality.
- Coverage is useful, but meaningful assertions matter more than a high percentage alone.

## 18. Graceful Shutdown
- Stop accepting new work.
- Allow in-flight requests or jobs to finish when possible.
- Close database connections and other resources.
- Then terminate the application.

## 19. Webhooks and Email
- Webhooks deliver event notifications to an HTTP endpoint.
- Verify webhook signatures and handle duplicate deliveries and retries.
- Transactional emails include verification, password reset, receipts, and notifications.
- Use background jobs for email delivery when appropriate.

## 20. Search and Distributed Systems
- Search engines such as Elasticsearch support full-text search, relevance scoring, and aggregations.
- Understand indexes, mappings, analyzers, and shards.
- Distributed services require attention to timeouts, retries, consistency, and partial failures.

## 21. DevOps and Deployment
- CI runs automated checks as code changes.
- CD automates delivery or deployment.
- Docker packages applications into containers.
- Kubernetes orchestrates containers and supports deployment and scaling.
- Understand rolling deployments, environment configuration, health checks, and infrastructure as code.

## 22. Twelve-Factor and API Contracts
- Build applications that are configurable, portable, and suitable for automated deployment.
- OpenAPI describes endpoints, parameters, request bodies, responses, schemas, and security requirements.
- API-first design defines the contract before or alongside implementation.

## Key Principles
- Keep responsibilities clearly separated.
- Validate untrusted input.
- Enforce authorization on every protected operation.
- Measure performance before optimizing.
- Make background work resilient to retries and failures.
- Keep secrets out of source control.
- Test failure paths as well as successful paths.
- Design services to be observable and safely deployable.


## 23. Quick Reference Tables

### HTTP Methods

| Method | Typical purpose | Notes |
|---|---|---|
| GET | Read a resource | Should not change server state |
| POST | Create a resource or trigger an operation | Often not idempotent |
| PUT | Replace a resource | Intended to be idempotent |
| PATCH | Partially update a resource | Updates selected fields |
| DELETE | Delete a resource | Intended to be idempotent |

### Common HTTP Status Codes

| Status | Meaning | Typical use |
|---|---|---|
| 200 | OK | Successful request |
| 201 | Created | Resource created |
| 202 | Accepted | Work accepted for asynchronous processing |
| 204 | No Content | Success with no response body |
| 400 | Bad Request | Invalid request |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Authenticated caller lacks permission |
| 404 | Not Found | Resource does not exist or is not exposed |
| 409 | Conflict | Request conflicts with current state |
| 422 | Unprocessable Content | Request syntax is valid but content fails validation |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Unexpected server failure |
| 502 | Bad Gateway | Upstream server returned an invalid response |
| 503 | Service Unavailable | Service temporarily unable to handle the request |
| 504 | Gateway Timeout | Upstream request timed out |

### Authentication vs Authorization

| Concept | Question answered | Examples |
|---|---|---|
| Authentication | Who are you? | Password login, session, JWT validation, OAuth sign-in |
| Authorization | What are you allowed to do? | Roles, permissions, ownership checks, policy rules |

### Serialization Formats

| Format | Strengths | Considerations |
|---|---|---|
| JSON | Readable and widely supported by web APIs | Can be verbose; schema rules need to be defined separately |
| XML | Supports structured documents and established tooling | Often more verbose |
| Protocol Buffers | Compact binary format with schema definitions | Requires generated tooling and is not human-readable by default |

### Application Layers

| Layer | Main responsibility | Example |
|---|---|---|
| Controller / handler | HTTP request and response handling | Parse parameters and return status codes |
| Service / domain | Business rules and use cases | Check eligibility and calculate totals |
| Repository / data access | Persist and retrieve data | Execute database queries |
| Database | Durable data storage and constraints | Tables, indexes, transactions |

### Caching Strategies

| Strategy | Read behavior | Write behavior | Trade-off |
|---|---|---|---|
| Cache-aside | App checks cache, then database on miss | App updates database and usually invalidates cache | Flexible; app manages invalidation |
| Read-through | Cache loads missing data through a configured loader | Depends on implementation | Simplifies read path |
| Write-through | Cache and backing store are updated together | Writes pass through cache | Better consistency; may add write latency |
| Write-back | Writes go to cache first and backing store later | Store update is deferred | Fast writes but greater durability/consistency complexity |

### Cache Eviction Policies

| Policy | Evicts | Useful note |
|---|---|---|
| LRU | Least recently used item | Works well when recent use predicts future use |
| LFU | Least frequently used item | Favors items accessed often |
| FIFO | Oldest inserted item | Simple, but ignores how often an item is used |

### Logs, Metrics, Traces, and Monitoring

| Signal / practice | What it tells you | Example |
|---|---|---|
| Logs | What happened in an event | Error message with request ID |
| Metrics | How the system behaves over time | Request rate, error rate, CPU, latency percentiles |
| Traces | How one request moves across services | Time spent in gateway, API, and database |
| Monitoring | Whether signals indicate a problem | Alert when error rate exceeds a threshold |
| Observability | How well internal state can be understood from outputs | Combine logs, metrics, traces, and useful context |

### Concurrency vs Parallelism

| Concept | Meaning | Common fit |
|---|---|---|
| Concurrency | Multiple tasks make progress during overlapping periods | Managing many I/O-bound requests |
| Parallelism | Multiple tasks execute at the same time | CPU-heavy work that can be split across cores |

### Testing Types

| Test type | Scope | Example |
|---|---|---|
| Unit | One function or component | Validate a pricing calculation |
| Integration | Components working together | API endpoint with a test database |
| End-to-end | Complete user workflow | Sign in, create a record, retrieve it |
| Regression | Previously fixed behavior | Ensure a bug does not return |
| Load | Expected or elevated workload | Normal peak traffic simulation |
| Stress | Beyond expected capacity | Find failure limits and recovery behavior |
| Security | Vulnerabilities and access boundaries | Check authorization and injection defenses |

### Scaling Approaches

| Approach | How it works | Trade-off |
|---|---|---|
| Vertical scaling | Add CPU, memory, or other resources to one machine | Has hardware limits and may preserve a single bottleneck |
| Horizontal scaling | Add more application instances | Requires coordination around state, traffic, and shared resources |
| Caching | Reuse computed or retrieved data | Adds invalidation and staleness concerns |
| Queue-based processing | Move noncritical work out of the request path | Adds eventual completion and retry handling |

### Webhooks vs Polling

| Approach | How it works | Advantages | Trade-offs |
|---|---|---|---|
| Webhook | Provider sends an HTTP request when an event occurs | Prompt updates; avoids repeated checks | Endpoint must handle retries, duplicates, and signature verification |
| Polling | Client checks periodically for changes | Simple when no event mechanism exists | Can add delay and generate unnecessary requests |

### HTTP/1.1 vs HTTP/2 vs HTTP/3

| Version | Transport | High-level characteristic |
|---|---|---|
| HTTP/1.1 | TCP | Persistent connections; requests may be affected by connection-level limitations |
| HTTP/2 | TCP | Multiplexed streams and header compression |
| HTTP/3 | QUIC over UDP | Stream multiplexing with improved handling of packet loss between streams |

### Common Security Risks

| Risk | What can go wrong | Common defense |
|---|---|---|
| SQL injection | Untrusted input changes a database query | Parameterized queries and safe query APIs |
| XSS | Attacker-controlled script runs in a user's browser | Context-aware output encoding and suitable content policies |
| CSRF | Browser sends an unwanted authenticated request | CSRF tokens and appropriate cookie/site settings |
| Broken access control | User accesses another user's data or restricted action | Server-side permission and ownership checks |
| Credential leakage | Secrets are exposed in code, logs, or responses | Secret management and redaction |
| Abuse / brute force | Excessive requests overwhelm or compromise a service | Rate limits, throttling, monitoring, and suitable authentication controls |

## 24. Questions and Answers for Revision

### Fundamentals

**Q1. What does a backend system do?**  
It receives requests, applies application rules, accesses data or external services, and returns responses. A production backend must also consider security, reliability, performance, and maintainability.

**Q2. What is the difference between a path parameter and a query parameter?**  
A path parameter identifies a resource, such as `/users/42`. Query parameters commonly filter, sort, or paginate, such as `/users?role=admin&page=2`.

**Q3. What is the difference between PUT and PATCH?**  
PUT generally replaces the resource representation; PATCH applies a partial modification.

**Q4. What is the difference between 401 and 403?**  
401 indicates that valid authentication is missing or has failed. 403 indicates the caller is not permitted to perform the requested action.

**Q5. What is serialization?**  
Serialization converts application data into a transferable format such as JSON. Deserialization parses that representation back into application data.

### Application Design

**Q6. What is middleware?**  
Middleware is reusable request/response processing for tasks such as logging, authentication, validation, security headers, and error handling.

**Q7. Why separate controllers, services, and repositories?**  
It separates HTTP concerns, business rules, and data access, making code easier to test, change, and maintain.

**Q8. Where should business rules live?**  
Usually in a service or domain layer rather than being scattered across HTTP handlers or database-specific code.

**Q9. Why is server-side validation necessary if the frontend already validates input?**  
Clients can be bypassed or manipulated. The server must validate every untrusted request before processing or storing it.

**Q10. What is request context used for?**  
It can carry a request ID, authenticated identity, deadline, cancellation signal, and other small pieces of request-scoped information.

### Security

**Q11. What is the difference between authentication and authorization?**  
Authentication establishes identity; authorization checks the actions and resources that identity is permitted to access.

**Q12. What is the difference between a session and a JWT?**  
A session commonly stores state server-side and gives the client a session identifier. A JWT carries signed claims that a recipient can verify. JWTs still need expiry, revocation, storage, and authorization decisions to be designed carefully.

**Q13. Why should passwords be hashed rather than encrypted or stored as plain text?**  
Password hashes are designed to make recovery of the original password difficult. Use a dedicated password-hashing algorithm with a salt, not a general-purpose fast hash.

**Q14. How can SQL injection be prevented?**  
Use parameterized queries or safe ORM query APIs, validate inputs, and avoid constructing SQL by concatenating untrusted strings.

**Q15. Why is authorization required on every protected resource?**  
A user who is allowed to access one endpoint may not be allowed to access every record or operation. Verify permissions and ownership on the server for each request.

### Databases and Performance

**Q16. What is an index, and what is its trade-off?**  
An index can make lookups and sorting faster, but uses storage and adds overhead to inserts, updates, and deletes.

**Q17. What is the N+1 query problem?**  
An application makes one query to fetch a set of records and then issues an additional query for each record's related data. Eager loading, joins, or batching may reduce the extra queries.

**Q18. What is a database transaction?**  
A transaction groups operations into a unit that follows the database's transaction guarantees. ACID describes atomicity, consistency, isolation, and durability.

**Q19. Why use connection pooling?**  
It reuses database connections rather than repeatedly creating and closing them, reducing overhead. Pool sizes and timeouts must still be configured appropriately.

**Q20. What is cache invalidation?**  
It is the process of expiring or updating cached values when their source data changes. Incorrect invalidation can return stale or inconsistent results.

**Q21. How should an engineer optimize a slow endpoint?**  
Measure first; inspect latency breakdowns, database query plans, N+1 queries, payload sizes, external calls, and resource usage. Optimize the measured bottleneck and test again.

### Reliability and Distributed Work

**Q22. When should work be moved to a background queue?**  
When it is slow or not required to complete before the HTTP response, such as sending email or processing images. The API should communicate that work has been accepted where appropriate.

**Q23. Why are idempotency and retry handling important for jobs and webhooks?**  
A delivery may be repeated after a timeout or temporary failure. Idempotent processing prevents duplicate effects when the same operation is attempted more than once.

**Q24. What is graceful shutdown?**  
Stop accepting new work, allow in-flight requests or jobs to finish when possible, close connections and resources, and then exit.

**Q25. What is the difference between concurrency and parallelism?**  
Concurrency allows tasks to make overlapping progress; parallelism means tasks execute simultaneously.

**Q26. What are the three common pillars of observability?**  
Logs, metrics, and traces. Logs describe events, metrics quantify behavior, and traces show a request's path across components.

**Q27. What is the difference between a webhook and polling?**  
A webhook sends an event notification to a configured endpoint. Polling repeatedly asks a service whether anything changed.

### APIs, Testing, and Deployment

**Q28. Why use pagination?**  
It limits response size and database work, improves perceived performance, and helps APIs handle large collections. Cursor pagination can be useful for changing or large datasets.

**Q29. What is OpenAPI used for?**  
It defines an API contract including paths, operations, parameters, schemas, responses, and security requirements. Tools can use it for documentation and client generation.

**Q30. What is the difference between unit, integration, and end-to-end tests?**  
Unit tests check a small component; integration tests check collaborating components; end-to-end tests validate a complete workflow.

**Q31. What is the difference between CI and CD?**  
Continuous Integration frequently builds and tests changes. Continuous Delivery or Deployment automates the path from validated changes to release or production deployment.

**Q32. What are Docker and Kubernetes used for?**  
Docker packages an application and its dependencies into containers. Kubernetes manages containerized workloads, including deployment, recovery, and scaling.

**Q33. What is graceful degradation?**  
When a dependency or optional feature fails, the system preserves its essential functionality rather than failing completely.

**Q34. Why should secrets not be committed to a repository?**  
Repositories can be copied, indexed, or shared. Exposed secrets may grant unauthorized access and should be stored in appropriate secret-management systems.

## 25. Practical Review Checklist

- [ ] Explain the lifecycle of an HTTP request through the backend.
- [ ] Design a REST endpoint with validation and consistent errors.
- [ ] Explain authentication and resource-level authorization.
- [ ] Identify an N+1 query and propose a fix.
- [ ] Explain an index, a transaction, and connection pooling.
- [ ] Compare cache-aside with write-through caching.
- [ ] Design a retry-safe background job.
- [ ] Describe logs, metrics, traces, and a useful alert.
- [ ] Explain how to investigate a slow endpoint.
- [ ] Describe safe shutdown and dependency-failure handling.
- [ ] Compare unit, integration, and end-to-end tests.
- [ ] Explain how a change moves through CI/CD to deployment.
