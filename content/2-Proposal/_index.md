---
title: "Proposal"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Real Estate Rental Management System

---

### 1. Executive summary

The Real Estate Rental Management System is a unified software platform supporting the end-to-end lifecycle of residential rental transactions — from property listing, map-based search, application submission, application review, automated lease creation, payment tracking, to real-time messaging and automated email notifications.

The system serves two primary user roles: **property managers/landlords (manager)** who publish listings, manage property portfolios, review rental applications, and track active leases; and **tenants (tenant)** who search for properties using multi-criteria filters combined with map locations, submit online applications, track application status, and communicate directly with landlords via real-time messaging.

Technically, the system is built on a monorepo architecture (`pnpm workspaces`) featuring a **NestJS** (TypeScript) backend, a **Next.js** (App Router) frontend utilizing **Redux Toolkit / RTK Query**, a **PostgreSQL** database with **PostGIS** spatial extensions accessed via **Prisma ORM**, and integrated AWS cloud services including **Amazon Cognito** (user authentication), **Amazon S3** (image storage), **Amazon RDS** (managed cloud database), **Amazon SES** (automated email notifications), and **Amazon Location Service** (address geocoding and map rendering). The project aims to deliver a fully functional, high-performance, and secure application tailored for cloud environments.

{{% notice tip %}}
**Live Production Application:** [https://real-estate-client-one-eta.vercel.app/](https://real-estate-client-one-eta.vercel.app/)  
View testing demo accounts and feature walkthroughs in section **[2.1. Live project application](2.1-Project/)**.
{{% /notice %}}

---

### 2. Problem statement

#### Context

The current residential rental market relies heavily on informal channels such as social media groups, individual text messaging, or third-party brokers. The workflow from property discovery to lease execution is often fragmented, lacks dedicated management tools, and presents information transparency risks for both landlords and tenants.

Specifically, key challenges include:

- **For Landlords**: Lack of a centralized dashboard to manage multi-property portfolios; tracking vacant rooms, incoming rental applications, and lease agreements is performed manually using spreadsheets or physical notebooks.
- **For Tenants**: Absence of intuitive map-integrated search tools with multi-criteria filters (price, area, bedroom count, amenities); lack of centralized application status tracking and communication channels.
- **For Communication & Notifications**: Exchanges via personal messaging apps lead to scattered information; lack of automated email alerts when rental application or lease contract statuses change.

#### Proposed solution

![Overview](/images/2-Proposal/Role.png)

The system resolves these limitations through a centralized web application:

- Landlords gain access to a dedicated Manager Dashboard to oversee property portfolios, review incoming applications, and track active lease agreements.
- Tenants benefit from an intuitive search interface combining filtering and map visualizations, online application submission, and real-time status tracking.
- Upon application approval, the system automatically generates a lease contract (Lease) within an atomic database transaction, ensuring strict lease schedule control and preventing scheduling overlaps.
- Tenants and landlords communicate directly via property-anchored real-time chat widgets.
- Automated transactional email notifications are dispatched via Amazon SES when applications are submitted or status updates occur.

---

### 3. Solution architecture

The system is structured as a **monorepo** (`pnpm workspaces`), containing backend services (`apps/server`), frontend client (`apps/client`), and shared data types (`packages/types`, `@shared/types`) to ensure strict type consistency between client and server layers.

#### System architecture diagram

![Architecture](/images/2-Proposal/AWS_architect.png)

#### AWS services utilized

| Service | Role in the System |
|---------|--------------------|
| **Amazon RDS (PostgreSQL + PostGIS)** | Primary relational database storing all domain data; PostGIS extension handles spatial data queries and distance calculations |
| **Amazon S3** | Centralized storage for property images uploaded from the application |
| **Amazon Cognito** | Manages user registration, login authentication, and issues JWT tokens for all backend API requests |
| **Amazon SES** | Dispatches automated transactional emails to users upon rental application status changes or lease creations |
| **Amazon Location Service** | Geocodes text addresses into coordinates (latitude, longitude), powers place autocomplete, and renders interactive map tiles |

#### Backend module design

The NestJS backend is organized into modular domain components:

- **Auth module**: Receives JWT tokens from Amazon Cognito, applying `AuthGuard` and `RolesGuard` for user authentication and authorization.
- **Property module**: Handles property CRUD operations, Amazon S3 image uploads, and multi-criteria filtering integrated with PostGIS spatial queries.
- **Application module**: Manages tenant rental applications and landlord approval workflows.
- **Lease module**: Automatically generates lease contracts inside a `prisma.$transaction` upon application approval and maintains payment history (Payment).
- **Message module**: Manages WebSocket Gateway connections, enabling real-time chat between tenants and landlords.
- **Notification module**: Stores in-app notifications and triggers Amazon SES email dispatches.
- **Location module**: Interfaces with Amazon Location Service for address geocoding and map spatial queries.
- **Tenant / Manager module**: Manages user profile information by role, synchronized with Cognito profiles via `cognitoId`.

---

### 4. Technical implementation

#### Technology stack & rationale

**Backend — NestJS (TypeScript)**

**NestJS** was selected for its modular architecture, allowing clear separation of domain concerns such as properties, applications, leases, and real-time chat. Its **Dependency Injection** mechanism reduces coupling between application layers, facilitating unit testing and maintainability. **TypeScript** delivers strong static typing, combining with **Prisma ORM** for end-to-end data type safety from database schema to controllers.

**Frontend — Next.js (App Router)**

**Next.js** App Router enables optimized page rendering. The application leverages **Server Components** for property listing pages to maximize initial load performance, while employing **Client Components** for interactive UI elements such as maps, filters, and chat boxes. **Redux Toolkit** and **RTK Query** manage global authentication state, API response caching, and automatic JWT bearer token header injection.

**Database — PostgreSQL + PostGIS + Prisma ORM**

**PostgreSQL** perfectly fits the relational domain model of rental property management. The **PostGIS** extension enables storing coordinates as spatial `geography` types and performing distance calculations directly in SQL queries. **Prisma ORM** manages database migrations cleanly while allowing raw SQL execution (`$queryRaw`) when combining standard filter predicates with PostGIS spatial functions.

#### Key engineering achievements

**Role-based authentication & authorization**

The system relies on JWT tokens issued by **Amazon Cognito**. At the NestJS backend, `AuthGuard` extracts and verifies JWT token validity, while `RolesGuard` enforces role permissions (TENANT or MANAGER). The system implements **Refresh Token Rotation** to invalidate old refresh tokens upon rotation, preventing session hijacking.

**Eliminating N+1 queries**

During development, fetching property listings alongside images and landlord details initially caused N+1 query performance hits. This was resolved by utilizing Prisma's **include** feature to eager-load related models in a single JOIN query. Combined with database **indexes** on frequently queried columns (`location`, `price`, `status`), P95 response times dropped from ~800ms to ~120ms.

**Concurrency control (race condition prevention)**

Concurrent application approvals for the same property could lead to duplicate lease creation or data inconsistency. This was resolved by wrapping approval workflows in a **`prisma.$transaction`** combined with **Pessimistic Locking (`SELECT ... FOR UPDATE`)**. When a transaction begins, the target property record is locked until completion, guaranteeing absolute data consistency.

**Geocoding & interactive maps**

Text addresses entered during property creation are sent to **Amazon Location Service** to convert into geographic coordinates (latitude, longitude). These coordinates are stored in PostGIS-enabled location tables. During user property searches, the backend executes `ST_DWithin` spatial distance queries to return properties within the selected radius on the Next.js interactive map.

#### 8-week development timeline

| Week | Phase | Key tasks |
|------|-------|-----------|
| 1 | Onboarding & Setup | FCAJ policy review, AWS fundamentals, AWS Free Tier registration, $200 AWS Credits receipt, AWS Budget creation |
| 2 | Architecture & Cognito | Requirements analysis, architecture design, monorepo refactoring, Amazon Cognito integration, `AuthGuard` & `RolesGuard` implementation |
| 3 | DB & Core Modules | Prisma schema definition (PostgreSQL + PostGIS), development of **tenant**, **manager**, **application**, and **lease** modules |
| 4 | Property & Search | Development of **property** module, **Amazon S3** image upload integration, multi-criteria search filtering, user profile pages |
| 5 | Manager Dashboard & RDS | Manager Dashboard completion, property editing & S3 image management, DB migration to **Amazon RDS PostgreSQL** |
| 6 | Real-time Chat & SES Email | Development of **message** module (WebSocket real-time chat), **notification** module (in-app notifications), **Amazon SES** email integration |
| 7 | Performance & Security | N+1 query optimization (Prisma `include`), Race Condition handling (Pessimistic Locking `SELECT ... FOR UPDATE`), **Amazon Location Service** integration |
| 8 | E2E Testing & Handover | End-to-End system testing, bug fixes, README and Swagger API documentation completion, Worklog & Proposal finalization, demo presentation |

---

### 5. Roadmap & milestones

```
Week 1    │ ████ Onboarding · AWS Fundamentals · AWS Budget
Week 2    │ ████ Architecture Design · Monorepo · Amazon Cognito · Guards
Week 3    │ ████ Prisma Schema · PostgreSQL · Tenant/Manager/Application/Lease Modules
Week 4    │ ████ Property Module · Amazon S3 Image Upload · Filters · Profile Pages
Week 5    │ ████ Manager Dashboard · Property Editing · DB Migration to Amazon RDS
Week 6    │ ████ WebSocket Real-time Chat · Notification · Amazon SES Email
Week 7    │ ████ N+1 Query Optimization · Race Condition Locking · Amazon Location Service
Week 8    │ ████ E2E Testing · README & Swagger Docs · Proposal & Demo Handover
```

**Key milestones:**

- **Week 2**: Completed onboarding, received $200 AWS Credits, configured AWS Budget, and finalized Cognito authentication flows.
- **Week 4**: Finalized property listing workflow with Amazon S3 image uploads and multi-criteria search filtering.
- **Week 5**: Successfully migrated database to Amazon RDS and conducted internal Manager Dashboard demo with mentor.
- **Week 6**: Deployed WebSocket real-time chat and automated transactional email dispatches via Amazon SES.
- **Week 7**: Resolved N+1 query bottlenecks, prevented Race Conditions using Pessimistic Locking, and integrated location map features.
- **Week 8**: Passed End-to-End testing, finalized technical documentation, successfully presented live demo, and handed over system.

---

### 6. Budget estimation

System operational costs throughout the 8-week internship were managed strictly within the **AWS Free Tier** allocation and the **$200 USD AWS Credits** provided by the student program.

#### AWS Infrastructure costs (Estimated development environment)

| Service | Utilization configuration | Estimated monthly cost |
|---------|---------------------------|------------------------|
| **AWS Amplify** | Frontend Next.js app hosting (Build & Static/SSR hosting) | $0.00 (Free Tier) |
| **Amazon Route 53** | DNS lookup & custom domain routing (1 Hosted Zone) | ~$0.50 / month |
| **AWS WAF** | Web Application Firewall protecting ingress traffic (1 Web ACL) | ~$6.00 / month |
| **AWS ACM** | SSL/TLS certificate management for HTTPS domain encryption | $0.00 (Free AWS Service) |
| **Application Load Balancer (ALB)** | Ingress traffic routing to EC2 backend in Public Subnet | $0.00 (Free Tier) / ~$16.00 / month |
| **Amazon EC2** | NestJS backend REST & WebSocket server in Private Subnet (`t3.micro`, 8 GB EBS) | $0.00 (Free Tier) / ~$7.50 / month |
| **Amazon RDS (PostgreSQL)** | Primary database + PostGIS extension (`db.t3.micro`, 20 GB SSD, Single-AZ) | $0.00 (Free Tier) / ~$15.00 / month |
| **Amazon S3** | Property image uploads (~5 GB storage, ~10,000 requests/month) | $0.00 (Free Tier) / ~$0.15 / month |
| **Amazon Cognito** | User registration, authentication & JWT token issuance (<50,000 MAU) | $0.00 (Free Tier - Always Free) |
| **Amazon SES** | Transactional email notifications (~500 emails/month in Sandbox) | $0.00 (Free Tier - Always Free) |
| **AWS IAM** | Access management, EC2 execution roles & S3 bucket policies | $0.00 (Free AWS Service) |
| **Amazon CloudWatch** | Application logging, metrics collection & basic alarm monitoring (<5 GB logs) | $0.00 (Free Tier - Always Free) |
| **Amazon Location Service** | Address geocoding and interactive map tile rendering within tier limits | ~$0.50 / month |
| **Total Estimated Cost** | **Development Environment (with AWS Free Tier applied)** | **~$7.00 - $16.00 / month** |

> Total actual expenditure across 8 weeks of development and testing was approximately $32.00, fully covered by the $200 AWS Credits allocation.

#### Cost management strategy

- Configured **AWS Budget** automated email alerts triggered at $50 and $100 spending thresholds.
- Provisioned **Single-AZ RDS** instances for development to minimize costs compared to Multi-AZ setups.
- Implemented client-side input debouncing to limit API request frequency to Amazon Location Service during address searches.
- Scheduled Amazon RDS instance stops outside office hours when active development was paused.

---

### 7. Risk assessment & mitigation

#### Risk matrix

| Identified Risk | Severity | Likelihood | Mitigation Strategy |
|-----------------|----------|------------|---------------------|
| AWS Credit overspending | Medium | Low | Configured AWS Budget alerts; stopped RDS instances during off-hours |
| Race Conditions during concurrent application approvals | High | Medium | Applied `prisma.$transaction` combined with Pessimistic Locking (`SELECT ... FOR UPDATE`) |
| Performance degradation due to N+1 Queries | Medium | Medium | Eager-loaded relations with Prisma `include` and indexed frequently searched columns |
| Real-time WebSocket chat disconnection | Medium | Low | Implemented client auto-reconnection logic and stored message history in PostgreSQL |
| Excessive file upload sizes to Amazon S3 | Low | Low | Enforced payload size and MIME-type validation at Controller layer prior to SDK calls |

#### Contingency plans

- **Amazon RDS Database**: Configured automated daily snapshots on Amazon RDS to enable rapid point-in-time recovery if data corruption occurs.
- **Amazon S3 Uploads**: Implemented backend retry logic with exponential backoff for transient S3 connection issues.
- **Amazon SES Email Delivery**: Persisted all notification events in the `Notification` table; if email delivery fails, users can still view notifications in the app UI.

---

### 8. Expected outcomes

#### Technical deliverables

At the conclusion of the 8-week project, the system achieved the following outcomes:

- **Fully functional business workflows**: Tenants search properties on interactive maps, submit applications, and engage in real-time chat; Landlords manage listings, edit images, review applications, and automatically generate leases.
- **Standardized authentication & security**: 100% of API endpoints protected via `AuthGuard` and `RolesGuard`, implementing Refresh Token Rotation and strict payload validation.
- **Optimized system performance**: N+1 queries eliminated, reducing listing API response times to ~120ms; concurrency issues eliminated using Pessimistic Locking.
- **Comprehensive documentation**: Complete deployment `README.md`, **Swagger API documentation** covering 40+ endpoints, and standardized system architecture diagrams.

#### Learning outcomes & skill development

The internship project provided extensive hands-on experience in full-stack cloud engineering:

- Software architecture design and monorepo management for enterprise application development.
- Integration and operation of cloud services (Cognito, S3, RDS, SES, Location Service) in production-ready workflows.
- Relational database query optimization, PostGIS spatial data handling, and database transaction concurrency control.
- Professional technical writing, worklogging, and product demonstration capabilities.

#### Future enhancements

The modular NestJS and Next.js architecture enables seamless future expansion:

- Online payment gateway integration (VNPay, ZaloPay, Stripe) for automated monthly rent processing.
- Cross-platform mobile application development utilizing React Native.
- Application containerization using Docker and deployment onto Amazon ECS / EKS for automated cloud scaling.