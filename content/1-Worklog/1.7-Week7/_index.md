---
title: "Week 7 worklog"
date: 2026-08-03
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

* System optimization: identify and eliminate **N+1 Query** bottlenecks in database queries.
* Analyze and resolve **Race Condition** scenarios in application approval and lease generation using **Transactions** and **Pessimistic Locking**.
* Enhance overall system security: audit role authorization, implement Refresh Token Rotation, Rate Limiting, and HTTP security headers.
* Complete integration of **Amazon Location Service** for property address geocoding and interactive map rendering.
* Optimize PostGIS spatial data queries combined with Amazon Location Service.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Optimize database queries and eliminate **N+1 Query**: <br>&emsp; + Analyze Prisma query logs, measuring query counts on property listing and application endpoints <br>&emsp; + Replace lazy loading with Prisma `include` / `select` eager loading <br>&emsp; + Create database indexes on frequently queried columns (`location`, `price`, `status`, `createdAt`) | 03/08/2026 | <https://www.prisma.io/docs/guides/performance-and-optimization> |
| Tue | - Resolve **Race Condition** challenges: <br>&emsp; + Analyze concurrency conflict scenarios: simultaneous application approval or lease generation operations <br>&emsp; + Apply `prisma.$transaction` and Pessimistic Locking (`SELECT FOR UPDATE`) <br>&emsp; + Run concurrent request tests to verify data consistency under high concurrency | 04/08/2026 | <https://www.postgresql.org/docs/current/explicit-locking.html> |
| Wed | - Strengthen overall application security: <br>&emsp; + Verify 100% protection of mutative endpoints with `AuthGuard` and `RolesGuard` <br>&emsp; + Implement **Refresh Token Rotation**: invalidating old refresh tokens upon rotation <br>&emsp; + Integrate Helmet.js for HTTP security headers and `@nestjs/throttler` for Rate Limiting against brute-force attacks <br>&emsp; + Validate and sanitize all user input using DTO class-validator decorators | 05/08/2026 | <https://owasp.org/www-project-top-ten/> |
| Thu | - Complete **Amazon Location Service** integration: <br>&emsp; + Provision Place Index and Map resources on AWS Console <br>&emsp; + Build `LocationService` in NestJS backend to geocode property text addresses into coordinates (latitude, longitude) <br>&emsp; + Integrate MapLibre GL JS / Amazon Location Service SDK to display interactive search maps in Next.js UI | 06/08/2026 | <https://docs.aws.amazon.com/location/> |
| Fri | - Optimize PostGIS spatial queries and weekly summary: <br>&emsp; + Optimize spatial distance queries (`ST_DWithin` / `ST_Distance`) combining coordinates from Amazon Location Service <br>&emsp; + Re-benchmark API response times after optimization (showing significant latency reductions) <br>&emsp; + Evaluate system security and performance improvements with mentor | 07/08/2026 | |

---

### Week 7 Achievements:

* Completely resolved **N+1 Query** issues: reduced database queries from dozens down to a single JOIN query using Prisma `include`.

* Resolved **Race Conditions** successfully: applied `prisma.$transaction` and `SELECT FOR UPDATE` to prevent duplicate lease creation during concurrent requests.

* Comprehensively elevated **system security**: implemented Refresh Token Rotation, Rate Limiting, Helmet.js HTTP security headers, and secured 100% of API endpoints with `AuthGuard` and `RolesGuard`.

* Successfully integrated **Amazon Location Service**: supported automated address-to-coordinate geocoding and rendering interactive maps for property search.

* Optimized PostGIS spatial queries, reducing location-based property filtering response times to under 50ms.

---

### Knowledge / Experience Gained:

* Understood the root cause of N+1 Query patterns in ORM frameworks and learned eager loading techniques to boost response times.
* Mastered handling concurrent data modification conflicts in PostgreSQL using `prisma.$transaction` and Pessimistic Locking (`SELECT FOR UPDATE`).
* Applied key OWASP Top 10 security principles: Refresh Token Rotation against session hijacking and Rate Limiting against DDoS/brute-force attacks.
* Mastered integrating Amazon Location Service into backend and frontend applications for location services and map visualizations.
