# interviewQuestions

# Detailed Breakdown of System Design Topics for Tech Lead and Architect role Interview questions

---

## 🧠 **Core Topics to Cover**

### 1. **Load Balancing**
Distributes network or application traffic across multiple servers to ensure no single server becomes overwhelmed, increasing fault tolerance and reliability.

### 2. **Caching**
Temporarily stores frequently accessed data to reduce latency and load on backend systems.  
**Tools:** Redis, Memcached.

### 3. **CDN (Content Delivery Network)**
Delivers static assets (images, videos, CSS) from edge servers closest to the user, reducing latency and improving performance.

### 4. **Database Sharding**
Splits large databases horizontally into smaller chunks (shards), each holding part of the data, to enhance scalability and performance.

### 5. **Replication**
Duplicates data across multiple database instances to improve availability, disaster recovery, and read performance.

### 6. **CAP Theorem**
In distributed systems, only **two** of the following three are possible at the same time:
- **Consistency**
- **Availability**
- **Partition Tolerance**

### 7. **Consistency Models**
Defines how and when changes in one copy of data appear in other copies.  
**Examples:** Strong consistency, eventual consistency, causal consistency.

### 8. **Stateless vs Stateful Services**
- **Stateless**: Each request is independent (e.g., REST APIs). Easier to scale.  
- **Stateful**: Keeps user state across sessions (e.g., chat servers).

### 9. **Horizontal vs Vertical Scaling**
- **Horizontal**: Add more machines (e.g., more servers).  
- **Vertical**: Increase the capacity of a single machine (e.g., more RAM/CPU).

---

## 🛠 **System Design Process**

### 1. **Requirements Gathering**
Identify functional (features) and non-functional (latency, availability, scalability) requirements.

### 2. **High-Level Architecture**
Create an overview showing client, APIs, services, databases, and third-party integrations.

### 3. **Component Breakdown**
Define responsibilities for API Gateway, backend services, database, cache, messaging queues, etc.

### 4. **Data Flow**
Describe how data flows through the system—synchronously or asynchronously—and which components communicate.

### 5. **Bottlenecks**
Anticipate performance limits like single points of failure, heavy database reads/writes, or poor cache hit ratios.

### 6. **Fault Tolerance**
Implement retries, timeouts, redundancy, failover systems, and circuit breakers to handle failures.

### 7. **Observability**
Use **logs**, **metrics**, and **tracing** to monitor the system.  
**Tools:** ELK stack, Prometheus, Grafana, Jaeger.

---

## 📋 **Common Interview Questions – Basic to Mid-Level**

### 1. **URL Shortener (like bit.ly)**
Core concepts: key generation, redirection, database design, analytics, rate limiting.

### 2. **E-Commerce Platform**
Components: product catalog, shopping cart, checkout system, payments, order history, inventory sync.

### 3. **News Feed (like Facebook)**
Focus on ranking algorithms, caching, user graph, read/write optimization.

### 4. **Messaging App (like WhatsApp)**
Key aspects: message ordering, delivery guarantees, push notifications, encryption.

### 5. **Ride Sharing App (like Uber)**
Features: real-time location tracking, matching algorithm, pricing engine, ETA calculations.

### 6. **File Storage System (like Google Drive)**
Concepts: chunking, deduplication, file metadata, access control, versioning, S3-like storage.

### 7. **Rate Limiter**
Techniques: token bucket, sliding window, leaky bucket algorithms; apply per IP or user.

---

## 🧩 **Common Interview Questions – Tech Lead / Architect-Level**

### 1. **Multi-Tenant SaaS Platform**
Design with tenant isolation, shared vs dedicated resources, and tenant-specific configs.

### 2. **Global CDN Architecture**
Plan for edge nodes, request routing, cache invalidation, and load balancing across regions.

### 3. **Microservices Architecture**
Define bounded contexts, inter-service communication (REST, gRPC), service discovery, data ownership.

### 4. **Observability/Monitoring Platform**
Collect logs, metrics, and traces; correlate events; alerting and dashboard setup.

### 5. **Identity and Access Management**
Use protocols like OAuth 2.0, SAML, OpenID Connect; manage roles, permissions, SSO, MFA.

### 6. **Monolith to Microservices Migration**
Approach incrementally using strangler pattern; establish shared APIs and avoid data coupling.

### 7. **Disaster Recovery Strategy**
Define RPO (Recovery Point Objective), RTO (Recovery Time Objective), backups, failovers.

### 8. **Streaming Architecture**
Real-time processing using Kafka, Flink/Spark; durable storage in S3 or Data Lake; idempotency.

---

## 🔍 **What Interviewers Look For**

### 1. **Clarification of Requirements**
Do you ask relevant questions to understand constraints and priorities?

### 2. **Trade-off Discussions**
Can you justify choices (e.g., SQL vs NoSQL, REST vs queues) and show awareness of alternatives?

### 3. **Scalability Planning**
How will your system handle traffic spikes or increased data volume?

### 4. **Security Considerations**
Is authentication, authorization, data encryption, and compliance (e.g., GDPR) addressed?

### 5. **Cost Optimization**
Are you optimizing compute, storage, and bandwidth costs without compromising availability?

### 6. **Clear Communication**
Can you convey your ideas to engineers, PMs, and stakeholders with diagrams and simple language?

---

## 🧰 **Tools to Practice With**

### 1. **Diagramming Tools**
- [Excalidraw](https://excalidraw.com): Sketch system diagrams quickly  
- [draw.io](https://draw.io): More formal diagrams with export options

### 2. **Mock Interviews**
- Leetcode (system design interviews)  
- Pramp  
- Interviewing.io

### 3. **Books to Read**
- **Designing Data-Intensive Applications** by *Martin Kleppmann*  
  (Deep dive into distributed systems, databases, and performance)  
- **System Design Interview – An Insider's Guide** by *Alex Xu*  
  (Step-by-step guide to solving real-world system design problems)



# Multi-Tenant SaaS Platform Design

## What is Multi-Tenancy?
Multi-tenancy is a software architecture where a single instance of the application serves multiple customers (tenants). Each tenant’s data is isolated, but they share the same application and infrastructure.

---

## Key Design Goals

1. **Tenant Isolation**  
   Ensure data and access segregation between tenants. Can be logical (row-level) or physical (dedicated DB per tenant).

2. **Configurability**  
   Allow tenant-specific branding, feature toggles, and workflow customizations.

3. **Scalability**  
   Support onboarding of new tenants easily. Horizontal scalability of services and databases.

4. **Security**  
   Enforce strict access controls and encryption. Ensure secure tenant authentication and authorization (RBAC, OAuth, etc.).

---

## Approaches to Data Isolation

| Approach                     | Description                        | Pros                          | Cons                        |
|-----------------------------|------------------------------------|-------------------------------|-----------------------------|
| Shared DB, Shared Schema    | All tenants in same DB and tables  | Easy to manage & cost-efficient | Hard to scale and isolate   |
| Shared DB, Separate Schema  | Same DB, separate tables per tenant| Better isolation, easier backup | Schema migrations are complex |
| Dedicated DB per Tenant     | Each tenant has its own DB         | Best isolation & flexibility   | Operationally expensive     |

---

## Key Architecture Components

1. **Tenant Context Resolver**  
   Middleware that extracts tenant ID from request (e.g., subdomain, token, header).

2. **Authentication & Authorization**  
   Use OAuth 2.0 / JWT with tenant context. Implement RBAC per tenant.

3. **API Gateway**  
   Handle request routing, tenant context injection, rate limiting, and throttling.

4. **Config Service**  
   Fetch tenant-specific UI configs, feature flags, limits, etc.

5. **Data Layer**  
   Abstract database logic using a multi-tenant-aware ORM or data access layer.

6. **Monitoring & Analytics**  
   Metrics and logs should be tagged by tenant ID. Track tenant usage patterns, errors, and audit trails.

---

## Shared vs Dedicated Resources

| Resource   | Shared                         | Dedicated                         |
|------------|--------------------------------|-----------------------------------|
| Compute    | Most often shared              | Can be isolated for premium tenants |
| Storage    | Logical isolation              | Dedicated for compliance (HIPAA, etc.) |
| Cache      | Namespacing per tenant         | Separate Redis clusters            |
| Queues     | Namespaced topics              | Dedicated topics for high-volume tenants |
| Databases  | Often varies                   | Usually dedicated for large clients |

---

## Configuration Management

Use a config service or tenant config table to store:
- Feature toggles
- Branding (colors, logos)
- Limits (users, storage)
- SLA rules (e.g., retry policies)

---

## Multi-Tenancy in the Cloud

- Use **S3 bucket prefixes** per tenant  
- Use **IAM roles** per tenant for data access  
- Auto-scale containers by tenant demand

---

## Security Practices

- Encrypt sensitive data at rest and in transit  
- Apply tenant-aware access controls (tenant ID in access tokens)  
- Prevent cross-tenant data leaks via strict validation and tenancy filters

---

## Testing Considerations

- Test for tenant leakage  
- Ensure per-tenant feature restrictions work as expected  
- Load test with multiple tenants simulating real-world scenarios

---

## Tenant Onboarding Strategy

1. Signup → Collect metadata  
2. Provision DB/schema/resources  
3. Inject tenant config defaults  
4. Send welcome email / UI tour
