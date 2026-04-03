# 08 - Database Interactions & Transactions

Senior backend engineers design data access layers to be highly concurrent, performant, and failure-resistant.

---

## 🏗 Connection Pooling Internals (HikariCP)
Opening a TCP connection to a DB is incredibly slow. Connection pools maintain persistent connections.
- **HikariCP**: The default in Spring Boot. It's aggressively optimized down to the bytecode level using custom concurrent data structures (`FastList`, lock-free programming).
- **Tuning**: Pool size should not be massive. Formula: `connections = ((core_count * 2) + effective_spindle_count)`. A pool of 10-20 is often optimal for typical workloads.

## 🏗 Transaction Isolation Levels
Understanding how different transactions see data concurrently.
1. **READ_UNCOMMITTED**: Dirty Reads allowed (reading uncommitted data).
2. **READ_COMMITTED**: Prevents Dirty Reads. (Postgres Default).
3. **REPEATABLE_READ**: Prevents Non-Repeatable reads. (MySQL InnoDB Default).
4. **SERIALIZABLE**: Highest level, strictly sequential. Prevents Phantom Reads but causes high lock contention.

## 🏗 Spring `@Transactional` Internals
Spring creates a CGLIB or JDK Proxy around your bean.
- **Self-Invocation Problem**: Calling a `@Transactional` method from another method *within the same class* bypassing the proxy, meaning the transaction is ignored.
- **Rollback Rule**: By default, it only rolls back on unchecked exceptions (`RuntimeException`). You must specify `rollbackFor = Exception.class` for checked exceptions.

## 🏗 Propagation Behaviors
- `REQUIRED` (Default): Join existing tx, or create new.
- `REQUIRES_NEW`: Suspend current tx, create a brand new independent tx.
- `SUPPORTS`: Execute within tx if one exists, otherwise execute non-transactionally.

### Interview Question
*Explain the "N+1 Queries" problem and how to solve it in JPA.*
The N+1 problem occurs when querying a list of entities (1 query) and then iterating over them to fetch lazily loaded associations (N queries).
**Fix**: Use `JOIN FETCH` (JPQL) or `EntityGraphs` so the data is fetched eagerly in a single query.

---
[⬅ Back to Interview Roadmap](../README.md)
