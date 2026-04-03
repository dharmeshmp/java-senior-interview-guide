# 11 - Microservices Architecture & Design Patterns

Microservices have become the de-facto standard for large-scale enterprise applications. In senior-level interviews, you are expected to know the trade-offs of distributed systems and how to solve their structural, data, and resilience challenges.

---

## 🏗 1. Decomposition & Arch Patterns
- **Strangler Fig Pattern**: Used for migrating a Monolith. You gradually replace specific functionalities with new microservices, routing traffic via an API Gateway until the monolith can be "strangled" and decommissioned.
- **Sidecar Pattern**: Deploying helper components (like a proxy, logging agent, or metrics exporter) alongside your service in the same Pod (Kubernetes). This is the foundation of **Service Mesh** (e.g., Istio).

## 🏗 2. Data Management Patterns
- **Database per Service**: The golden rule. To ensure loose coupling, each service must exclusively own its data. If Service A needs Service B's data, it must ask Service B via an API.
- **Transactional Outbox Pattern**: When a service needs to update its database *and* publish a Kafka message reliably (preventing the "Dual Write" problem). The system inserts the event into an `Outbox` table in the same DB transaction. A separate Relaying Process (like Debezium) streams the outbox table to Kafka.

## 🏗 3. Distributed Transactions
- **Saga Pattern**: Solves distributed transactions without locking/2PC. A Saga is a sequence of local transactions. If a step fails, **Compensating Transactions** execute to undo previous operations.
  - *Choreography*: Event-driven. Services listen to each other. Good for simple workflows but becomes a "spaghetti" of events.
  - *Orchestration*: A central orchestrator service tells other services what to do via commands. Best for complex workflows.

## 🏗 4. Querying & Composition Patterns
- **CQRS (Command Query Responsibility Segregation)**: Separates read and write operations. The write side updates the source of truth, emitting events. The read side consumes these events to build optimized NoSQL/Elasticsearch materialized views for hyper-fast querying.
- **API Composition**: When a UI needs data from 4 different services, an API Composer (or Gateway) queries them all, joins the data in memory, and returns a single unified payload.

## 🏗 5. Communication & Gateway Patterns
- **API Gateway**: A single entry point that routes requests to internal microservices. It handles cross-cutting concerns: SSL termination, Authentication, Rate Limiting, and Request Routing.
- **BFF (Backend for Frontend)**: Instead of one massive API Gateway, you create specialized gateways tailored for specific clients (e.g., one BFF for the Mobile App, one for the Web App).

## 🏗 6. Resiliency Patterns
Network calls in distributed systems *will* fail. Relying entirely on timeouts is dangerous.
- **Circuit Breaker** (e.g., Resilience4j): Prevents an application from repeatedly trying to execute an operation that's failing. If failures breach a threshold, the circuit "opens" (fails fast). It later periodically tests (half-open) to see if the service recovered.
- **Bulkhead Pattern**: Isolating resources. E.g., giving connection pools quotas so a failure in the Payment service doesn't consume all threads and crash the User service.
- **Retry with Exponential Backoff + Jitter**: When a service is overloaded, immediate retries will DDOS it. Exponential backoff distributes retries over time, and "Jitter" adds randomness to prevent the "Thundering Herd" problem.

## 🏗 7. Observability Patterns
- **Distributed Tracing**: Uses a `Trace ID` (covers the entire request journey) and `Span ID` (covers a single service hop). Essential for debugging microservice latency (Zipkin, Jaeger, OpenTelemetry).
- **Log Aggregation**: You cannot SSH into 50 different pods to read logs. Push everything to an ELK/EFK Stack (Elasticsearch, Fluentd/Logstash, Kibana) or Splunk.

### Interview Scenario
**Q:** *How do you synchronize data updates across microservices efficiently without locking?*
**A:** Use **Eventual Consistency** driven by **Event Sourcing** and CQRS. Instead of storing just the current state of an entity, you append a sequence of state-changing events to an Event Store (like Kafka). Other microservices consume the stream and update their local read-databases.

---
[⬅ Back to Interview Roadmap](../README.md)
