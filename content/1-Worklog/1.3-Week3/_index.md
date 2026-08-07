---
title: "Week 3 Worklog"
date: 2026-07-06
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Design relational database schema and define Prisma models for the real estate rental management system (PostgreSQL + PostGIS).
* Develop **tenant** and **manager** modules: build APIs for user profile and account management by role.
* Develop **application** module: build APIs for tenants to submit and track rental applications.
* Develop **lease** module: build APIs for managers to approve applications, automatically generate leases, and track payments.
* Test API endpoints for user, application, and lease management, and complete Swagger API documentation.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - ERD design and Prisma schema definition: <br>&emsp; + Create schemas for tables: User, Tenant, Manager, Application, Lease, Payment <br>&emsp; + Establish relationships (1-1, 1-N, N-N) and status enums (ApplicationStatus, LeaseStatus) <br>&emsp; + Execute Prisma migrations to initialize PostgreSQL database structure | 06/07/2026 | <https://www.prisma.io/docs/> |
| Tue | - Develop **tenant** module and **manager** module: <br>&emsp; + Build APIs to fetch and update profile details for tenants and managers <br>&emsp; + Build service to sync user data from Amazon Cognito to PostgreSQL via `cognitoId` <br>&emsp; + Validate request payloads using class-validator DTOs | 07/07/2026 | <https://docs.nestjs.com/techniques/validation> |
| Wed | - Develop **application** module (rental application management): <br>&emsp; + Build API for tenants to submit rental applications with personal details and requested lease dates <br>&emsp; + Build API for tenants to view submitted applications and cancel PENDING applications <br>&emsp; + Build API for managers to view and process applications per property | 08/07/2026 | |
| Thu | - Develop **lease** module (contract & payment management): <br>&emsp; + Build API for managers to review applications (APPROVED / DENIED) <br>&emsp; + Implement automatic lease creation upon application approval inside a `prisma.$transaction` <br>&emsp; + Build API to view lease contracts and associated payment history | 09/07/2026 | <https://www.prisma.io/docs/concepts/components/prisma-client/transactions> |
| Fri | - API testing and documentation: <br>&emsp; + Test application creation, approval, and lease generation flows with Postman <br>&emsp; + Verify business constraint enforcement (e.g. preventing duplicate active lease dates) <br>&emsp; + Integrate Swagger (OpenAPI) to document endpoints across the 4 modules | 10/07/2026 | <https://docs.nestjs.com/openapi/introduction> |

---

### Week 3 Achievements:

* Completed **ERD** design and **Prisma schema** definitions for core entities: User, Tenant, Manager, Application, Lease, Payment; ran database migrations successfully on PostgreSQL.

* Completed **Tenant module** and **Manager module**: delivered full APIs for managing user profiles, account settings, and syncing with Amazon Cognito.

* Completed **Application module**: enabled tenants to submit and track applications, while allowing managers to review incoming rental applications.

* Completed **Lease module**: enabled managers to approve applications and automatically generate corresponding lease records inside an atomic database transaction.

* Tested all APIs across the 4 modules using Postman and generated **Swagger API documentation** for all implemented endpoints.

---

### Knowledge / Experience Gained:

* Gained deep insight into designing relational database schemas for rental property lifecycles: Application → Lease → Payment.
* Mastered defining 1-N, 1-1 relations and enums in Prisma schemas, managing migration histories using Prisma CLI.
* Understood how to leverage `prisma.$transaction` to guarantee atomicity during multi-step database operations (application approval + lease creation).
* Learned best practices for DTO input validation to reject malformed requests at the Controller layer.
