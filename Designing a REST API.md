# how to design a REST APIs? What aspects we should consider and take care of when doing so?

When you design REST APIs, you’re basically designing a **contract** between clients and your service. Good design = easy to use, stable, secure, and scalable.

I’ll break it down into the main aspects you should think about, with examples and interview-friendly phrasing.

---

## 1. Resource Modeling (What are your “things”?)

First step: think in **resources**, not actions.

* Identify resources: `User`, `Order`, `Product`, `Cart`, etc.
* Represent them with **nouns** in URLs, not verbs:

```text
GET    /users           # list users
POST   /users           # create user
GET    /users/{id}      # get a single user
PATCH  /users/{id}      # update part of a user
DELETE /users/{id}      # delete a user
```

Guidelines:

* Use **plural nouns**: `/users`, `/orders`
* Use **hierarchies** when there’s a clear parent-child relation:

  * `/users/{id}/orders` vs `/orders?userId={id}`
* Avoid verbs in paths: use actions via HTTP methods or subresources, not `/createUser` or `/getUserDetails`.

---

## 2. HTTP Methods & Semantics

Use methods as they’re intended:

* `GET`: Read (safe, no side effects)
* `POST`: Create / non-idempotent actions
* `PUT`: Replace entire resource (idempotent)
* `PATCH`: Partial update (idempotent by intention)
* `DELETE`: Delete resource

**Idempotency** matters:

* `PUT /users/123` with same payload multiple times → same result
* `POST /payments` multiple times → likely multiple charges (non-idempotent)
  → use idempotency keys if needed (`Idempotency-Key` header).

---

## 3. URI Design & Query Parameters

Keep URIs:

* **Simple & predictable**
* **Hierarchical**, not RPC-style

Use query params for:

* Filtering: `/orders?status=SHIPPED&userId=123`
* Pagination: `/orders?page=2&size=20`
* Sorting: `/orders?sort=createdAt,desc`
* Searching: `/products?search=iphone`

Avoid encoding actions in query: `/doSomething?action=createUser` ❌

---

## 4. Request & Response Body Design

Use consistent, predictable JSON structures.

### a) JSON structure

* Stick to **snake_case** or **camelCase** consistently.
* Avoid leaking internal field names (`db_id`, `internal_flag`)

Example:

```json
{
  "id": "123",
  "name": "Aditya Raj",
  "email": "aditya@example.com",
  "createdAt": "2025-12-02T10:15:30Z"
}
```

### b) Don’t overfetch/underfetch

* Avoid huge payloads — allow sparse fields if needed:
  `/users?fields=id,name,email`
* For heavy subresources, consider separate endpoints:
  `/users/{id}` vs `/users/{id}/preferences`

---

## 5. Status Codes & Error Handling

Use HTTP status codes correctly:

* `200 OK` – success with response body
* `201 Created` – resource created (return `Location` header)
* `204 No Content` – success without body (e.g., delete)
* `400 Bad Request` – invalid input
* `401 Unauthorized` – no/invalid auth
* `403 Forbidden` – authenticated but not allowed
* `404 Not Found` – resource doesn’t exist
* `409 Conflict` – version conflict, duplicate, etc.
* `422 Unprocessable Entity` – semantically invalid data
* `500 Internal Server Error` – unexpected error

### Error response format

Standardize an **error envelope**:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "One or more fields are invalid",
    "details": [
      { "field": "email", "message": "must be a valid email" },
      { "field": "password", "message": "must be at least 8 characters" }
    ],
    "traceId": "b12f…"
  }
}
```

This:

* Makes client handling easier
* Helps debugging (traceId) with logs and tracing.

---

## 6. Validation & Contracts

Treat your API as a **strict contract**:

* Validate request body:

  * Types (string, number, array)
  * Required fields vs optional
  * Length, format (email, phone, etc.)
* Reject invalid data early (`400/422`) with clear messages.
* Use **schema definitions** (OpenAPI/Swagger, JSON Schema) to document and validate.

API contracts should be:

* Documented
* Versioned
* Backward compatible as much as possible.

---

## 7. Versioning Strategy

You *will* need to change APIs. Plan for it.

Common patterns:

1. **URL versioning** (very common):

   * `/v1/users`, `/v2/users`
2. **Header-based versioning**:

   * `Accept: application/vnd.myapi.v1+json`

Best practices:

* Avoid breaking changes if possible.
* When needed:

  * Introduce `v2` and keep `v1` for some deprecation window.
  * Communicate deprecation clearly.

Examples of breaking changes:

* Removing fields
* Changing field type
* Changing semantics of existing behavior

Non-breaking changes:

* Adding optional fields
* Adding new endpoints

---

## 8. Pagination, Filtering, Sorting

For collections, never return “everything”.

### Pagination

Common styles:

**Offset-based:**

```http
GET /users?page=2&size=20
```

Response:

```json
{
  "data": [ ... users ... ],
  "page": 2,
  "size": 20,
  "totalPages": 10,
  "totalElements": 200
}
```

**Cursor-based** (better for large/real-time data):

```http
GET /transactions?limit=50&cursor=abc123
```

Response:

```json
{
  "data": [...],
  "nextCursor": "xyz987"
}
```

### Filtering & Sorting

Allow multiple filters and sorting:

```http
GET /orders?status=PLACED&from=2025-01-01&to=2025-02-01&sort=createdAt,desc
```

---

## 9. Security & Auth

Absolute non-negotiables:

* **Authentication** (who are you?)

  * JWT / OAuth2 / API keys, etc.
* **Authorization** (are you allowed?)

  * Role-based, permission-based, resource-level checks.
* **Transport security**

  * Enforce HTTPS only.
* **Input protection**

  * Avoid injection (SQL, NoSQL, headers)
  * Handle JSON parsing safely
* **Rate limiting & throttling**

  * Protect from abuse and DoS.
* **Sensitive data**

  * Never log passwords, tokens, or full card numbers.
  * Mask sensitive fields in logs and responses.

---

## 10. Performance & Scalability

Think about how your API will behave under load.

* **Caching**

  * Use `ETag`, `Last-Modified`, `Cache-Control` headers where applicable.
  * Cache static or rarely-changing resources.
* **Avoid N+1 queries**

  * Batch DB calls, use joins or bulk fetch.
* **Bulk operations**

  * `/users/bulk` with a list of operations instead of 1000 separate calls.
* **Compression**

  * Enable gzip / deflate at gateway/load balancer.
* **Timeouts and retries**

  * Timeouts for slow services.
  * Retry with backoff for transient failures (careful with idempotency).

---

## 11. Observability (Logs, Metrics, Tracing)

Make debugging production issues easier.

* **Correlation IDs / trace IDs**

  * Include in request headers (`X-Request-Id`) and propagate downstream.
* **Structured logging**

  * Log JSON with fields like `timestamp`, `level`, `service`, `traceId`, `errorCode`.
* **Metrics**

  * Count requests by endpoint, status, latency, errors.
* **Distributed tracing**

  * Trace request across microservices to debug latency and failures.

---

## 12. Documentation & Discoverability

Good APIs are self-explanatory and well documented.

* Use OpenAPI/Swagger to:

  * Define endpoints, schemas, examples
  * Auto-generate docs & client SDKs
* Maintain:

  * Endpoint descriptions
  * Request/response examples
  * Error codes and meaning
  * Auth requirements

For public APIs: also provide guides, tutorials, and changelogs.

---

## 13. Testing & Backward Compatibility

Treat API like a product, not just code.

* **Unit tests** for business logic.
* **Contract tests**:

  * Make sure you don’t break existing responses.
* **Integration tests / end-to-end tests** for critical flows.
* **Backward compatibility checks**:

  * Avoid silently breaking existing clients.
  * Use canary releases, feature flags, or dark launches where needed.

---

## 14. Consistency Across the Board

Most important: **consistency**.

* Same naming conventions
* Same error structure
* Similar pagination format across all resources
* Same auth style and headers

In an **interview-style summary**, you could say:

> When designing REST APIs, I start with resource modeling and clear, consistent URI design. I leverage HTTP methods and status codes correctly, ensure strong input validation and standardized error responses, and carefully design pagination, filtering, and sorting for collection endpoints. I also pay close attention to security (auth, authorization, HTTPS, rate limiting), performance (caching, avoiding N+1s, timeouts), and observability (structured logs, metrics, tracing). Finally, I treat the API as a versioned contract, maintain good documentation with OpenAPI, and ensure backward compatibility through testing and non-breaking changes.

# Design a REST API for a bookstore system where we can search books based on their author, book title, or book rating.

Below is a **clean, production-ready REST API design** for a **Bookstore Search System** that supports searching by **author, title, and rating**, along with pagination, sorting, and proper response standards.

---

# 1. Core Resource Model

### Resource: `Book`

```json
{
  "id": "UUID",
  "title": "Clean Code",
  "author": "Robert C. Martin",
  "rating": 4.6,
  "price": 499.00,
  "currency": "INR",
  "publishedYear": 2008,
  "genre": "Programming",
  "createdAt": "2025-12-02T10:15:30Z"
}
```

---

# 2. Base Endpoint

```http
GET /api/v1/books
```

This **single endpoint supports all search combinations** using query parameters.

---

# 3. Search by Individual Fields

## Search by Author

```http
GET /api/v1/books?author=Robert+C+Martin
```

## Search by Title (partial match)

```http
GET /api/v1/books?title=clean
```

## Search by Rating (greater than or equal)

```http
GET /api/v1/books?minRating=4.5
```

---

# 4. Combined Search (Most Realistic Case)

```http
GET /api/v1/books?author=Martin&title=clean&minRating=4.0
```

This performs:

```text
author LIKE '%Martin%'
AND title LIKE '%clean%'
AND rating >= 4.0
```

---

# 5. Pagination & Sorting

```http
GET /api/v1/books?author=Martin&minRating=4.0&page=1&size=10&sort=rating,desc
```

### Pagination Rules

| Parameter | Meaning                         |
| --------- | ------------------------------- |
| `page`    | Page number (1-based)           |
| `size`    | Items per page                  |
| `sort`    | `<field>,asc` or `<field>,desc` |

---

# 6. Successful Response Format (Standardized)

```json
{
  "data": [
    {
      "id": "b123",
      "title": "Clean Code",
      "author": "Robert C. Martin",
      "rating": 4.6,
      "price": 499.00
    }
  ],
  "pagination": {
    "page": 1,
    "size": 10,
    "totalElements": 125,
    "totalPages": 13
  }
}
```

---

# 7. Error Handling Design

## Invalid Query Parameter

```http
GET /api/v1/books?minRating=badValue
```

### Response → **400 Bad Request**

```json
{
  "error": {
    "code": "INVALID_QUERY_PARAM",
    "message": "minRating must be a number",
    "traceId": "abc123"
  }
}
```

---

## No Matching Books Found

```http
GET /api/v1/books?author=UnknownPerson
```

### Response → **200 OK with empty list**

```json
{
  "data": [],
  "pagination": {
    "page": 1,
    "size": 10,
    "totalElements": 0,
    "totalPages": 0
  }
}
```

---

# 8. Optional Advanced Search (POST for Complex Filters)

Use this when filters become large or dynamic.

```http
POST /api/v1/books/search
```

```json
{
  "title": "clean",
  "author": "martin",
  "minRating": 4.0,
  "maxPrice": 1000,
  "genres": ["Programming", "Software"],
  "page": 1,
  "size": 10,
  "sort": "rating,desc"
}
```

---

# 9. Database Indexing Strategy (Very Important for Performance)

To support fast searching:

```sql
CREATE INDEX idx_books_author ON books(author);
CREATE INDEX idx_books_title ON books(title);
CREATE INDEX idx_books_rating ON books(rating);
CREATE INDEX idx_books_author_rating ON books(author, rating);
```

For partial text search:

```sql
CREATE FULLTEXT INDEX idx_books_title_ft ON books(title);
```

---

# 10. Security Considerations

* Input sanitization for `title` and `author`
* Rate limiting on `/books` endpoint
* SQL Injection protection via prepared statements
* Public access → `GET` allowed
* Admin access → `POST/PUT/DELETE`

---

# 11. API Versioning

```http
/api/v1/books
/api/v2/books
```

Used when result format or behavior changes.

---

# 12. Interview-Ready Summary (What You Say)

> I designed the bookstore search API using a resource-oriented approach with `/books` as the core resource. Searching is achieved through query parameters for author, title, and rating, with support for pagination and sorting. I ensured proper HTTP semantics, standardized response and error handling, and designed indexes on searchable fields for optimal performance. For complex filtering, I exposed a POST-based search endpoint. The API is versioned, secured with validation and rate-limiting, and optimized for scalability.

---

# 13. Complete Endpoint Summary

| Purpose          | Method | Endpoint               |
| ---------------- | ------ | ---------------------- |
| Search Books     | GET    | `/api/v1/books`        |
| Advanced Search  | POST   | `/api/v1/books/search` |
| Pagination       | GET    | `?page=1&size=10`      |
| Sorting          | GET    | `?sort=rating,desc`    |
| Filter by Author | GET    | `?author=Martin`       |
| Filter by Title  | GET    | `?title=clean`         |
| Filter by Rating | GET    | `?minRating=4.0`       |

---

If you want, I can now also provide:

* **Controller + Service + Repository design (Spring Boot)**
* **SQL schema**
* **HLD + sequence diagram for search flow**
* **Caching design using Redis**
* **Rate-limited API Gateway design**
