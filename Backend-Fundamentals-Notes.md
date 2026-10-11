# Backend Fundamentals — Notes, Tables, and Examples

> Companion notes for the backend engineering roadmap. This file focuses on what a backend is, how requests travel through a deployed application, why backend services are needed, how frontends execute, and where trusted business logic belongs.
>
> **Scope:** This is a new standalone file. It does not replace or modify `Roadmap.md`.

## 1. What Is a Backend?

A **backend** is the server-side application that receives requests, executes application and business logic, manages data, enforces security, communicates with other services, and returns responses.

A server commonly communicates through HTTP/HTTPS, WebSockets, or gRPC. It may return JSON, HTML, images, JavaScript, files, or other content.

| Component | Responsibility | Example |
|---|---|---|
| Frontend / client | Displays UI and sends user actions | React page with a Login button |
| Backend application | Processes requests and enforces business rules | FastAPI endpoint checks login credentials |
| Database | Persists and queries application data | PostgreSQL stores users and orders |
| Cache | Stores frequently used data temporarily | Redis caches popular product details |
| Object storage | Stores large files | S3 stores uploaded images |
| External service | Provides functionality over an API | Email provider sends a password-reset email |
| Background worker | Processes work outside the request cycle | Celery worker generates an image embedding |

### Typical request-response flow

```text
Browser / Mobile App
        |
        | HTTPS request (method, URL, headers, body)
        v
Backend API
        |
        +----> Authentication and authorization
        |
        +----> Business logic and validation
        |
        +----> Database / Cache / External API
        |
        v
HTTP response (status code, headers, JSON/body)
        |
        v
Browser updates the UI
```

### Example: Login

1. The user enters an email and password in the frontend.
2. The frontend sends the credentials to the backend over HTTPS.
3. The backend validates the input and finds the user.
4. The backend verifies the password against its stored hash.
5. The backend establishes a session or issues an appropriate token.
6. The frontend receives the result and displays the next screen.

The frontend is responsible for the user experience. The backend is responsible for enforcing the authentication decision.

## 2. How Does a Deployed Backend Request Travel?

The backend deployed on AWS EC2. The example below shows one possible deployment path; real systems may use a CDN, load balancer, API gateway, container platform, or managed application service.

| Step | Component | What it does | Example / troubleshooting clue |
|---:|---|---|---|
| 1 | Browser or client | Creates an HTTP request | `GET https://api.example.com/users` |
| 2 | DNS | Resolves the hostname to address information | `api.example.com` resolves to an IP or another hostname |
| 3 | Network / firewall | Permits or blocks traffic according to rules | Inbound HTTPS traffic on port 443 is allowed |
| 4 | Cloud instance or load balancer | Receives traffic at the network destination | AWS EC2 instance or a load balancer |
| 5 | TLS termination | Establishes encrypted HTTPS communication | A valid TLS certificate is configured |
| 6 | Reverse proxy | Routes the request to an upstream service | Nginx forwards to `127.0.0.1:3001` |
| 7 | Backend application | Runs application code and creates a response | Node.js, Django, or FastAPI handler |
| 8 | Database / dependencies | Reads or writes data if required | PostgreSQL returns user records |
| 9 | Response path | Sends the status, headers, and body back | `200 OK` with JSON |

### DNS record types

| Record | Purpose | Example |
|---|---|---|
| A | Maps a hostname to an IPv4 address | `api.example.com → 203.0.113.10` |
| AAAA | Maps a hostname to an IPv6 address | Hostname maps to an IPv6 address |
| CNAME | Aliases one hostname to another hostname | `www.example.com → example.com` |

The IP addresses above are illustrative documentation addresses, not real deployment details.

### Common ports

| Port | Typical use | Security note |
|---:|---|---|
| 80 | HTTP | Commonly redirects to HTTPS |
| 443 | HTTPS | Public web traffic usually enters here |
| 22 | SSH | Restrict to trusted administrators or networks |
| 3000 / 3001 | Common development or application ports | Usually keep private behind a proxy or firewall |

A port number alone does not determine whether a service is secure. Access rules, authentication, TLS, and application configuration matter.

### Nginx reverse proxy example

```nginx
server {
    listen 443 ssl;
    server_name api.example.com;

    # Configure the appropriate TLS certificate and key.

    location / {
        proxy_pass http://127.0.0.1:3001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**How to read this configuration:**
- Nginx accepts HTTPS requests for `api.example.com`.
- The TLS certificate configuration enables HTTPS.
- Requests are forwarded to the backend listening on local port `3001`.
- Forwarded headers help the upstream application understand the original host, client address, and scheme when configured to trust the proxy correctly.

A reverse proxy can also support routing, load balancing, request-size limits, compression, and other cross-cutting concerns.

### Local development versus deployment

| Environment | Example URL | What happens |
|---|---|---|
| Local frontend | `http://localhost:3000` | Browser accesses a local development server |
| Local backend | `http://localhost:8000` | API runs on the developer's machine |
| Public frontend | `https://app.example.com` | DNS and public networking route to the deployed frontend |
| Public API | `https://api.example.com` | Requests reach the deployed API through its network path |

**Debugging order:** verify the URL and DNS resolution, network/firewall rules, TLS, reverse-proxy configuration, application health and logs, then database connectivity.

## 3. Why Do We Need Backends?

The backend centralizes trusted rules and shared data so different users and devices can interact consistently.

| Need | Why the backend helps | Example |
|---|---|---|
| Shared state | Users see consistent data | A post's like count is shared across users |
| Persistence | Data survives page refreshes and device changes | An order remains in order history |
| Authentication | Verifies identity | User signs in with email and password |
| Authorization | Enforces access permissions | Only an asset's owner can delete it |
| Business rules | Prevents clients from deciding trusted outcomes | Server calculates a final discount |
| Integrations | Protects credentials and coordinates services | Backend calls a private payment API |
| Notifications | Coordinates actions between users | Liking a post triggers a notification |
| Reliability | Provides controlled retries, logging, and error handling | Failed email delivery is retried |
| Scaling | Allows server capacity and workers to scale independently | More API instances handle peak traffic |

### Example: Like a social-media post

Suppose User A likes a post created by User B.

1. User A clicks **Like**.
2. The frontend sends `POST /api/posts/123/like`.
3. The backend identifies User A using the authenticated session or token.
4. The backend checks that the post exists and that the action is permitted.
5. The backend saves the like in the database.
6. The backend or a background worker triggers a notification to User B.
7. The frontend updates the displayed like state and count.

Example relational table:

| user_id | post_id |
|---:|---:|
| 101 | 123 |
| 102 | 123 |
| 101 | 456 |

A unique constraint on `(user_id, post_id)` can prevent duplicate likes by the same user on the same post.

Illustrative Django view:

```python
from django.http import JsonResponse
from django.shortcuts import get_object_or_404
from django.views.decorators.http import require_POST

@require_POST
def like_post(request, post_id):
    if not request.user.is_authenticated:
        return JsonResponse(
            {"error": "Authentication required"},
            status=401,
        )

    post = get_object_or_404(Post, id=post_id)
    Like.objects.get_or_create(user=request.user, post=post)

    return JsonResponse({"message": "Post liked"})
```

This is a teaching example. A production implementation also needs imports, database constraints, permission checks, appropriate CSRF handling for cookie-based authentication, and a notification strategy.

## 4. How Does the Frontend Work?

A browser fetches the resources needed for a page and executes frontend code in the browser environment.

| Technology | Responsibility | Example |
|---|---|---|
| HTML | Page structure | A form containing email and password fields |
| CSS | Visual presentation | Colors, layout, spacing, responsive styles |
| JavaScript | Interaction and client-side behavior | A click handler sends an API request |
| React | UI composition and state-driven rendering | A user list updates after an API response |
| Browser APIs | Client capabilities exposed by the browser | DOM, Fetch, local storage, selected file APIs |

### Typical browser loading sequence

1. The browser requests the page's HTML document.
2. The browser downloads referenced CSS, JavaScript, fonts, images, and other resources.
3. The browser parses HTML and CSS to build and style the page.
4. JavaScript executes and registers event handlers.
5. Framework code may hydrate server-rendered HTML or render the interactive UI.
6. User interactions trigger client-side behavior and, where necessary, API calls.

Rendering details vary by framework. For example, a Next.js application can execute some code on the server and other code in the browser.

### Example: Fetch users from React

```javascript
async function fetchUsers() {
  const response = await fetch("/api/users");

  if (!response.ok) {
    throw new Error("Failed to fetch users");
  }

  return response.json();
}
```

The browser executes this JavaScript. The Fetch API sends the HTTP request, the backend processes it, and the browser receives JSON that the UI can display.

## 5. Why Not Put All Backend Logic in the Frontend?

Some logic belongs in the frontend, but trusted business decisions and private operations must be enforced by the backend.

| Concern | Frontend limitation | Recommended approach | Example |
|---|---|---|---|
| Security | Users can inspect and modify client code or requests | Enforce permissions on the server | Check admin role in the API |
| Secrets | Browser-delivered code is visible to users | Keep private keys and credentials server-side | Backend calls an AI provider using a protected key |
| Database | Direct access exposes credentials and bypasses central controls | Access the database through a trusted service | API queries PostgreSQL |
| Business rules | Client-side checks can be bypassed | Revalidate rules on the server | Server calculates the final payable amount |
| Shared state | Separate devices have separate local state | Persist authoritative state centrally | Store orders in a database |
| External APIs | Browser requests can be restricted and often cannot safely hold secrets | Use a backend integration when private credentials or server-side control are needed | Backend calls a payment provider |
| Computing | Devices vary in CPU, memory, and battery | Move suitable heavy workloads to backend workers | Generate image embeddings asynchronously |
| Connection management | Browser clients are not trusted database connection managers | Use server-side drivers and connection pools | Django maintains a managed PostgreSQL pool |

### Example: Bonus calculation

A frontend might show a bonus preview:

```javascript
const estimatedBonus = salary * 0.20;
```

This is fine as a UI estimate, but it cannot be the authoritative result for a payroll operation.

A safer flow is:

1. The frontend submits the required inputs.
2. The backend validates the data and checks authorization.
3. The backend applies the official bonus rules.
4. The backend stores or returns the authoritative result.

**Rule of thumb:** client-side validation improves user experience; server-side validation and authorization provide enforcement.

## 6. Browser Sandbox, Same-Origin Policy, and CORS

| Concept | Meaning | Example |
|---|---|---|
| Browser sandbox | Restricts web code's access to the user's device and operating system | A website cannot freely read arbitrary local files |
| Same-origin policy | Restricts certain interactions between different origins | A page from one origin cannot freely read another origin's data |
| CORS | Lets a server specify which cross-origin browser requests may read its responses | API allows a trusted frontend origin |
| Authentication | Establishes identity | Session cookie or validated access token |
| Authorization | Checks allowed actions and resources | User can edit only permitted assets |

An **origin** is defined by scheme, hostname, and port. For example, `http://localhost:3000` and `http://localhost:8000` are different origins because their ports differ.

Example FastAPI CORS configuration:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["GET", "POST", "PATCH", "DELETE"],
    allow_headers=["Authorization", "Content-Type"],
)
```

Use the actual trusted frontend origins in each environment. When credentials are enabled, use explicit allowed origins rather than a wildcard.

**Important:** CORS is not authentication. A request may still reach a server even if browser JavaScript cannot read its response. Backend APIs must independently validate identity, permissions, and input.

## 7. Databases and Connection Pooling

Backend applications typically use database drivers or ORM libraries to communicate with databases.

A **connection pool** reuses a bounded set of database connections rather than opening and closing a connection for every operation.

```text
Incoming API requests
         |
         v
Backend application
         |
         v
  Connection pool
   |      |      |
 Conn 1 Conn 2 Conn 3
   |      |      |
   +------+------+
          |
          v
      PostgreSQL
```

| Without effective pooling | With a managed pool |
|---|---|
| Frequent connection setup and teardown | Connections can be reused |
| More connection overhead under load | Less setup overhead |
| Risk of too many concurrent connections if unmanaged | Connection count can be bounded |
| Resource usage can become unpredictable | Pool size and timeouts can be tuned |

Pooling does not make database capacity unlimited. Pool sizes must be considered across all application instances, and transactions should not hold connections longer than necessary.

## 8. Frontend vs Backend — Comparison Table

| Dimension | Frontend | Backend |
|---|---|---|
| Main role | User experience and client interactions | Trusted application behavior and shared resources |
| Typical runtime | Browser or mobile device | Server, container, or serverless runtime |
| Examples | React, HTML, CSS, JavaScript | Python, Django, FastAPI, Node.js |
| Data access | Usually through APIs | Through database drivers and service APIs |
| Trust model | Client input is untrusted | Must still validate every request; server code enforces rules |
| Secrets | Never embed private credentials in browser code | Can use protected environment and secret-management systems |
| State | UI state and client preferences | Shared persistent state and server-side session data |
| Scaling | Depends partly on client devices and content delivery | Scale instances, workers, databases, and caches as needed |
| Typical testing | Component and browser interaction tests | Unit, API, integration, security, and load tests |

## 9. End-to-End Example: Digital Asset Management with AI

Imagine a platform where users upload images, search assets, and generate AI descriptions.

| Step | Action | Component | Example |
|---:|---|---|---|
| 1 | User chooses an image | React frontend | Upload form |
| 2 | Upload request is sent | HTTP API | `POST /api/assets` |
| 3 | Identity, permissions, and file are validated | Backend | Check user access and size limits |
| 4 | File is stored | Object storage | S3 or Azure Blob Storage |
| 5 | Metadata is persisted | PostgreSQL | Filename, owner, tags, storage key |
| 6 | AI processing is queued | Task queue | Create embedding or generate description |
| 7 | Worker processes the job | Background worker / AI service | Generate vector representation |
| 8 | Results are saved | Database / vector index | Store description and searchable embedding |
| 9 | User searches for an image | Frontend and API | Search for “blue silk saree” |
| 10 | Matching assets are returned | Backend search layer | Metadata filters plus semantic similarity |

This illustrates that a backend does more than query a database: it coordinates permissions, file storage, business rules, asynchronous work, and AI integrations.

## 10. Troubleshooting Examples

| Symptom | Possible cause | First checks |
|---|---|---|
| Domain does not resolve | DNS record or resolver issue | Verify hostname and DNS records |
| Connection times out | Firewall, routing, or unavailable service | Check security-group rules, routing, and service health |
| HTTPS certificate error | Incorrect, expired, or mismatched certificate | Check certificate and hostname configuration |
| Nginx returns 502 | Upstream application unavailable or unreachable | Check backend process, port, and proxy configuration |
| API returns 401 | Missing or invalid authentication | Inspect session/token handling |
| API returns 403 | Authenticated user lacks permission, or policy blocks access | Verify server-side authorization and policies |
| Browser reports CORS error | Cross-origin response is not permitted for browser access | Check request origin and CORS response headers |
| API returns 500 | Unhandled server error or dependency failure | Inspect server logs and correlation/request ID |
| API is slow | Slow database query, dependency, or excessive work | Measure latency and inspect traces/queries |
| Database connection errors | Pool exhaustion, network issue, or bad credentials | Check pool metrics, database health, configuration, and limits |

Use logs and metrics to verify the actual cause rather than assuming the first likely explanation is correct.

## 11. Interview Questions and Short Answers

| Question | Short answer |
|---|---|
| What is a backend? | A server-side application that handles requests, business logic, security, data access, and integrations. |
| What happens when a browser calls an API? | The client resolves and connects to the destination, sends an HTTP request, and receives a response through the network and application stack. |
| What is the difference between a server and a database? | The server executes application logic; the database persists and queries data. |
| Why not connect React directly to PostgreSQL? | It exposes database access to untrusted clients and makes credentials, permissions, and connection management unsafe. |
| What is Nginx? | A web server that can serve static files and act as a reverse proxy, TLS termination point, or load balancer. |
| Authentication vs authorization? | Authentication establishes identity; authorization determines what that identity can do. |
| What is CORS? | A browser mechanism that controls cross-origin access to responses according to server policies. |
| Why use a connection pool? | To reuse connections, reduce setup overhead, and bound concurrent database connections. |
| Can frontend code contain business logic? | Yes, for UI behavior and previews; authoritative rules and protected decisions must be enforced on the server. |
| HTTP vs HTTPS? | HTTPS is HTTP protected by TLS, which encrypts traffic in transit and helps authenticate the server. |
| What is a reverse proxy? | A component that receives client requests and forwards them to upstream services. |
| Why use background jobs? | To move lengthy or retryable work, such as email delivery or AI processing, outside the main request-response path. |

## 12. Quick Revision — Key Takeaways

- The frontend handles the interface; the backend enforces trusted application behavior.
- DNS helps resolve hostnames; firewalls control network access; reverse proxies route requests.
- HTTPS protects data in transit using TLS.
- Backend APIs should validate input and enforce authentication and authorization.
- Databases persist data; connection pools help manage database connections efficiently.
- Browser sandboxing and CORS protect aspects of the browser environment but do not replace API security.
- Use background workers for suitable long-running tasks.
- Keep private credentials out of frontend code.
- Debug systematically across DNS, network access, TLS, proxy, application, and database layers.
- Test failure scenarios as well as successful request paths.

---

*These notes complement the backend roadmap and are intended for learning, revision, and interview preparation.*
