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
