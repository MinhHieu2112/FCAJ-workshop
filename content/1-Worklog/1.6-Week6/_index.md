---
title: "Week 6 Worklog"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

* Optimize database queries — identify and resolve **N+1 Query** problems.
* Apply **eager loading** and **batching** techniques to improve performance.
* Benchmark system performance after optimization (before/after comparison).
* Document technical findings and optimization results.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Database query optimization: <br>&emsp; + Analyze existing queries using Prisma logging and query counting <br>&emsp; + Identify N+1 Query hotspots: Property listing with images, Booking with user and property info <br>&emsp; + Benchmark query counts before optimization <br>&emsp; + Set up database query logging | 27/07/2026 | <https://www.prisma.io/docs/concepts/components/prisma-client/logging> |
| Tue | - Study and address N+1 Query problems: <br>&emsp; + Analyze root cause: ORM lazy loading triggers N+1 when iterating lists and accessing relations <br>&emsp; + Research solutions: eager loading (Prisma `include`), batching (DataLoader pattern) <br>&emsp; + Compare `include` vs `select` in Prisma to fetch only required fields <br>&emsp; + Apply `include` to API endpoints with N+1 issues | 28/07/2026 | <https://www.prisma.io/docs/guides/performance-and-optimization/query-optimization-performance> |
| Wed | - Apply eager loading, batching and query optimization: <br>&emsp; + Refactor Property listing: `include` images and landlord info in a single query <br>&emsp; + Refactor Booking listing: `include` property and tenant info <br>&emsp; + Apply **database indexing**: create indexes on frequently filtered/searched columns (location, price, status) <br>&emsp; + Implement **cursor-based pagination** to avoid full table OFFSET scans | 29/07/2026 | |
| Thu | - Post-optimization performance testing: <br>&emsp; + Re-measure query count after refactoring (N+1 → 1) <br>&emsp; + Use `EXPLAIN ANALYZE` in PostgreSQL to inspect query plans <br>&emsp; + Load test with Artillery or `k6` using 100 concurrent users <br>&emsp; + Record results: response time, query count, throughput | 30/07/2026 | |
| Fri | - Consolidate optimization results: <br>&emsp; + Write a technical report on N+1 Query: causes, solutions, and measured results <br>&emsp; + Update the codebase with query optimization best practices <br>&emsp; + Review the entire codebase for any remaining optimization opportunities | 31/07/2026 | |

---

### Week 6 Achievements:

* Identified **5 N+1 Query hotspots** in the system: Property listing (images + landlord), Booking listing (property + user), Notification listing (booking details).

* Resolved all N+1 issues by applying **Prisma `include`** — reducing from N+1 queries to a single query with JOIN:
  * Property listing: from ~50 queries → 1 query (for 20 records)
  * Booking listing: from ~30 queries → 1 query (for 10 records)

* Applied **database indexing** on columns: `location`, `price`, `status`, `createdAt` — reduced filter query time from ~200ms to ~15ms.

* Implemented **cursor-based pagination** to replace offset pagination, more efficient for large tables (no full table scan required).

* Load test results (100 concurrent users):
  * Before optimization: P95 response time ~800ms
  * After optimization: P95 response time ~120ms (**85% reduction**)

* Completed a technical report on N+1 Query with concrete measurement data.

---

### Knowledge / Experience Gained:

* Gained a deep understanding of N+1 Query — one of the most common bottlenecks in ORM-based applications. ORM lazy loading is convenient but dangerous for performance when working with large lists.
* Learned how to use `EXPLAIN ANALYZE` in PostgreSQL to read query execution plans and identify sequential scans vs. index scans.
* Understood when to use cursor-based pagination: ideal for large datasets and real-time feeds; offset pagination is better for traditional page-number UIs.
* Practical lesson: selective `include` (only joining required fields) outperforms full `include` — avoids fetching unnecessarily large data payloads.
