---
title: "Week 3 Worklog"
date: 2026-07-06
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Study the business domain of a real estate system — identify actors, use cases, and core business workflows.
* Analyze requirements and build a feature list for the system.
* Design the overall system architecture (high-level architecture).
* Select appropriate technologies for Backend, Frontend, and Database.
* Identify AWS services to be integrated into the system.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Research real estate business domain: <br>&emsp; + Identify actors: Tenant, Landlord, Admin <br>&emsp; + Analyze core business workflows: listing, search, viewing appointment, contract signing, payment <br>&emsp; + Study real-world real estate platforms (Mogi, Batdongsan) for reference | 06/07/2026 | Real-world reference |
| Tue | - Analyze system requirements: <br>&emsp; + Write Use Case Diagrams for each actor <br>&emsp; + List functional and non-functional requirements <br>&emsp; + Identify business rules and constraints <br> - Discuss system scope with mentor | 07/07/2026 | Business Analysis Doc |
| Wed | - Design high-level system architecture: <br>&emsp; + Draw high-level architecture diagram <br>&emsp; + Identify core modules: Auth, Property, Booking, Payment, Notification <br>&emsp; + Define inter-module communication (REST API, message queue) <br>&emsp; + Draft initial API endpoints following RESTful conventions | 08/07/2026 | |
| Thu | - Select technology stack: <br>&emsp; + **Backend**: NestJS (TypeScript) — modular architecture, dependency injection <br>&emsp; + **Frontend**: Next.js (React) — SSR/SSG, routing, TailwindCSS <br>&emsp; + **Database**: PostgreSQL + Prisma ORM <br>&emsp; + Document rationale for each technology choice (trade-off analysis) | 09/07/2026 | |
| Fri | - Identify AWS services to integrate: <br>&emsp; + **S3**: store real estate listing images <br>&emsp; + **SES**: send notification and verification emails <br>&emsp; + **Cognito**: user authentication management <br>&emsp; + **RDS**: production database <br>&emsp; + **EC2 / ECS**: deploy backend services <br> - Consolidate design documentation and push to repository | 10/07/2026 | |

---

### Week 3 Achievements:

* Thoroughly understood the business domain of a property rental system with 3 main actors: **Tenant**, **Landlord**, and **Admin**.

* Completed the requirements analysis document including:
  * Use Case Diagrams for each actor
  * Functional Requirements list (listing, search, scheduling, payment, notifications)
  * Non-Functional Requirements (performance, security, scalability)

* Completed the high-level system architecture design with modules: **Auth**, **Property**, **Booking**, **Payment**, **Notification**, **Admin**.

* Finalized technology stack decision:
  * Backend: **NestJS** (TypeScript, modular, DI)
  * Frontend: **Next.js** (App Router, SSR)
  * ORM: **Prisma** with **PostgreSQL**
  * Styling: **TailwindCSS**

* Completed a comprehensive list of AWS services to integrate: S3, SES, Cognito, RDS, EC2/ECS.

---

### Knowledge / Experience Gained:

* Learned the methodology for real-world business domain analysis: starting from actors → use cases → business rules → functional requirements.
* Understood the critical importance of architectural design before coding — preventing costly refactors down the line.
* Grasped technology selection trade-offs: NestJS suits modular backend architecture; Prisma enables type-safe database access.
* Learned to incorporate AWS services into system design during the planning phase rather than retrofitting them later.
