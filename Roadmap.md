# Backend Engineering — 31 Core Topics

## 1. HTTP Protocol
- HTTP defines communication between clients and servers.
- Common methods: `GET`, `POST`, `PUT`, `PATCH`, and `DELETE`.
- Understand status codes, headers, cookies, request/response bodies, and content types.
- HTTPS secures communication using TLS.
- Learn about CORS, connection handling, and HTTP caching.

## 2. Routing
- Routing maps a URL and HTTP method to the appropriate application logic.
- Path parameters identify resources; query parameters support filtering, sorting, and pagination.
- Route handlers should remain focused on request processing.
- Use consistent URL patterns and API versioning where appropriate.

## 3. Serialization and Deserialization
- Serialization converts application data into formats such as JSON.
- Deserialization converts incoming data into application objects or structures.
- Define clear request and response schemas.
- Handle malformed payloads and unsupported data types safely.
- Avoid exposing internal fields or sensitive information in API responses.

## 4. Authentication and Authorization
- Authentication verifies the identity of a user or service.
- Authorization determines which resources and operations that identity can access.
- Common mechanisms include sessions, cookies, JWT, API keys, and OAuth 2.0.
- Apply role-based or permission-based access control.
- Follow the principle of least privilege and protect credentials and tokens.

## 5. Validation and Transformation
- Syntactic validation checks whether input follows the expected format, such as a valid email address, phone number, or date.
- Semantic validation checks whether input makes sense according to business rules, such as preventing a future date of birth.
- Type validation ensures values match expected data types.
- Transformation normalizes or converts input into the required representation.
- Validate all untrusted input on the server before processing it.

## 6. Middleware
- Middleware executes shared logic during the request-response lifecycle.
- Common responsibilities include authentication, logging, validation, rate limiting, and error handling.
- Middleware can modify requests or responses, reject requests, or pass control to the next component.
- Execution order matters because middleware may depend on earlier processing.
- Keep middleware modular and focused on cross-cutting concerns.

## 7. Request Context
- Request context carries information associated with an individual request.
- It may contain request IDs, authenticated identity, deadlines, and cancellation signals.
- Context helps correlate logs across application layers and downstream services.
- Use context to propagate timeouts and cancellation where supported.
- Avoid storing unrelated or long-lived application state in request context.

## 8. Handlers, Controllers, and Services
- **Handler:** Receives the request and produces the response.
- **Controller:** Coordinates request processing and delegates work to application components.
- **Service:** Implements business rules and application use cases.
- **Repository:** Encapsulates database access and persistence operations.
- Separating responsibilities improves testability, maintainability, and reuse.

## 9. CRUD Deep Dive
- CRUD stands for Create, Read, Update, and Delete.
- Common HTTP mappings include `POST` for creation, `GET` for reading, `PUT` or `PATCH` for updates, and `DELETE` for deletion.
- Use appropriate status codes, including `201 Created`, `204 No Content`, `400 Bad Request`, and `404 Not Found`.
- Handle missing resources, duplicate records, invalid updates, and concurrent modifications.
- Use pagination for collection endpoints and make retry behavior safe where necessary.

## 10. RESTful Architecture and Best Practices
- REST organizes APIs around resources identified by URLs.
- Use nouns for resource paths, such as `/users` and `/orders`.
- Follow HTTP method semantics and return meaningful status codes.
- Design consistent error responses and support pagination, filtering, and sorting.
- Use idempotency where needed, particularly for operations that clients may retry.
- Document API contracts and introduce versioning when breaking changes require it.

## 11. Databases
- Understand relational database concepts, schema design, primary keys, foreign keys, constraints, and normalization.
- Learn SQL queries, joins, aggregations, subqueries, and window functions.
- Indexes improve many read operations but introduce storage and write overhead.
- Transactions help maintain data consistency; understand ACID properties and isolation levels.
- Use connection pooling, inspect slow queries, and prevent ORM-related N+1 query problems.

## 12. Business Logic Layer (BLL)
- The business logic layer implements application rules and domain-specific behavior.
- It coordinates validation, calculations, permissions, and interactions between components.
- Keep business rules separate from HTTP handling and database-specific code.
- Use transactions when a business operation requires multiple consistent data changes.
- Unit-test business rules independently from infrastructure where possible.

## 13. Caching
- Caching stores frequently accessed data to reduce latency and repeated computation.
- Common strategies include cache-aside, read-through, write-through, and write-back.
- TTL determines how long a cached entry remains valid.
- Cache invalidation, stale data, and concurrent updates are important design concerns.
- Redis is commonly used for distributed caching, counters, sessions, and rate limiting.
- Cache only when the performance benefit justifies the added complexity.

## 14. Transactional Emails
- Transactional emails are triggered by specific user actions or system events.
- Examples include account verification, password resets, receipts, and order notifications.
- Use an email provider or SMTP service to deliver messages.
- Send emails asynchronously when delivery should not delay the main request.
- Handle provider failures, retries, delivery status, and duplicate notifications.

## 15. Task Queuing and Scheduling
- Background jobs move long-running or nonessential work outside the HTTP request lifecycle.
- Common use cases include email delivery, report generation, image processing, and bulk operations.
- A queue connects producers with workers that consume and process jobs.
- Scheduling supports recurring, delayed, and time-based tasks.
- Implement retry policies, failure handling, monitoring, and idempotent job processing.
- Understand tools such as Celery, Redis queues, RabbitMQ, and Kafka, including their different purposes.

## 16. Elasticsearch
- Elasticsearch is a distributed search and analytics engine built around indexed documents.
- It supports full-text search, relevance scoring, filtering, and aggregations.
- Understand indexes, mappings, analyzers, shards, and replicas.
- Use appropriate mappings and analyzers to improve search quality.
- Consider indexing delays and synchronization between the primary database and search index.

## 17. Error Handling
- Handle errors at the appropriate application boundaries.
- Use centralized error handling to maintain consistent API responses.
- Distinguish validation errors, authentication failures, authorization failures, and internal errors.
- Return safe messages to clients while retaining useful diagnostic information in logs.
- Avoid swallowing exceptions silently or exposing stack traces and secrets.
- Use retries only for errors that are potentially recoverable.

## 18. Configuration Management
- Keep environment-specific settings separate from application code.
- Use environment variables or configuration files for ports, database URLs, feature flags, and service endpoints.
- Store secrets in a secure secret-management system rather than source control.
- Validate required configuration during application startup.
- Separate development, testing, staging, and production settings.
- Support controlled secret rotation and configuration changes.

## 19. Logging, Monitoring, and Observability
- **Logs:** Record events and application behavior.
- **Metrics:** Measure values such as latency, request rate, errors, and resource utilization.
- **Traces:** Follow requests across services and dependencies.
- Use structured logs, appropriate log levels, and request or correlation IDs.
- Monitor latency percentiles, error rates, throughput, and saturation.
- Configure alerts for actionable problems and avoid logging sensitive data.

## 20. Graceful Shutdown
- Stop accepting new requests or background jobs during shutdown.
- Allow in-flight operations to complete within a configured deadline.
- Stop workers and scheduled tasks safely.
- Close database connections, message consumers, and other resources.
- Handle operating-system termination signals and orchestrator shutdown timeouts.
- Make shutdown repeatable and safe, even when dependencies are unavailable.

## 21. Security
- Understand common vulnerabilities such as SQL injection, XSS, CSRF, broken access control, and insecure deserialization.
- Use parameterized queries and validate untrusted input.
- Enforce authorization for every protected resource and operation.
- Apply HTTPS, secure cookie settings, rate limiting, and appropriate security headers.
- Protect passwords, API keys, tokens, and other secrets.
- Use dependency scanning, least privilege, and defense in depth.

## 22. Scaling and Performance
- Measure system behavior before optimizing.
- Investigate slow queries, unnecessary network calls, large payloads, and excessive computation.
- Use caching, pagination, batching, compression, and connection pooling where appropriate.
- Vertical scaling increases the resources of one machine; horizontal scaling adds more instances.
- Design stateless services where practical to simplify horizontal scaling.
- Consider load balancing, timeouts, backpressure, and graceful degradation.

## 23. Concurrency and Parallelism
- Concurrency allows multiple tasks to make progress during overlapping periods.
- Parallelism means multiple tasks execute simultaneously.
- I/O-bound workloads often benefit from asynchronous operations.
- CPU-bound workloads may benefit from multiprocessing or other parallel execution strategies.
- Understand race conditions, locks, thread safety, deadlocks, and shared mutable state.
- Choose execution models based on workload characteristics and language runtime behavior.

## 24. Object Storage and Large Files
- Object storage is suitable for images, videos, documents, backups, and other large files.
- Common services include Amazon S3 and Azure Blob Storage.
- Use streaming or multipart uploads instead of loading entire files into application memory.
- Pre-signed URLs can allow clients to upload or download files directly.
- Validate file sizes and content, enforce access controls, and scan uploads when appropriate.
- Store file metadata and references in the database rather than storing large binary content there by default.

## 25. Real-Time Backend Systems
- Real-time systems deliver updates with low latency as events occur.
- WebSockets support persistent, bidirectional communication between clients and servers.
- Server-Sent Events (SSE) stream server-to-client updates over HTTP.
- Polling is simpler but may introduce additional requests and delays.
- Use message brokers or event-driven architecture when updates must reach multiple services or clients.
- Plan for authentication, reconnects, message ordering, and horizontal scaling.

## 26. Testing and Code Quality
- **Unit tests:** Verify individual functions or components.
- **Integration tests:** Verify interactions between components such as APIs and databases.
- **End-to-end tests:** Validate complete user workflows.
- Test successful paths, validation failures, edge cases, permissions, and dependency failures.
- Use mocks selectively and test real integrations where they matter.
- Automate formatting, linting, static analysis, and tests in CI pipelines.
- Prioritize meaningful assertions and maintainable tests over coverage percentage alone.

## 27. Twelve-Factor App
- The Twelve-Factor App methodology describes practices for building maintainable, portable, cloud-friendly applications.
- Keep the codebase under version control and explicitly declare dependencies.
- Store configuration outside the codebase.
- Treat backing services as attached resources.
- Separate build, release, and run stages.
- Design processes to be stateless and disposable.
- Support port binding, concurrency, logs as event streams, and administrative tasks.
- Use these principles to improve deployment consistency and operational reliability.

## 28. OpenAPI Standards
- OpenAPI provides a machine-readable description of HTTP APIs.
- Define paths, methods, parameters, request bodies, response schemas, and security requirements.
- Reuse schema components to reduce duplication.
- Use the specification to generate interactive documentation, clients, and server stubs.
- Keep the API contract aligned with implementation and tests.
- Review contract changes for backward compatibility.

## 29. Webhooks
- Webhooks send event notifications to an HTTP endpoint when something happens in another system.
- Examples include payment updates, repository events, and order status changes.
- Verify webhook signatures and reject unauthenticated requests.
- Handle duplicate deliveries through idempotency and event identifiers.
- Use fast acknowledgements and process lengthy work asynchronously.
- Implement retry handling, timeouts, observability, and replay protection.

## 30. DevOps for Backend Engineers
- **CI:** Automatically runs tests and quality checks when code changes.
- **CD:** Automates application delivery or deployment.
- Docker packages applications and their runtime dependencies into containers.
- Kubernetes manages container deployment, scaling, service discovery, and recovery.
- Understand Git workflows, Jenkins or GitHub Actions, and container registries.
- Learn health checks, environment management, rolling deployments, rollback strategies, and infrastructure as code.
- Monitor applications after deployment and ensure that releases can be rolled back safely.

## 31. Distributed Systems

- A distributed system consists of multiple independent services or computers that communicate over a network to perform a shared task.
- Understand service-to-service communication using REST APIs, gRPC, and message brokers.
- Learn synchronous and asynchronous communication patterns and when to use each.
- Handle network failures, timeouts, retries, duplicate messages, and partial failures.
- Understand consistency models, replication, partitioning, and distributed transactions.
- Use idempotency, circuit breakers, and bulkheads to improve resilience.
- Learn event-driven architecture, service discovery, and distributed tracing.
- Understand the CAP theorem and the trade-offs between consistency and availability during network partitions.
- Design services to be resilient, observable, and independently scalable.

---

## Key Backend Engineering Principles

- Keep HTTP handling, business logic, and data access responsibilities separate.
- Validate untrusted input and enforce authorization on every protected operation.
- Use transactions to preserve data consistency.
- Design background jobs and webhooks to handle retries and duplicate deliveries safely.
- Protect secrets and sensitive information.
- Measure performance before introducing optimizations.
- Test failure scenarios, not just successful requests.
- Make services observable, configurable, secure, and safely deployable.
