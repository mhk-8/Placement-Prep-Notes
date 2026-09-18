# Microservices and API Design

---

## 1. Monolith vs microservices

### The honest comparison

| | Monolith | Microservices |
|---|---|---|
| Deployment | one unit | many, independent |
| Development speed (small team) | **faster** | slower — boilerplate per service |
| Development speed (large org) | slows as the codebase grows | **faster** — teams ship independently |
| Scaling | the whole app together | per service |
| Technology choice | one stack | per service |
| Refactoring across boundaries | easy — the compiler helps | **hard** — a coordinated multi-repo change |
| Transactions | local ACID | **distributed** — sagas |
| Debugging | one stack trace | distributed tracing required |
| Operational cost | low | **high** — CI/CD, monitoring, service mesh, on-call |
| Failure mode | all or nothing | partial, but with cascade risk |

**The correct default is a monolith.** Microservices solve an *organisational* problem — many teams blocking each other on one deployment — and they solve it by paying a substantial technical cost. A team of five shipping a new product should not pay it.

**Interview phrasing worth having:** "I'd start as a modular monolith with clear internal boundaries, and extract a service when a specific part needs independent scaling or is owned by a separate team. Extracting later is far easier than merging back."

**The modular monolith** is the underrated middle: one deployable, but with enforced internal module boundaries and no cross-module database access. It captures most of the design discipline of microservices with none of the network.

### When extraction is justified
- A component has a **genuinely different scaling profile** (video transcoding vs the API).
- A component has **different availability requirements** (payments must stay up when recommendations are down).
- A separate team owns it end to end and is blocked by the shared release train.
- It needs a different technology (an ML service in Python inside a Java shop).

**Not justified by:** "microservices are modern", or a diagram that looks tidier.

---

## 2. Service boundaries

Getting boundaries wrong is worse than not splitting at all, because you get the network costs *and* the coupling.

**Draw boundaries around business capabilities, not technical layers.** An "Orders" service owning order data, logic and API is right. A "Database service", a "Business logic service" and a "UI service" are a distributed monolith — every change touches all three.

**Each service owns its data.** No other service reads its tables directly. This is the rule that makes independent deployment real: if two services share a table, neither can change the schema alone, and you have a distributed monolith with extra latency.

**Signs the boundaries are wrong:**
- A typical feature requires changing three services together.
- Services share a database.
- One service cannot be deployed without deploying another.
- Chatty synchronous call chains (A calls B calls C calls D) in a single request path.

**Getting data across boundaries** when you cannot share tables:
- **API composition** — the caller queries several services and joins in memory. Simple, but N calls and the tail latency of the slowest.
- **CQRS with a read model** — a service maintains its own denormalised read copy, updated from events. Fast reads, eventually consistent.
- **Change data capture** — stream one service's changes so others can build local projections.

---

## 3. Inter-service communication

### Synchronous — REST, gRPC

**REST over HTTP/JSON.** Universal, human-readable, cacheable, trivially debuggable. Verbose on the wire and weakly typed.

**gRPC over HTTP/2 with protobuf.** Binary and compact, strongly typed with generated clients, supports streaming, and multiplexes over one connection. Harder to debug by hand and needs schema management. **Typical choice for internal service-to-service calls**, with REST at the public edge.

**GraphQL.** The client specifies exactly the fields it wants, so one round trip replaces several and over-fetching disappears — valuable for mobile. The costs are real: caching is harder (every query is different), and an unbounded query can be arbitrarily expensive, so you need query depth limits and cost-based rate limiting.

### Asynchronous — events

Services publish events; others subscribe. The publisher does not know the consumers, which is the strongest form of decoupling and the one that survives a consumer being down.

**Choosing between them:**

| Use synchronous when | Use asynchronous when |
|---|---|
| the caller needs the result to proceed | the work can happen later |
| the operation is a query | the operation is a notification |
| an immediate error must be returned | failures can be retried in the background |
| latency budget is tight and the call is fast | the downstream is slow or unreliable |

**A long synchronous chain is an availability multiplier.** Four services at 99.9% each in series give 99.6% — over a day of downtime a year, caused purely by the topology. This is the argument for making chains shallow and for moving links off the critical path.

---

## 4. The supporting infrastructure

**API gateway.** A single entry point handling authentication, rate limiting, routing, request validation, response aggregation and TLS termination. It stops every service reimplementing these, and gives clients one address and one contract.

**Service discovery.** Instances come and go, so addresses cannot be static. Either **client-side** (the client queries a registry and load-balances itself) or **server-side** (a load balancer or DNS name fronts the pool). The registry is typically Consul, etcd or a platform's built-in service.

**Service mesh.** A sidecar proxy beside each instance handling retries, timeouts, circuit breaking, mutual TLS, traffic splitting and telemetry — uniformly, without application code. Powerful, and a substantial operational commitment; it is rarely the right answer for a handful of services.

**Configuration service.** Centralised configuration and feature flags with dynamic updates, so behaviour can change without a deploy.

**Distributed tracing.** Without it, a slow request across six services is undebuggable. A trace id is propagated through every call so the whole path can be reassembled. **This is not optional in a microservice architecture** — it is the replacement for the stack trace you gave up.

---

## 5. API design

### REST conventions

```
GET    /v1/users              list      (paginated, filterable)
POST   /v1/users              create    → 201 + Location header
GET    /v1/users/{id}         read
PUT    /v1/users/{id}         replace   (idempotent)
PATCH  /v1/users/{id}         partial update
DELETE /v1/users/{id}         delete    (idempotent)
GET    /v1/users/{id}/orders  sub-resource
```

**Nouns not verbs.** `/users/123/orders`, never `/getUserOrders`. The HTTP method is the verb.

**Status codes that matter:** 200 OK, 201 Created, 202 Accepted (async), 204 No Content, 400 Bad Request, 401 Unauthenticated, 403 Unauthorised, 404 Not Found, 409 Conflict, 422 Unprocessable, 429 Rate Limited, 500 Internal, 503 Unavailable, 504 Gateway Timeout.

**401 vs 403** is worth getting right: 401 means "I don't know who you are"; 403 means "I know, and you may not".

### Pagination

| Style | Mechanism | Trade-off |
|---|---|---|
| **Offset** | `?page=3&limit=20` | simple; **drifts** when rows are inserted, and `OFFSET 100000` is slow |
| **Cursor / keyset** | `?after=eyJpZCI6MTIzfQ&limit=20` | stable and fast at any depth; no random page access |

**Cursor pagination is the correct default for large or changing datasets**, and being able to say *why* — offset skipping is O(offset) in the database, and concurrent inserts shift items across page boundaries so users see duplicates or miss rows — is the substance of the answer.

### Versioning

- **URL path** (`/v1/users`) — explicit, cacheable, easy to route. Most common.
- **Header** (`Accept: application/vnd.api.v2+json`) — cleaner URLs, harder to test by hand.
- **Query parameter** (`?version=2`) — simple, easy to forget.

**Prefer additive, backwards-compatible change over versioning.** Adding an optional field breaks nobody; removing or renaming one does. A new major version is a maintenance commitment — you now run both.

### Idempotency
Any operation that creates or charges should accept an **idempotency key**. The server records the key with its result; a repeat returns the stored result rather than re-executing. Without this, a client timeout followed by a retry double-charges — and client timeouts are routine.

### Other essentials
- **Consistent errors:** a machine-readable `code`, a human-readable `message`, and a `request_id` for correlation.
- **Filtering, sorting, field selection:** `?status=active&sort=-created_at&fields=id,name`.
- **Rate limit headers** on every response, not only on 429s.
- **HATEOAS** exists; almost nobody implements it, and that is fine.

---

## 6. Authentication and authorisation

**Authentication** is who you are; **authorisation** is what you may do. Keep the words straight.

| Mechanism | Fits | Note |
|---|---|---|
| API key | server-to-server, simple | no expiry unless you build it; rotate them |
| Session cookie | browser apps | needs CSRF protection; state on the server |
| **JWT** | stateless APIs, microservices | signed claims; **cannot be revoked before expiry** |
| OAuth 2.0 | third-party access | delegation, not authentication |
| OIDC | login with an identity provider | OAuth 2.0 plus an identity layer |
| mTLS | internal service-to-service | strong, operationally heavy |

**The JWT trade-off, stated properly:** it removes the session lookup, which is why it suits distributed systems — but it is **valid until it expires** regardless of what happens meanwhile. Logout and revocation therefore need either short expiry with refresh tokens, or a revocation list that reintroduces the very lookup you removed. Say this explicitly; "we'll use JWTs because they're stateless" without the caveat is a shallow answer.

---

## 7. Recall questions

1. Give the honest monolith/microservices comparison and state what problem microservices actually solve.
2. What is a modular monolith and why is it underrated?
3. Give four justifications for extracting a service, and two bad ones.
4. Why must each service own its data, and what is a distributed monolith?
5. List four signs that service boundaries are wrong.
6. Give three ways to get data across a service boundary and their trade-offs.
7. Compare REST, gRPC and GraphQL, including GraphQL's two costs.
8. Compute the availability of four 99.9% services in series and say what it implies.
9. What does an API gateway do, and what does a service mesh add?
10. Why is distributed tracing not optional in microservices?
11. Give the REST conventions for naming and the distinction between 401 and 403.
12. Why is cursor pagination preferred to offset, with both reasons?
13. Why prefer additive change to versioning?
14. What is an idempotency key and which failure does it prevent?
15. State the JWT trade-off precisely, including the revocation problem.
