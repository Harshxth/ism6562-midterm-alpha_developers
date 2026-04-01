# SLIDE PORTADA

**Title:** SingleStore: Distributed SQL for the Real-Time Enterprise  
**Subtitle:** ISM 6562 — Midterm Part 4 | Alpha Developers  

**Team Members:**  
Harshith  
Sana  
Robin  
Santiago  

**Date:** April 2, 2026  

---

# SLIDE 1 — TEAM INTRODUCTION

**Title:** Meet the Alpha Developers  

| Team Member | Role in Presentation |
|---|---|
| Harshith | Architecture Lead — Parts 1 & Architecture Overview |
| Sana | Scaling & Sharding Analyst — Part 2 & Scaling Model |
| Robin | Consistency & Cassandra Analyst — Part 3 & CAP/Transactions |
| Santiago | TechRetail Evaluator & System Overview |

---

# SLIDE 2 — WHAT IS SINGLESTORE?

**Title:** What is SingleStore?  

- A distributed relational database engineered for simultaneous high-velocity ingestion and real-time analytics  
- Implements HTAP (Hybrid Transactional/Analytical Processing) — handles both OLTP and OLAP workloads in a single unified engine  
- Originally called MemSQL (2011) — renamed SingleStore in 2021  
- MySQL wire-compatible — any MySQL client connects without driver changes  
- Built on a dual storage engine:  
  - Rowstore → in-memory, optimized for fast writes and point lookups  
  - Columnstore → disk-based columnar format, optimized for analytical scans  
- Core value proposition: "One database. No ETL. Real-time everything."  

---

# SLIDE 3 — WHO BUILT IT & WHO USES IT

**Title:** Who Built It — Who Uses It  

## Creators
- Founded: 2011 as MemSQL  
- Founders:  
  - Nikita Shamgunov — former Microsoft SQL Server engineer  
  - Eric Frenkiel — former Facebook infrastructure engineer  
- Renamed SingleStore: 2021  
- HQ: San Francisco, CA  
- Funding: $300M+ raised  

## Who Uses It

| Company | Use Case |
|---|---|
| Uber | Real-time analytics on billions of ride events/day |
| Palo Alto Networks | Live threat detection on massive network log streams |
| Nasdaq | Market surveillance & financial analytics |
| Comcast | Customer experience analytics at scale |

---

# SLIDE 4 — ARCHITECTURE OVERVIEW

**Title:** Architecture: How SingleStore Distributes Data  

- Storage Engine: Custom hybrid  
  - Rowstore: in-memory skip-list → fast writes/lookups  
  - Columnstore: disk-based compressed columnar → fast scans  
- Consensus/Durability: Synchronous replication  
- Distributed Transactions: Two-Phase Commit (2PC)  
- Node Roles:  
  - Master Aggregator → receives SQL, builds execution plan, routes queries  
  - Leaf Nodes → store partitions, execute queries in parallel  
- Data Partitioning: Hash-based using shard key → unit = Partition  
- Each partition has a primary copy and a redundant copy on a different node  

---

# SLIDE 5 — CAP THEOREM POSITIONING

**Title:** CAP Theorem — SingleStore as CP  

- SingleStore = CP (Consistency + Partition Tolerance)  
  - Strong consistency  
  - Synchronous replication  
  - Rejects writes during partitions  
  - Fixed design (not configurable)  

## Comparison

| System | CAP | Behavior During Partition | Configurable |
|---|---|---|---|
| PostgreSQL | CP | Rejects requests | No |
| Cassandra | AP | Continues reads/writes (may be stale) | Yes |
| SingleStore | CP | Rejects writes | No |

- Suitable for systems requiring strong consistency (orders, inventory, financial data)  

---

# SLIDE 6 — SCALING MODEL

**Title:** Scaling Model — Automatic vs Manual Sharding  

- Automatic hash-based partitioning  
  - User defines shard key  
  - Hash function distributes rows across partitions  
- Unit of distribution: Partition  
- Automatic rebalancing when nodes are added  
- Query performance:  
  - Single-partition queries → fast  
  - Cross-partition queries → slower (broadcast joins)  

## Comparison

| Aspect | Manual Sharding (Part 2) | SingleStore |
|---|---|---|
| Sharding | Manual (geographic) | Automatic (hash-based) |
| Adding nodes | Manual | Automatic |
| ETL changes | Required | Not required |
| Query complexity | High | Transparent |
| Rebalancing | Manual | Automatic |

---

# SLIDE 7 — SQL COMPATIBILITY & TRANSACTIONS

**Title:** SQL Compatibility & Transactions  

## SQL Compatibility
- MySQL wire-compatible  
- Supports JOINs, CTEs, window functions, subqueries, stored procedures  
- Limitations:  
  - No SERIALIZABLE isolation (distributed)  
  - No DDL in transactions  
  - No DEFERRABLE constraints  
  - Cross-partition queries may degrade performance  

## Transactions
- Isolation levels: READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SNAPSHOT  
- Distributed transactions via Two-Phase Commit (2PC)  
- Performance:  
  - Single-partition → fast  
  - Cross-partition → higher latency  

| System | Isolation Level | Distributed Transactions | Cost |
|---|---|---|---|
| PostgreSQL | Up to SERIALIZABLE | N/A | Low |
| Cassandra | Limited | No | Very Low |
| SingleStore | Up to SNAPSHOT | Yes (2PC) | Medium |

---

# SLIDE 8 — TECHRETAIL EVALUATION

**Title:** Would SingleStore Replace TechRetail's Architecture?  

## Current Architecture
- Cassandra Cluster → order ingestion  
- PostgreSQL shards → reference data  
- PostgreSQL warehouse → analytics  
- ETL service → data integration  

## With SingleStore
- Rowstore → ingestion + transactional data  
- Columnstore → analytics  
- Automatic partitioning → replaces manual sharding  
- No ETL required  

## Comparison Table

| Aspect | PG Shards | Cassandra | SingleStore |
|---|---|---|---|
| Role | OLTP | Ingestion | HTAP |
| CAP | CP | AP | CP |
| Consistency | Strong | Eventual | Strong |
| Sharding | Manual | Automatic | Automatic |
| SQL | Full | Limited | Full |
| ETL | Required | Required | Not required |
| Analytics | Slow | Poor | Fast |
| Write Speed | Medium | Very Fast | Fast |
| Cost | Free | Free | Expensive |
| HA | Primary-standby | Leaderless | Synchronous replicas |

---

# SLIDE 9 — CONCLUSION

**Title:** Conclusion — When Should You Use SingleStore?  

## Use SingleStore when
- Real-time analytics is required  
- High ingestion + analytics in one system  
- Need to eliminate ETL pipelines  
- Want unified OLTP + OLAP  

## Do not use SingleStore when
- Cost is a constraint  
- Need always-available writes (AP model)  
- Limited hardware resources  
- Workload is only OLTP  
- Requires SERIALIZABLE distributed isolation  

## TechRetail
- Strong fit at large scale with real-time needs  
- Hard to justify at small scale due to cost  
