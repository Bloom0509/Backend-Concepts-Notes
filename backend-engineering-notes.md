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
