# Event Booking System (EBS) 

*A scalable event ticket booking platform inspired by BookMyShow, built with Spring Boot microservices.*

---

### **1\. Problem Statement**

Build a platform where users can:

* Browse and search events

* View seat availability

* Book tickets securely

* Make payments

* Receive booking notifications

Admins can:

* Create and manage events

* View analytics (popular events, revenue)

System should be **modular, scalable, and fault-tolerant**.

---

### **2\. Functional Requirements**

* **User Management:** Registration, login, roles (User/Admin)

* **Event Management:** CRUD operations for events, venues, seat maps

* **Booking System:** Safe seat allocation and booking with concurrency control

* **Payments:** Integration with mock or real payment gateway (Razorpay sandbox)

* **Notifications:** Email or push notifications for confirmations

* **Analytics:** Booking summaries, top events

---

### **3\. Non-Functional Requirements**

| Category | Details |
| ----- | ----- |
| Scalability | Microservice architecture; independent scaling per service |
| Availability | Fault-tolerant via async queues \+ retry logic |
| Consistency | Strong consistency for bookings (transactional); eventual consistency for notifications |
| Security | JWT-based auth, encrypted communications |
| Performance | Redis caching for events/seat maps |
| Observability | Prometheus metrics, structured logging |

---

### **4\. High-Level Architecture Diagram**

                        `+-----------------------+`  
                         `|      Client Apps      |`  
                         `| (Web, Mobile, Admin)  |`  
                         `+----------+------------+`  
                                    `|`  
                             `HTTPS / REST / WS`  
                                    `|`  
                         `+----------v------------+`  
                         `|     API Gateway       |`   
                         `| (Spring Cloud Gateway)|`  
                         `+---+-----+-----+-------+`  
                             `|     |     |`  
        `+--------------------+     |     +----------------+`  
        `|                          |                      |`  
`+-------v------+          +--------v---------+     +------v-------+`  
`| Auth Service |          |  User Service    |     | Event Service|`  
`| (JWT, OAuth) |          | (profiles, roles)|     | (CRUD, search)|`  
`+--------------+          +------------------+     +---------------+`  
        `|                           |                     |`  
        `|                           |                     |`  
`+-------v-------+          +--------v-------+      +------v--------+`  
`| Redis Cache   |          | PostgreSQL DB  |      | PostgreSQL DB |`  
`| (sessions)    |          | (users)        |      | (events)      |`  
`+---------------+          +----------------+      +---------------+`

                    `+------------------------------------------+`  
                    `|            Booking Service               |`  
                    `| (Seat allocation, concurrency handling)  |`  
                    `+----------------+-------------------------+`  
                                     `|`  
                              `+------v------+`  
                              `| PostgreSQL  |`  
                              `| (bookings)  |`  
                              `+-------------+`  
                                     `|`  
                             `+-------v--------+`  
                             `| Message Broker |`  
                             `|  (Kafka/Rabbit)|`  
                             `+---+----+-------+`  
                                 `|    |`  
           `+---------------------+    +------------------+`  
           `|                                         |`  
`+----------v-----------+                +-------------v-------------+`  
`| Notification Service |                |   Payment Service         |`  
`| (Email/Push async)   |                | (Razorpay/Mock Sandbox)   |`  
`+----------------------+                +---------------------------+`

`Monitoring: Prometheus/Grafana | Deployment: Docker/K8s/AWS`

---

### **5\. Service Descriptions**

| Service | Key Responsibilities | Tech Stack |
| ----- | ----- | ----- |
| **Auth Service** | User registration, login, JWT issuance | Spring Boot, Spring Security, JWT |
| **User Service** | Stores profiles, preferences | Spring Boot, PostgreSQL |
| **Event Service** | Manage event CRUD, seat map caching | Spring Boot, PostgreSQL, Redis |
| **Booking Service** | Handle seat booking with concurrency control | Spring Boot, PostgreSQL, Kafka |
| **Payment Service** | Process or mock payments | Spring Boot, Razorpay Sandbox |
| **Notification Service** | Send email/SMS asynchronously | Spring Boot, RabbitMQ, SMTP |
| **API Gateway** | Central routing, rate-limiting, auth verification | Spring Cloud Gateway |
| **Message Broker** | Decouples async tasks | Kafka or RabbitMQ |

---

### **6\. Data Flow Example (Booking Flow)**

1. User selects event and seats → sends booking request.

2. Booking Service verifies seat availability (Event Service / Redis).

3. Seats are **held temporarily** using optimistic locking or Redis locks.

4. Booking is created in DB with `status=HELD`.

5. Payment Service handles payment; upon success:

   * Booking Service updates `status=CONFIRMED`.

   * Emits event → Notification Service.

6. Notification Service sends confirmation mail and ticket.

---

### **7\. Key Design Choices**

| Concern | Approach |
| ----- | ----- |
| Concurrency | Optimistic Locking with version fields |
| Scalability | Each service is stateless and containerized |
| Fault Tolerance | Retry logic \+ message queues |
| Security | JWT Auth, rate limiting |
| Performance | Redis cache for events and seat maps |
| Deployability | Docker Compose for local; Render/AWS for prod |
| Observability | Prometheus metrics \+ Grafana dashboard |

---

### **8\. Future Enhancements**

* Dynamic pricing (surge)

* Waitlist / auto-upgrade

* Recommendation engine

* Integration with real mail/SMS providers

* Multi-region replication and failover

