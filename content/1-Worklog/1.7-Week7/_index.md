---
title: "Week 7 Worklog"
date: 2026-08-03
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

* Study and handle **Race Condition** issues in the system.
* Apply **Transactions** and **Locking** (Optimistic/Pessimistic) to ensure data consistency.
* Research and implement system security measures.
* Harden **Authentication**, **Authorization**, and **Input Validation**.
* Perform security testing and fix vulnerabilities.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Study Race Condition in the system: <br>&emsp; + Identify Race Condition risk points: simultaneous bookings for the same property, concurrent payment balance updates <br>&emsp; + Analyze scenario: 2 users booking the same room at the same time <br>&emsp; + Research handling mechanisms: Optimistic Locking (version field), Pessimistic Locking (SELECT FOR UPDATE) <br>&emsp; + Compare trade-offs between Optimistic and Pessimistic Locking | 03/08/2026 | <https://www.postgresql.org/docs/current/explicit-locking.html> |
| Tue | - Apply Transactions and Locking: <br>&emsp; + Implement **Prisma Transaction** (`$transaction`) for operations requiring atomicity <br>&emsp; + Apply **Pessimistic Locking** (`SELECT FOR UPDATE`) for Booking creation to prevent double-booking <br>&emsp; + Apply **Optimistic Locking** with version field for Payment updates <br>&emsp; + Write integration tests to verify Race Condition handling | 04/08/2026 | <https://www.prisma.io/docs/concepts/components/prisma-client/transactions> |
| Wed | - Study system security measures: <br>&emsp; + **OWASP Top 10**: SQL Injection, XSS, CSRF, Broken Authentication, Sensitive Data Exposure <br>&emsp; + Rate limiting to prevent brute-force attacks <br>&emsp; + HTTPS/TLS, CORS configuration <br>&emsp; + Helmet.js for setting security HTTP headers <br>&emsp; + Input sanitization to prevent XSS <br>&emsp; + Environment variable management (no hardcoded secrets) | 05/08/2026 | <https://owasp.org/www-project-top-ten/> |
| Thu | - Harden Authentication, Authorization and Validation: <br>&emsp; + Implement **Refresh Token Rotation**: invalidate old token after each refresh <br>&emsp; + Implement account lockout after multiple failed login attempts <br>&emsp; + Add complete validation to all DTOs (class-validator decorators) <br>&emsp; + Implement global Exception Filter for consistent error handling <br>&emsp; + Sanitize all user input before persisting to the database | 06/08/2026 | <https://docs.nestjs.com/exception-filters> |
| Fri | - Security testing and bug fixes: <br>&emsp; + Test basic attack scenarios: SQL Injection, XSS attempts, CSRF <br>&emsp; + Verify rate limiting is functioning correctly <br>&emsp; + Test Race Condition with concurrent requests (Postman Runner or k6) <br>&emsp; + Fix identified security vulnerabilities <br>&emsp; + Review all Authorization logic | 07/08/2026 | |

---

### Week 7 Achievements:

* Successfully identified and resolved **Race Condition** in the property booking feature:
  * Applied `SELECT FOR UPDATE` within Prisma Transaction, ensuring only 1 booking is created when multiple concurrent requests target the same slot.
  * Concurrent test with 10 simultaneous requests → only 1 booking succeeded, remaining 9 received appropriate error responses.

* Applied **Optimistic Locking** for Payment updates with a version field — detects conflicts and requires retry instead of overwriting data.

* Deployed security measures:
  * Helmet.js with comprehensive security headers (CSP, HSTS, X-Frame-Options)
  * Rate limiting: 10 requests/minute for the login endpoint
  * CORS restricted to the Frontend domain only
  * All DTOs validated and sanitized

* Completed **Refresh Token Rotation** — each token is single-use only, significantly strengthening security.

* Implemented **account lockout** after 5 consecutive failed login attempts (15-minute lockout).

* Security testing confirmed the system is resistant to basic attack vectors from the OWASP Top 10.

---

### Knowledge / Experience Gained:

* Clearly understood the difference between Optimistic and Pessimistic Locking: Optimistic is better when conflicts are rare (better performance), Pessimistic is better when conflicts are frequent (stronger consistency guarantees).
* Internalized the Refresh Token Rotation principle: a token must be immediately invalidated after use to prevent replay attacks.
* Learned how to read the OWASP Top 10 and apply each specific defensive measure to a real-world system.
* Key insight: security is not a feature to add at the end — it must be designed from the start and continuously verified.
