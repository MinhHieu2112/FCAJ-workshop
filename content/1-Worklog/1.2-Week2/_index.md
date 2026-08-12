---
title: "Week 2 worklog"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Analyze business requirements for the real estate rental management system and define user roles (tenant, manager).
* Design overall system architecture and refactor the repository into a monorepo structure (pnpm workspaces).
* Integrate Amazon Cognito service to manage user identities and issue JWT authentication tokens.
* Build authentication and authorization mechanisms in the NestJS backend using `AuthGuard` and `RolesGuard`.
* Test user registration, login, and role-based access control flows before developing core business modules.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Analyze real estate rental system requirements: <br>&emsp; + Define primary user roles: tenant and manager <br>&emsp; + Map out core business flows: property listing, application submission, approval, lease contract <br> - Refactor project repository into a monorepo using pnpm workspaces (`apps/server`, `apps/client`, `packages/types`) | 29/06/2026 | <https://cloudjourney.awsstudygroup.com/1-explore/> |
| Tue | - Design high-level system architecture: <br>&emsp; + Backend: NestJS (TypeScript, modular architecture, DI) <br>&emsp; + Frontend: Next.js (App Router, Redux Toolkit / RTK Query) <br>&emsp; + Database: PostgreSQL with Prisma ORM <br>&emsp; + Define integration diagram for AWS services (Cognito, S3, RDS, Location Service) | 30/06/2026 | <https://docs.nestjs.com/> |
| Wed | - Integrate Amazon Cognito and JWT Authentication: <br>&emsp; + Create and configure Amazon Cognito User Pool & App Client <br>&emsp; + Set up registration, email verification, and login authentication flows via Cognito <br>&emsp; + Synchronize user profiles from Cognito to backend using `cognitoId` | 01/07/2026 | <https://docs.aws.amazon.com/cognito/> |
| Thu | - Implement authentication and authorization mechanisms in NestJS backend: <br>&emsp; + Build `AuthGuard` to verify JWT signature and expiration <br>&emsp; + Build `RolesGuard` to check role-based permissions (TENANT, MANAGER) <br>&emsp; + Apply role decorators to initial controller endpoints | 02/07/2026 | <https://docs.nestjs.com/guards> |
| Fri | - Test and review Week 2 progress: <br>&emsp; + Test authentication and authorization APIs using Postman (register, login, token verification) <br>&emsp; + Verify restriction of invalid or unauthorized requests <br>&emsp; + Report progress and discuss Week 3 roadmap with mentor | 03/07/2026 | |

---

### Week 2 Achievements:

* Completed system requirements analysis, defining two main user roles: **tenant** and **manager**.

* Successfully refactored project architecture to a **monorepo** structure with pnpm workspaces (`apps/server`, `apps/client`, `packages/types`), facilitating type sharing between frontend and backend.

* Finalized the high-level system architecture combining NestJS, Next.js, PostgreSQL, and AWS services.

* Integrated **Amazon Cognito** successfully: configured User Pool, App Client, authentication flow, and JWT token issuance.

* Built and applied **`AuthGuard`** and **`RolesGuard`** in NestJS backend, securing controller endpoints and enforcing role-based permissions (TENANT, MANAGER).

---

### Knowledge / Experience Gained:

* Learned monorepo organization using pnpm workspaces, keeping shared DTOs and interfaces consistent across client and server.
* Mastered delegating authentication management to Amazon Cognito, reducing risks associated with managing user credentials in an in-house database.
* Understood how to write custom Guards in NestJS (using ExecutionContext and Reflector) to implement Authentication & Role-based Access Control (RBAC).
* Developed an architectural mindset of modular system design prior to feature implementation.
