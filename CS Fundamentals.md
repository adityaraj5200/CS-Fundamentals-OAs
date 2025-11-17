# OSI Model – 7 Layers

The **OSI (Open Systems Interconnection) Model** is a **conceptual framework** that standardizes how data is transmitted over a network by dividing the communication process into **7 layers**. Each layer serves a specific function and interacts only with its adjacent layers.

---

## 1. **Physical Layer**

* **Role**: Deals with transmission of raw bits (0s and 1s) over physical medium.
* **Key Functions**:

  * Defines **hardware characteristics**: cables, connectors, voltage levels, frequencies.
  * Data encoding: Converts binary data into signals (electrical, optical, or radio).
  * Data rate (bit rate) and synchronization of bits.
  * Defines topology (bus, star, ring).
  * Manages physical transmission mode (simplex, half-duplex, full-duplex).
* **Examples**: Ethernet cables (Cat5, Cat6), fiber optics, hubs, repeaters.
* **Interview Insight**: It doesn’t deal with meaning of data — only **how bits travel physically**.

---

## 2. **Data Link Layer**

* **Role**: Responsible for **node-to-node data transfer** (between two directly connected nodes).
* **Key Functions**:

  * **Framing**: Groups raw bits into **frames**.
  * **Error detection/correction**: CRC, checksums.
  * **Flow control**: Prevents fast sender from overwhelming slow receiver.
  * **MAC addressing**: Uses physical addresses (MAC) to identify devices.
  * **Medium Access Control**: Decides who can use the medium (e.g., CSMA/CD in Ethernet).
* **Sub-layers**:

  1. **LLC (Logical Link Control)** – error & flow control.
  2. **MAC (Media Access Control)** – controls how devices share medium.
* **Examples**: Ethernet (802.3), PPP, HDLC, ARP, switches, bridges.
* **Interview Insight**: Focuses on **frames** and **error detection**, not just bits.

---

## 3. **Network Layer**

* **Role**: Responsible for **source-to-destination delivery** across multiple networks.
* **Key Functions**:

  * Logical addressing: Assigns IP addresses.
  * Routing: Selects optimal path for data (routing algorithms, tables).
  * Packet forwarding, fragmentation & reassembly.
  * Congestion control.
* **Examples**: IP (IPv4, IPv6), ICMP, routers.
* **Interview Insight**:

  * Data unit here is **Packet**.
  * Makes communication possible between devices **not directly connected**.

---

## 4. **Transport Layer**

* **Role**: Ensures **end-to-end reliable delivery** of data between processes.
* **Key Functions**:

  * **Segmentation & reassembly** of data.
  * **Error detection & recovery** (acknowledgments, retransmissions).
  * **Flow control** (e.g., sliding window).
  * Multiplexing/demultiplexing: Differentiates multiple apps on same device (via ports).
* **Protocols**:

  * **TCP**: Reliable, connection-oriented (ack, retransmission).
  * **UDP**: Unreliable, connectionless, faster.
* **Examples**: TCP, UDP, SCTP.
* **Interview Insight**:

  * Data unit is **Segment (TCP)** or **Datagram (UDP)**.
  * TCP vs UDP is a favorite question.

---

## 5. **Session Layer**

* **Role**: Manages **sessions (dialogues)** between applications.
* **Key Functions**:

  * Establish, manage, and terminate sessions.
  * Synchronization (checkpoints, recovery after crash).
  * Dialog control (half-duplex or full-duplex).
* **Examples**: NetBIOS, RPC (Remote Procedure Call), SQL session, PPTP.
* **Interview Insight**: Often skipped in real-world stacks, but conceptually **maintains stateful communication**.

---

## 6. **Presentation Layer**

* **Role**: Ensures data is in a **usable and standardized format**.
* **Key Functions**:

  * Data translation: Converts between application and network formats.
  * Data compression: Reduces size for faster transmission.
  * Data encryption & decryption (security).
  * Character code translation (ASCII ↔ EBCDIC).
* **Examples**: SSL/TLS, JPEG, GIF, MPEG, ASCII, HTTPS encryption.
* **Interview Insight**: Think of it as the **translator** – makes sure sender’s data is understandable to receiver.

---

## 7. **Application Layer**

* **Role**: Closest to the end user. Provides **network services to applications**.
* **Key Functions**:

  * Interface for user applications to access network services.
  * Handles file transfers, email, web browsing, remote login.
* **Examples**: HTTP, HTTPS, FTP, SMTP, POP3, IMAP, DNS, Telnet, SNMP.
* **Interview Insight**: Only layer directly interacting with users. Data unit is **Message**.

---

# Summary Table

| Layer        | Data Unit        | Function                             | Examples           |
| ------------ | ---------------- | ------------------------------------ | ------------------ |
| Application  | Message          | Network services to apps             | HTTP, FTP, DNS     |
| Presentation | Message          | Translation, encryption, compression | SSL/TLS, JPEG      |
| Session      | Message          | Session management                   | NetBIOS, RPC       |
| Transport    | Segment/Datagram | Reliable end-to-end delivery         | TCP, UDP           |
| Network      | Packet           | Routing, logical addressing          | IP, ICMP, routers  |
| Data Link    | Frame            | Error detection, MAC addressing      | Ethernet, switches |
| Physical     | Bits             | Transmission of raw bits             | Cables, hubs       |


---

# OSI Model – 1 Minute Revision

1. **Physical** – Transmits raw **bits** via medium (cables, signals).
2. **Data Link** – Converts bits to **frames**, handles **MAC addressing**, error detection, flow control.
3. **Network** – **Packets**, logical addressing (**IP**), **routing** across networks.
4. **Transport** – **Segments/Datagrams**, ensures reliable delivery (**TCP**) or fast delivery (**UDP**), ports for multiplexing.
5. **Session** – Manages **sessions** (setup, sync, termination) between apps.
6. **Presentation** – **Format translator**, encryption, compression.
7. **Application** – User-facing services (**HTTP, FTP, DNS, Email**).

**Data units**: Bits → Frames → Packets → Segments → Messages.

---

### Process vs thread

A `process` is an instance of a running program with its own independent memory space, while a `thread` is the smallest unit of execution within a process and shares the process's memory space with other threads.

Processes are heavyweight, requiring more time to create and switch between, but offer better fault isolation. Threads are lightweight, enabling faster creation and context switching, which improves performance through parallelism, but an error in one thread can crash the entire process.

# Operating System

### Q.1. What is synchronization in Operating system?

Synchronization in an Operating System refers to the coordination of processes or threads to ensure that shared resources (like memory, files, or variables) are accessed safely and correctly when multiple threads or processes run concurrently.

#### Goal of Synchronization:

* Ensure mutual exclusion: only one process/thread accesses a critical section at a time.
* Maintain data consistency and correct program behavior.
* Coordinate order of execution when certain operations must happen before others.

## Q.2. What's Deadlock?

A **Deadlock** in an Operating System occurs when **two or more processes are waiting indefinitely for each other** to release resources — and as a result, **none of them can proceed**.

---

#### Necessary Conditions for Deadlock (Coffman Conditions)

A deadlock can occur **only if all four conditions hold simultaneously**:

1. **Mutual Exclusion** – At least one resource is non-shareable.
2. **Hold and Wait** – A process is holding at least one resource and waiting for others.
3. **No Preemption** – Resources cannot be forcibly taken away; they must be released voluntarily.
4. **Circular Wait** – A circular chain of processes exists, each waiting for a resource held by the next.

---

#### Practical Avoidance in Real Systems

In real-world programming (like Java or C++ multithreading):
* Always acquire locks in a fixed order.
* Use timeout locks (e.g., tryLock(timeout) in Java).
* Avoid nested locking when possible.
* Minimize the scope of synchronized blocks.

## Q.1. What is the difference between process and thread?



## Q.1. What is the difference between mutex vs semaphore?
A mutex (mutual exclusion) is a synchronization primitive that acts as a lock, ensuring that only one thread can access a shared resource at a time, with an ownership concept where the thread that locks it must also unlock it.

A semaphore, in contrast, is a signaling mechanism using a counter to control access to a pool of resources, allowing a specified number of threads to access the resource and potentially allowing any thread to signal its availability.

Mutexes are ideal for exclusive resource access, while semaphores are suited for managing limited, shared resources.


## Q.2. What is context switching and when does it occur?

**Context Switching:**
The process of saving the state (CPU registers, program counter, etc.) of a running process/thread and loading the state of another so the CPU can switch execution.

**Occurs:**

* During **multitasking** when the OS switches between processes or threads.
* On **interrupts**, **I/O requests**, or **preemptive scheduling**.


## Q.3. Explain paging and segmentation
## Q.4. What is a page fault?
## Q.5. What is a deadlock?
## Q.6. What are the necessary conditions for deadlock?
## Q.7. How can deadlocks be prevented or avoided?
## Q.8. What is process synchronization and why is it needed?
## Q.9. Explain the difference between preemptive and non-preemptive scheduling
## Q.10. What are different CPU scheduling algorithms?
## Q.11. What is a critical section problem?
## Q.12. What is thrashing in OS?
## Q.13. Explain virtual memory
## Q.14. What is a semaphore and how is it used?
## Q.15. What are system calls?


---

## Q. HTTP vs HTTPS?

**HTTP vs HTTPS (Interview Answer):**

* **Definition:**
  HTTP (HyperText Transfer Protocol) is used for data communication over the web. HTTPS (HTTP Secure) is the secure version of HTTP that uses encryption.

* **Security:**
  HTTP transmits data in plain text; HTTPS encrypts data using **SSL/TLS**, preventing eavesdropping and tampering.

* **Port:**
  HTTP uses **port 80**, while HTTPS uses **port 443**.

* **Certificate:**
  HTTPS requires an **SSL/TLS certificate** issued by a Certificate Authority (CA) to verify the website’s authenticity.

* **Performance:**
  HTTPS adds slight overhead due to encryption but is optimized with modern hardware.

* **Usage:**
  HTTP is used for non-sensitive data; HTTPS is used for **secure communication** (banking, payments, login forms).

**In short:** HTTPS = HTTP + Encryption + Authentication + Data Integrity.


### What happens when you enter a url in a browser?

When you enter a URL, your browser performs a DNS lookup to find the website's IP address, establishes a TCP connection to that server, sends an HTTP request for the page, receives the HTTP response with the webpage's content, and then renders the HTML, CSS, and JavaScript to display the fully loaded page.

Here's a step-by-step breakdown: 

1. Enter URL and DNS Lookup 
	* You type a URL (like www.google.com) into the browser. [3]  
	* The browser first checks its cache for the IP address. [3]  
	* If not found, it performs a Domain Name System (DNS) lookup to find the corresponding IP address, which is the numerical address of the server hosting the website. [1, 4]  

2. Establish a TCP Connection 
	* Once the IP address is known, the browser initiates a Transmission Control Protocol (TCP) connection with the server at that IP address. [2, 5]  
	* This connection is established through a three-way handshake, ensuring both the client and server are ready to communicate. [5, 6]  

3. Send an HTTP Request 
	* The browser sends an HTTP (or HTTPS, if encrypted) request to the server. [1, 7]  
	* This request includes details about the resource you want to access. [7]  

4. Server Processes Request and Sends a Response 
	* The web server receives the request, processes it, and prepares an HTTP response. [2, 8]  
	* This response contains the webpage's HTML content, along with information in its headers. [1, 8]  

5. Render the Webpage 
	* The browser receives the HTTP response and begins to render the HTML, turning the code into a visible page. [2, 9]  
	* It then sends additional requests for embedded resources like images, CSS (for styling), and JavaScript (for interactivity), repeating the request/response process for these items. [1, 9]  
	* The browser combines these elements to display the fully loaded, interactive webpage. [9, 10]  


### How TCP connection is made?

**TCP Connection Establishment (3-Way Handshake):**

1. **SYN:**

    * Client sends a **SYN** (synchronize) packet to the server with an initial sequence number `x`.

2. **SYN-ACK:**

    * Server replies with **SYN + ACK** packet, acknowledging client’s `x` and sending its own sequence number `y`.

3. **ACK:**

   * Client sends an **ACK** packet back, acknowledging server’s `y`.

After this exchange, the connection is **established**, and both sides can start data transmission.

**In short:**
TCP handshake = **SYN → SYN-ACK → ACK** ensures both client and server agree on initial sequence numbers and are ready to communicate reliably.


### How TLS connection is made?

**TLS Connection Establishment (Simplified Handshake):**

1. **Client Hello:**

   * Client sends supported TLS versions, cipher suites, and a random number.

2. **Server Hello:**

   * Server selects version & cipher suite, sends its certificate (with public key), and a random number.

3. **Certificate Verification:**

   * Client verifies the server’s certificate using a trusted **CA (Certificate Authority)**.

4. **Key Exchange:**

   * Client creates a **pre-master secret**, encrypts it with server’s public key, and sends it.
   * Both compute the same **session key** (symmetric) from this secret + randoms.

5. **Finished Messages:**

   * Both sides send encrypted “Finished” messages to confirm key setup.

6. **Secure Data Transfer:**

   * Subsequent data is encrypted with the shared session key.

**In short:**
TLS uses **asymmetric encryption** during handshake for key exchange, then switches to **symmetric encryption** for fast, secure communication.

---

# DBMS

**Top DBMS (Database Management System) Interview Questions:**

## Q.1. What’s the difference between **DBMS and RDBMS**?

DBMS (Database Management System) 

* Definition: A software system that allows users to create, store, modify, and retrieve data from a database. [1, 3]  
* Data Storage: Can store data in various formats, including files, and does not necessarily establish relationships between the data. [5, 6]  
* Data Redundancy: Often results in data redundancy (repetition of data) because there's no strong mechanism to prevent it. [6, 7]  
* Security: Generally offers lower security features. [6]  
* Users: Typically supports single users. [6, 8]  
* Examples: XML, Windows Registry, and file systems can be considered types of DBMS. [6, 9]  

RDBMS (Relational Database Management System) 

* Definition: A specialized type of DBMS that stores data in structured tables with rows and columns, and uses relationships between these tables to organize information. [3, 5]  
* Data Storage: Data is stored in tables with predefined relationships, enabling a more organized and structured approach. [2, 3]  
* Data Redundancy: Eliminates data redundancy by using keys and indexes to maintain consistency. [7, 8]  
* Data Integrity: Enforces data integrity and consistency through normalization and constraints. [2, 3, 7]  
* Security: Provides robust security features, user roles, and granular control over data access through SQL. [10]  
* Users: Supports multiple users and is designed for client-server architecture. [8]  
* Examples: Popular examples include SQL, MySQL, PostgreSQL, and Oracle. [1, 6]  


## Q.2. Explain **Normalization** and why it’s needed.

`Normalization` is the process of reducing data redundancy in a table and improving data integrity. Data normalization is a technique used in databases to organize data efficiently. 
Data normalization ensures that your data remains clean, consistent, and error-free by breaking it into smaller tables and linking them through relationships. This process reduces redundancy, improves data integrity, and optimizes database performance. 

## Q.4. What’s the difference between **Primary Key, Candidate Key, and Foreign Key**?

Candidate Key

* Definition: A minimal set of attributes that can uniquely identify each row in a table. 
* Characteristics: 
	* Must be unique and non-null. 
	* A table can have multiple candidate keys. 
	* They serve as potential primary keys. 

Primary Key 

* Definition: A single candidate key that is chosen to uniquely identify each record (row) in a table. [1, 2]  
* Characteristics: 
	* Must be unique and cannot contain null values. [1, 5]  
	* A table can only have one primary key. [5, 6]  
	* It guarantees the existence of a unique identifier for each entity in the table. [7, 8]  

Foreign Key 

* Definition: An attribute or set of attributes in one table that refers to the primary key in another table. [1, 3, 4]  
* Purpose: 
	* Establishes relationships between tables. [1, 2]  
	* Ensures referential integrity, meaning that values in the child table's foreign key must exist in the parent table's primary key. [1, 2]  
	* Helps maintain data consistency and accuracy across the database. [9]  

## Q.5. What is **ACID property** in transactions?

ACID properties (Atomicity, Consistency, Isolation, Durability) are a set of guarantees for reliable database transactions, ensuring data remains accurate, complete, and consistent even with system failures or concurrent access. Atomicity ensures a transaction is an all-or-nothing operation, Consistency maintains the database's integrity rules, Isolation prevents concurrent transactions from interfering, and Durability guarantees committed data persists even after system restarts or crashes.   
Here's a breakdown of each property: 

* Atomicity 

  * What it means: A transaction is treated as a single, indivisible unit of work. 
  * Guarantee: Either all operations within a transaction are successfully executed, or none of them are. If a transaction fails halfway through, the database rolls back to its original state, as if the transaction never happened. 
  * Example: A bank transfer where money is deducted from one account and added to another must do both; if only the deduction occurs and the system crashes, the transaction must be undone entirely. 

* Consistency 

  * What it means: Ensures that each transaction brings the database from one valid state to another, maintaining data integrity. 
  * Guarantee: A transaction cannot violate any of the database's predefined rules, constraints, or relationships. The database's integrity is preserved before and after any transaction.
  * Example: If a transaction involves updating account balances, the sum of all account balances must remain constant before and after the transaction, preventing an unaccounted-for balance. 

* Isolation 

  * What it means: When multiple transactions are running concurrently, each transaction should execute as if it were the only one in the system. 
  * Guarantee: Prevents transactions from interfering with each other, ensuring that the outcome of concurrent transactions is the same as if they were executed sequentially. 
  * Example: If two users try to book the last available seat on a train simultaneously, isolation ensures that only one transaction is successful, preventing both from booking the same seat and ensuring a consistent state. 

* Durability 

  * What it means: Once a transaction has been successfully completed and committed, its changes are permanent and will not be lost. 
  * Guarantee: The database reliably stores committed changes even in the event of a power failure, system crash, or other hardware failures. 
  * Example: After a transaction successfully transfers money and its changes are committed, those changes are guaranteed to be present in the database, even if the server reboots immediately after. 


## Q.6. What’s the difference between **DELETE, TRUNCATE, and DROP**?
## Q.7. Explain **JOINs** and list their types
## Q.8. What is an **Index** and when can it **degrade performance**?

**Index:**
An index is a data structure (usually a **B-tree** or **hash**) used by a database to speed up data retrieval operations. It works like an index in a book — instead of scanning every row, the DB uses the index to directly locate the required records.

**When it degrades performance:**

1. **Frequent INSERT/UPDATE/DELETE:** Every change in the table must also update the index → adds overhead.
2. **Too many indexes:** Slows down write-heavy workloads because each modification affects multiple indexes.
3. **Small tables:** Full table scans can be faster than using an index due to low lookup cost.
4. **Low selectivity columns:** Index on columns with many duplicate values (like gender) provides little benefit but still adds maintenance cost.
5. **Fragmentation:** Over time, indexes can become fragmented, increasing I/O and reducing efficiency.

## Q.9. What’s the difference between **Clustered and Non-Clustered Index**?
## Q.10. What are **Views** and why are they used?
## Q.11. What’s **Normalization vs Denormalization**?

**Normalization:**
A process of organizing data into multiple related tables to **reduce redundancy** and **ensure data integrity**.

* Example: Split “Customer” and “Orders” into two tables linked by `customer_id`.
* **Pros:** Less redundancy, easier updates, better data consistency.
* **Cons:** Requires joins → slower read queries in large systems.

**Denormalization:**
The opposite — **combining tables** to improve **read performance** by reducing joins.

* Example: Store customer name directly in the “Orders” table.
* **Pros:** Faster reads, fewer joins.
* **Cons:** Data redundancy, harder updates (need to update in multiple places).

**Use:**

* Normalization → preferred for **transactional (OLTP)** systems.
* Denormalization → preferred for **analytical/reporting (OLAP)** systems.

## Q.12. Explain **Transaction Isolation Levels** and their trade-offs
## Q.13. What are **Deadlocks in DBMS** and how can you prevent them?
## Q.14. What is **Referential Integrity**?
## Q.15. What’s the difference between **OLTP** and **OLAP** systems?
## Q.16. Explain **Entity, Attribute, Relationship** in ER modeling
## Q.17. What is **SQL Injection** and how can it be prevented?

**SQL Injection:**
A web security vulnerability where attackers inject malicious SQL queries through user inputs to access or modify database data.
Example:

```sql
SELECT * FROM users WHERE username=' ' OR '1'='1';
```

This returns all rows, bypassing authentication.

**Prevention:**

1. **Use Prepared Statements / Parameterized Queries** – e.g. `PreparedStatement` in Java.
2. **Use ORM frameworks** – like Hibernate or JPA.
3. **Validate and sanitize inputs** – restrict special characters.
4. **Use least-privilege DB accounts** – restrict DB user permissions.
5. **Keep DB error messages generic** – avoid leaking SQL structure.


## Q.num. Teach me about CS fundamentals like DRY, KISS, SOLID, ACID?

Here’s a concise breakdown of those key **Computer Science fundamentals** you mentioned — useful for interviews and daily engineering work:

---

### 🧩 **1. DRY — Don’t Repeat Yourself**

**Meaning:** Avoid duplicating logic or code.
**Goal:** Reuse code via abstraction (functions, classes, modules).
**Example:**
❌ Bad:

```java
int areaSquare(int s){return s*s;}
int areaRectangle(int l,int b){return l*b;}
```

✅ Good:

```java
int area(int l,int b){return l*b;}
```

**Benefit:** Easier maintenance, less chance of bugs.

---

### 🧠 **2. KISS — Keep It Simple, Stupid**

**Meaning:** Prefer simple, clear, and readable solutions over clever or overengineered ones.
**Goal:** Code should be easy to read, debug, and extend.
**Example:**
❌ Bad (overcomplicated loop):

```java
for(int i=0;i<arr.length;i++) if(arr[i]%2==0) result.add(arr[i]);
```

✅ Good:

```java
for(int num:arr){if(num%2==0) result.add(num);}
```

**Benefit:** Reduces complexity and errors.

---

### 🧱 **3. SOLID Principles** *(Object-Oriented Design)*

Used to write maintainable and scalable OOP systems.

| Principle                     | Meaning                                          | Short Example                                                  |
| ----------------------------- | ------------------------------------------------ | -------------------------------------------------------------- |
| **S – Single Responsibility** | A class should have only one reason to change.   | `InvoicePrinter` shouldn’t also handle database updates.       |
| **O – Open/Closed**           | Open for extension, closed for modification.     | Use interfaces or inheritance to add new behavior.             |
| **L – Liskov Substitution**   | Subclasses should be usable as their base class. | A `Square` should behave correctly when used as a `Rectangle`. |
| **I – Interface Segregation** | Don’t force classes to implement unused methods. | Split big interfaces into smaller ones.                        |
| **D – Dependency Inversion**  | Depend on abstractions, not concretions.         | Use interfaces and dependency injection.                       |

---

### 💾 **4. ACID Properties (Databases)**

Guarantees reliable transactions in databases.

| Property            | Meaning                                                    | Example                                                     |
| ------------------- | ---------------------------------------------------------- | ----------------------------------------------------------- |
| **A – Atomicity**   | All or nothing. Transaction fully completes or rolls back. | Money transfer: debit and credit both succeed or both fail. |
| **C – Consistency** | Database remains valid before and after transaction.       | Total bank balance remains unchanged after transfer.        |
| **I – Isolation**   | Concurrent transactions don’t interfere.                   | Two users withdrawing at once don’t cause race conditions.  |
| **D – Durability**  | Once committed, changes survive crashes.                   | Data written to disk/logs before confirmation.              |

---

Would you like me to continue next with **CAP theorem**, **ACID vs BASE**, or **OOP design principles** (like cohesion & coupling)?
