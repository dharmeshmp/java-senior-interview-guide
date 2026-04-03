# 10 - Web Development & Microservices Architecture

Architectural knowledge separates intermediate developers from seniors. You must know how to build distributed, fault-tolerant systems.

---

## 🏗 Monolith vs Distributed Trade-Offs
Moving to microservices increases deployment complexity, debugging difficulty, and introduces network latency. It should be driven by organizational scalability, not hype.

## 🏗 Microservice Design Patterns
### 1. Saga Pattern (Distributed Transactions)
Since ACID transactions cannot span multiple databases efficiently (Two-Phase Commit is slow), Saga splits a transaction into local steps. If a step fails, **Compensating Transactions** are fired to undo previous steps.

### 2. CQRS (Command Query Responsibility Segregation)
Separating the Write model (Commands) from the Read model (Queries).
- High-read heavily apps optimize by mirroring event streams (via Kafka) to a NoSQL read DB (e.g., Elasticsearch or Redis) while writes go to a typical RDBMS.

### 3. API Gateway
A single entry point for clients (e.g., Spring Cloud Gateway).
- Handles Rate Limiting, SSL Termination, Authentication filtering, and routing.

## 🏗 System Resilience
- **Circuit Breaker** (Resilience4j): If an external service fails repeatedly, the circuit "opens" (fails fast) to prevent cascading failures. It periodically tests the service (half-open) until it recovers.
- **Bulkhead Pattern**: Allocating limited resources (e.g., thread pools) to different services so one slow service doesn't consume all container threads.

## 🏗 Distributed Observability
- **Centralized Logging**: Pushing all microservice logs to an ELK stack (Elasticsearch, Logstash, Kibana).
- **Distributed Tracing**: Using trace IDs and span IDs (via Zipkin, Jaeger, or OpenTelemetry) to track a request path across multiple container jumps.

### Interview Question: Designing Idempotent APIs
*How do you handle a scenario where a client sends a payment POST request, drops connection, and retries?*
The API must be **Idempotent** — repeating the identical request has no additional effect.
**Design**: The client sends an `Idempotency-Key` in the header. The server checks a high-speed cache (Redis) or lock table to see if the key was already processed. If yes, return the cached successful response.

---
[⬅ Back to Interview Roadmap](../README.md)
