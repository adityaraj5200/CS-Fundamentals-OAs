## What is REST APIs and best practices to create one?

Good question. Let’s go step by step so that you can **answer in interviews confidently**.

---

## What is a REST API?

**REST** (Representational State Transfer) is an **architectural style** for building web services.
A **REST API** allows clients to interact with server resources using standard **HTTP methods** (`GET`, `POST`, `PUT`, `DELETE`, etc.).

Key principles:

1. **Client-Server** → clear separation of client (UI) and server (data/service).
2. **Statelessness** → each request contains all info (no server session).
3. **Cacheable** → responses can be cached to improve performance.
4. **Uniform Interface** → consistent resource representation (URIs, methods, JSON/XML).
5. **Layered System** → client doesn’t care if it talks directly to server or via proxies/CDN.
6. **Resources** → everything is treated as a resource, identified by a URI.

---

## **REST API Design Best Practices**

### 1. **Use Proper HTTP Methods**

* `GET /users` → fetch users
* `POST /users` → create user
* `PUT /users/{id}` → update entire user
* `PATCH /users/{id}` → partial update
* `DELETE /users/{id}` → delete user

---

### 2. **Meaningful & Consistent URIs**

* Use **nouns, not verbs**:
  ✅ `/users/123/orders`
  ❌ `/getUserOrders`

---

### 3. **Use Proper Status Codes**

* `200 OK` → success
* `201 Created` → resource created
* `204 No Content` → successful deletion
* `400 Bad Request` → invalid input
* `401 Unauthorized` → no authentication
* `403 Forbidden` → not allowed
* `404 Not Found` → resource doesn’t exist
* `500 Internal Server Error` → server failure

---

### 4. **Support Filtering, Sorting, Pagination**

* `/users?page=2&size=20`
* `/products?category=shoes&sort=price,asc`

---

### 5. **Use JSON for Request/Response (common standard)**

```json
{
  "id": 123,
  "name": "Aditya",
  "email": "aditya@example.com"
}
```

---

### 6. **Statelessness**

* Don’t store session state on the server.
* Use **JWT tokens** or API keys for authentication.

---

### 7. **Versioning**

* Add version in URL or headers:

  * `/api/v1/users`
  * `Accept: application/vnd.company.v1+json`

---

### 8. **Error Handling**

* Return clear error messages with a standard format:

```json
{
  "error": "Invalid Request",
  "details": "Email must be valid"
}
```

---

### 9. **Security**

* Always use **HTTPS**.
* Implement **authentication & authorization** (JWT, OAuth2).
* Prevent **SQL Injection, XSS, CSRF**.
* Apply **rate limiting** to prevent abuse.

---

### 10. **Scalability & Performance**

* Use **caching** (ETags, Redis, CDN).
* Apply **load balancing**.
* Ensure APIs are **idempotent** (safe to retry).
* Document with **Swagger/OpenAPI**.

---

✅ In interviews, you should **first explain what REST is**, then **list best practices**, and if asked deeper, you can expand on **statelessness, versioning, error handling, and security**.

---

## Can you tell how can we implement this kind of API?

```
* Asked thought process for problem statement:

Given different documents, and GET API, which returns us the documents which contains the given word (passed as a param) assume, billions of requests are made to the api.
```
---
### Answer:

Here’s how you can think about implementing such an API:

---

### **Problem restated**

* You have many documents.
* You need a **GET API** like `/search?word=foo` that returns all documents containing the word `foo`.
* Must handle **billions of requests** → scalability is the key challenge.

---

### **Step-by-step thought process**

1. **Data structure for fast lookup**

   * Naive: Scan all documents each time → too slow.
   * Optimized: Build an **Inverted Index** (mapping `word → list of documents`).

    * Example:

        ```
        "apple" → [doc1, doc5, doc9]
        "banana" → [doc2, doc3]
       ```
   * Store inverted index in a **search engine** like Elasticsearch / Solr, or a **key-value store** like Redis.

2. **API design**

   * Endpoint:

      ```
      GET /search?word=foo
      ```
    * Response:

      ```json
      {
        "word": "foo",
        "documents": [1, 5, 9]
      }
      ```

3. **Scalability considerations**

   * **Caching**:

     * Popular words (e.g., "the", "a") can be cached in Redis/CDN.
   * **Sharding**:

     * Split inverted index across multiple nodes (e.g., word hash mod N).
   * **Replication**:

     * Replicas for read-heavy load.
   * **Asynchronous updates**:

     * New documents update the index asynchronously for performance.

4. **Handling billions of requests**

   * **Load balancer** (NGINX, AWS ELB) to distribute requests.
   * **Horizontal scaling**: Add more API servers + index shards.
   * **Stateless API servers** so they can be scaled easily.
   * **Rate limiting** for abusive clients.

5. **Algorithm complexity**

   * Query time = `O(1)` (hash lookup for word) + `O(k)` (size of document list).
   * Much faster than scanning all docs (`O(N)` per request).

---

### **Implementation choices**

* **Simple stack**:

  * API: Spring Boot / Express.js
  * Storage: Elasticsearch (already optimized inverted index)
  * Cache: Redis
* **Lightweight custom**:

  * API: Flask/Java
  * Storage: Redis Hash (word → docIDs)

---

👉 If this was an **interview**, you’d highlight:

* First build inverted index.
* Use caching + sharding for scaling.
* Choose Elasticsearch if industrial-level search is needed.
* Stateless scalable REST API with load balancer.

---

**Write a sample Java Spring Boot REST API implementation for this with a mock in-memory inverted index:**

Here’s a **minimal Java Spring Boot REST API** implementation that uses an **in-memory inverted index**.

---

### **Code:**

```java
// Time Complexity: O(1 + k) per query (hash lookup + list size)
// Space Complexity: O(N * M) where N=docs, M=avg words per doc

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.*;

import java.util.*;

@SpringBootApplication
public class SearchApiApp {
    public static void main(String[] args) {
        SpringApplication.run(SearchApiApp.class, args);
    }
}

@RestController
@RequestMapping("/search")
class SearchController {

    private final InvertedIndex index = new InvertedIndex();

    public SearchController() {
        // Mock documents
        index.addDocument(1, "apple banana mango");
        index.addDocument(2, "banana orange");
        index.addDocument(3, "mango apple");
    }

    @GetMapping
    public Map<String,Object> search(@RequestParam String word) {
        List<Integer> docs = index.search(word.toLowerCase());
        Map<String,Object> response = new HashMap<>();
        response.put("word", word);
        response.put("documents", docs);
        return response;
    }
}

class InvertedIndex {
    private final Map<String,List<Integer>> index = new HashMap<>();

    public void addDocument(int docId, String content) {
        for(String word: content.toLowerCase().split("\\s+")) {
            index.computeIfAbsent(word,k->new ArrayList<>()).add(docId);
        }
    }

    public List<Integer> search(String word) {
        return index.getOrDefault(word, Collections.emptyList());
    }
}
```

---

### **How this works**

* `InvertedIndex` builds a map `word → list of docIds`.
* `/search?word=apple` returns all documents containing `apple`.
* Right now, data is **mocked in-memory** (3 docs).
* For **real-world scaling**, replace `InvertedIndex` with:

  * **Elasticsearch / Solr** for distributed inverted index.
  * **Redis / DB** for persistence + caching.

---

## Questions interviewers may ask:

Good — REST API interviews often go beyond definitions and test **how you think about design, scalability, and edge cases**.

Here’s a categorized list of **common REST API interview questions**:

---

## **1. Basics & Fundamentals**

* What is a REST API? How is it different from SOAP or GraphQL?
* What are the key principles of REST (statelessness, client-server, etc.)?
* Which HTTP methods are idempotent? Which are safe?
* Difference between PUT vs PATCH?

---

## **2. API Design & Implementation**

* Design a REST API for a **library system / e-commerce cart / blog platform**.
* How would you implement **pagination, filtering, sorting** in an API?
* How would you design a **search API** (like the one you just asked about)?
* What format would you use for request/response payloads? Why JSON?

---

## **3. Error Handling & Standards**

* How do you handle errors in REST APIs?
* Which status codes would you return for:
  * Invalid input?
  * Unauthorized access?
  * Resource not found?
* How do you design consistent error responses?

---

## **4. Performance & Scalability**

* How do you handle **billions of requests** to an API?
* How do you use **caching (ETag, Redis, CDN)** in REST APIs?
* How do you scale APIs horizontally?
* How would you optimize an API that fetches large datasets?

---

## **5. Security**

* How do you secure REST APIs?
* How do JWT tokens work? Why are they stateless?
* How do you prevent CSRF, XSS, SQL Injection in APIs?
* Difference between **authentication** and **authorization** in APIs?

---

## **6. Advanced / Thought-Provoking**

* Suppose your **GET API is very slow** (due to DB load) — how would you fix it?
* How would you design a REST API that **supports real-time updates**? (push vs pull)
* If an API **needs to return millions of records**, what design choices would you make?
* How do you handle **API versioning** when clients are on different versions?
* Should APIs return **entities** or **DTOs**? Pros/cons?

---

👉 In short, interviewers test:

* **Concepts** (definitions, HTTP methods, status codes).
* **Design thinking** (pagination, error handling, scalability).
* **Security awareness** (auth, tokens, HTTPS).
* **Scalability and optimization** (caching, DB load, rate limiting).

---

## Questions and answers:

### Q.1. Which HTTP methods are idempotent? Which are safe?

Here’s how to answer this in interviews:

---

### **Idempotent Methods**

A method is **idempotent** if calling it once or multiple times has the **same effect on the server**.

* **GET** → fetches a resource, no change.
* **PUT** → updates/replaces a resource (repeated calls = same final state).
* **DELETE** → deleting once or multiple times leaves resource gone.
* **HEAD** → fetches headers, no change.
* **OPTIONS** → describes communication options, no change.

👉 So: **GET, PUT, DELETE, HEAD, OPTIONS** are idempotent.
👉 **POST** is *not* idempotent (each call usually creates a new resource).

---

### **Safe Methods**

A method is **safe** if it does **not modify server state** (only reads).

* **GET** → safe.
* **HEAD** → safe.
* **OPTIONS** → safe.

👉 So: **GET, HEAD, OPTIONS** are safe.

---

### **Quick Interview Summary**

* **Safe methods**: GET, HEAD, OPTIONS (only read, no state change).
* **Idempotent methods**: GET, PUT, DELETE, HEAD, OPTIONS.
* **POST is neither safe nor idempotent**.

### Q.2. What are most common status codes used in REST APIs?

Yes — here’s a **ready-to-use list** of the most commonly used HTTP status codes in REST APIs (with meaning and usage).

---

## **Most Common REST API Status Codes**

### ✅ **2xx – Success**

* **200 OK** → Request succeeded, response contains data (GET, PUT, PATCH).
* **201 Created** → Resource successfully created (POST).
* **202 Accepted** → Request accepted for processing, but not completed yet (async).
* **204 No Content** → Success but no response body (DELETE, successful PUT).

---

### ❌ **4xx – Client Errors**

* **400 Bad Request** → Invalid request (missing/invalid params, bad JSON).
* **401 Unauthorized** → Authentication required or failed (invalid token/credentials).
* **403 Forbidden** → Authenticated but not allowed (no permission).
* **404 Not Found** → Requested resource doesn’t exist.
* **405 Method Not Allowed** → HTTP method not supported on this resource.
* **409 Conflict** → Resource conflict (e.g., duplicate entry, version mismatch).
* **422 Unprocessable Entity** → Request is well-formed but semantically invalid (e.g., email format invalid).
* **429 Too Many Requests** → Rate limit exceeded.

---

### 💥 **5xx – Server Errors**

* **500 Internal Server Error** → Generic server failure.
* **502 Bad Gateway** → Server received invalid response from upstream service.
* **503 Service Unavailable** → Service is down/overloaded (retry later).
* **504 Gateway Timeout** → Upstream service did not respond in time.

---

👉 In interviews, you can mention:

* **200, 201, 204, 400, 401, 403, 404, 409, 500** as the *most* common ones.
* Add **429** if talking about rate limiting at scale.

---

## Q.3. How do you handle **billions of requests** to an API?

"To handle billions of requests, I would design the API to be stateless and horizontally scalable behind a load balancer. I would use caching for frequent queries, optimize database access with indexes and replicas, and offload heavy processing to queues asynchronously. Additionally, I would implement rate limiting to prevent abuse and continuously monitor system metrics to scale automatically when needed."

---

## Q.4. How would you optimize an API that fetches large datasets?

"To optimize APIs fetching large datasets, I’d implement pagination or cursor-based queries, filtering, and field selection to minimize payload. I’d use compression and caching to reduce bandwidth and response time. For very large responses, I’d stream data or process requests asynchronously, and ensure database queries are efficient with proper indexes and batching."

## Q.5. Suppose your **GET API is very slow** (due to DB load) — how would you fix it?

If a **GET API is very slow due to DB load**, here’s how I would approach fixing it (step-by-step, interviewer-style):

---

### 1. **Identify the Root Cause**

* Check query execution plan → slow joins, missing indexes, table scans?
* Monitor DB metrics (CPU, I/O, connections).
* Profile API → confirm time is spent in DB, not network/serialization.

---

### 2. **Database Query Optimization**

* **Indexes**: Add composite or covering indexes for frequent query filters/sorts.
* **Query rewrite**: Avoid `SELECT *`, unnecessary joins, subqueries → replace with joins or CTEs.
* **Partitioning / Sharding**: If table is huge, partition by time (e.g., monthly data).
* **Denormalization**: Precompute expensive joins into summary tables if read-heavy.

---

### 3. **Reduce Load via API Strategy**

* **Pagination / Limit**: Don’t fetch millions of rows at once. Use `LIMIT/OFFSET`, keyset pagination, or cursor-based pagination.
* **Filtering**: Force clients to specify filters (date ranges, IDs).
* **Projection**: Return only required fields, not entire rows.

---

### 4. **Caching**

* **Application cache**: Store frequent results in Redis/Memcached.
* **DB cache**: Use query caching if DB supports it.
* **CDN caching**: For public GET APIs, cache at CDN level.

---

### 5. **Asynchronous / Precomputation**

* For very heavy aggregations, precompute results in background jobs and serve them via fast lookup.
* Example: materialized views (Postgres), summary tables updated via cron/job.

---

### 6. **DB Scaling**

* **Read replicas**: Send read-heavy GET queries to replicas.
* **Connection pooling**: Use a proper pool (HikariCP, etc.) to reduce connection overhead.
* **Sharding**: If dataset is too large, distribute across DB shards.

---

### 7. **API Layer Optimizations**

* **GZIP compression**: Reduce payload size.
* **Streaming**: For very large datasets, stream results instead of loading all into memory.
* **Batching**: Reduce round trips if multiple queries are needed.

---

✅ **Final Answer (interview-ready):**
If my GET API is slow due to DB load, I’d first profile and optimize queries with proper indexing and pagination. Then, I’d reduce DB stress using caching (Redis), read replicas, and precomputation (materialized views/summary tables). On the API side, I’d ensure we only fetch necessary fields, paginate results, and possibly stream data. If scale grows further, I’d shard/partition the DB. This ensures the API responds fast and remains scalable.

---


## Q.6. Write me some dummy/mock APIs using java, spring boot, JPA to create a book search system. 
Where you could search books through author name, book name and could filter books by their rating, number of pages, etc.

Here’s a **mock Book Search System** built with **Spring Boot + JPA**, showing realistic REST APIs for searching and filtering books.
Everything is dummy — perfect for practice or portfolio.

---

### 🧩 Features

* Search books by **book name** or **author name**
* Filter books by **rating**, **number of pages**, etc.
* Uses **Spring Data JPA** for query building
* Clean layered architecture (`Controller → Service → Repository → Entity`)

---

### 📁 Folder Structure

```
com.example.booksearch
 ├── controller
 │    └── BookController.java
 ├── service
 │    └── BookService.java
 ├── repository
 │    └── BookRepository.java
 ├── model
 │    └── Book.java
 └── BookSearchApplication.java
```

---

### 1️⃣ `Book.java`

```java
package com.example.booksearch.model;

import jakarta.persistence.*;

@Entity
@Table(name="books")
public class Book {
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String author;
    private double rating;
    private int pages;

    // Constructors
    public Book() {}
    public Book(String title, String author, double rating, int pages) {
        this.title=title;
        this.author=author;
        this.rating=rating;
        this.pages=pages;
    }

    // Getters/Setters
    public Long getId(){return id;}
    public String getTitle(){return title;}
    public void setTitle(String title){this.title=title;}
    public String getAuthor(){return author;}
    public void setAuthor(String author){this.author=author;}
    public double getRating(){return rating;}
    public void setRating(double rating){this.rating=rating;}
    public int getPages(){return pages;}
    public void setPages(int pages){this.pages=pages;}
}
```

---

### 2️⃣ `BookRepository.java`

```java
package com.example.booksearch.repository;

import com.example.booksearch.model.Book;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import java.util.List;

public interface BookRepository extends JpaRepository<Book,Long> {

    // Search by title or author (case-insensitive)
    @Query("SELECT b FROM Book b WHERE LOWER(b.title) LIKE LOWER(CONCAT('%',:query,'%')) " +
           "OR LOWER(b.author) LIKE LOWER(CONCAT('%',:query,'%'))")
    List<Book> searchByTitleOrAuthor(String query);

    // Filter by rating and page count
    List<Book> findByRatingGreaterThanEqualAndPagesLessThanEqual(double rating, int pages);
}
```

---

### 3️⃣ `BookService.java`

```java
package com.example.booksearch.service;

import com.example.booksearch.model.Book;
import com.example.booksearch.repository.BookRepository;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class BookService {
    private final BookRepository repo;

    public BookService(BookRepository repo){this.repo=repo;}

    public List<Book> searchBooks(String query){
        return repo.searchByTitleOrAuthor(query);
    }

    public List<Book> filterBooks(double minRating,int maxPages){
        return repo.findByRatingGreaterThanEqualAndPagesLessThanEqual(minRating,maxPages);
    }

    public Book addBook(Book book){
        return repo.save(book);
    }

    public List<Book> getAllBooks(){
        return repo.findAll();
    }
}
```

---

### 4️⃣ `BookController.java`

```java
package com.example.booksearch.controller;

import com.example.booksearch.model.Book;
import com.example.booksearch.service.BookService;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/books")
public class BookController {
    private final BookService service;

    public BookController(BookService service){this.service=service;}

    @PostMapping
    public Book addBook(@RequestBody Book book){
        return service.addBook(book);
    }

    @GetMapping
    public List<Book> getAllBooks(){
        return service.getAllBooks();
    }

    @GetMapping("/search")
    public List<Book> searchBooks(@RequestParam String q){
        return service.searchBooks(q);
    }

    @GetMapping("/filter")
    public List<Book> filterBooks(@RequestParam double minRating,@RequestParam int maxPages){
        return service.filterBooks(minRating,maxPages);
    }
}
```

---

### 5️⃣ `BookSearchApplication.java`

```java
package com.example.booksearch;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BookSearchApplication {
    public static void main(String[] args){
        SpringApplication.run(BookSearchApplication.class,args);
    }
}
```

---

### 🧪 Example API Calls

| Endpoint                                           | Description              | Example                                                                             |
| -------------------------------------------------- | ------------------------ | ----------------------------------------------------------------------------------- |
| `POST /api/books`                                  | Add a new book           | `{ "title": "Clean Code", "author": "Robert Martin", "rating": 4.8, "pages": 464 }` |
| `GET /api/books/search?q=martin`                   | Search by author/title   | —                                                                                   |
| `GET /api/books/filter?minRating=4.5&maxPages=500` | Filter by rating & pages | —                                                                                   |

---

Would you like me to extend this to include **pagination + sorting + specification-based filtering (JPA Criteria API)** for real-world search scalability?
