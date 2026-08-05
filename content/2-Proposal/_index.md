---
title: "Proposal"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Real Estate Rental Management System
## An Integrated Software Solution with AWS for the Residential Rental Market

---

### 1. Executive Summary

The Real Estate Rental Management System is a web-based platform that manages the full lifecycle of a residential rental transaction — from property listing and search, to viewing appointment scheduling, contract creation, payment tracking, and automated notifications — within a unified system.

The platform serves three primary user groups: **Landlords** who manage their property portfolios and process rental applications; **Tenants** who search for properties, book viewings, and sign contracts online; and **Administrators** who moderate content and monitor overall system activity.

Technically, the system is built on a monorepo architecture with a **NestJS** (TypeScript) backend, a **Next.js** (App Router) frontend, a **PostgreSQL** database accessed via **Prisma ORM**, and integrated AWS services including **Amazon S3**, **Amazon SES**, **Amazon Cognito**, **Amazon RDS**, and **Amazon CloudFront**. The objective is to deliver a fully functional, scalable system that meets foundational security standards in a cloud environment.

---

### 2. Problem Statement

#### Background

The residential rental market still relies heavily on informal channels: Facebook groups, messaging apps, physical flyers, and real estate brokers. The process from listing a property to signing a lease is typically lengthy and opaque, creating friction for both landlords and tenants.

Specific pain points include:

- **For landlords**: No centralized tool to manage multiple properties; vacancy status, viewing schedules, and contracts must be tracked manually through spreadsheets or personal notes.
- **For tenants**: No multi-criteria search filters (price, area, location, amenities); no mechanism to confirm viewing appointments or track the status of rental applications.
- **Regarding data security**: Contracts and personal information are often exchanged through unsecured channels, creating potential data exposure risks.

#### Proposed Solution

The system addresses each of these issues through a centralized web platform where:

- Landlords have a dashboard to manage all properties, viewing schedules, and contracts.
- Tenants have a search interface with multi-criteria filters, online booking, and real-time application status tracking.
- Authentication and authorization ensure each role can only access data within its permitted scope.
- Critical notifications (appointment confirmations, contract updates) are sent automatically via email.

---

### 3. Solution Architecture

The system is organized as a **monorepo**, with clear separation between the backend, frontend, and a shared library (`@shared/types`).

#### High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                         │
│              Next.js App (App Router, SSR/CSR)              │
│         React Components · Zustand · Axios Interceptor       │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS / REST API
┌────────────────────────▼────────────────────────────────────┐
│                       Backend Layer                         │
│              NestJS (TypeScript · Modular DI)               │
│  Auth · Property · Booking · Contract · Notification · Admin │
│         JWT Guards · Role Guards · Class-Validator           │
└────┬──────────────┬──────────────┬──────────────────────────┘
     │              │              │
┌────▼────┐   ┌─────▼─────┐  ┌────▼────────────────────────┐
│ AWS RDS │   │ Amazon S3 │  │  AWS Services                │
│PostgreSQL│  │+CloudFront│  │  SES · Cognito               │
└─────────┘   └───────────┘  └─────────────────────────────┘
```

#### AWS Services Used

| Service | Role in the System |
|---------|--------------------|
| **Amazon RDS (PostgreSQL)** | Primary database storing all business data |
| **Amazon S3** | Property image storage; accessed via presigned URLs |
| **Amazon CloudFront** | CDN distributing images from S3, reducing client latency |
| **Amazon SES** | Sending account verification and business notification emails |
| **Amazon Cognito** | User authentication management, integrated with the JWT flow |

#### Backend Module Design

The NestJS backend is organized into independent modules, each responsible for a distinct business domain:

- **Auth Module**: Registration, login, token refresh, Google OAuth2, Refresh Token Rotation.
- **Property Module**: Property CRUD, S3 image upload, multi-criteria filter and search.
- **Booking Module**: Viewing appointment management with a state machine (PENDING → CONFIRMED/CANCELLED).
- **Contract Module**: Rental contract creation and storage.
- **Notification Module**: Email notifications via SES when booking status changes.
- **Admin Module**: Statistics dashboard, property listing approval/rejection.

---

### 4. Technical Implementation

#### Technology Stack and Rationale

**Backend — NestJS (TypeScript)**

NestJS was selected for its clearly modular architecture, which suits a system with multiple business domains. Its Dependency Injection mechanism facilitates unit testing and enforces separation of concerns between layers (Controller → Service → Repository). TypeScript provides end-to-end type safety, reducing runtime errors, particularly when integrating with Prisma ORM.

**Frontend — Next.js (App Router)**

Next.js with the App Router allows flexible use of Server Components (faster initial load, better SEO) alongside Client Components (interactive UI). An Axios interceptor handles automatic JWT refresh, providing a seamless user experience even when access tokens expire.

**Database — PostgreSQL + Prisma ORM**

PostgreSQL is a natural fit for the system's relational data model (User ↔ Property ↔ Booking ↔ Contract). Prisma provides a type-safe database client, schema migrations, and a query builder — eliminating manual SQL errors and accelerating development.

#### Key Technical Problems Addressed

**Authentication and Authorization**

The system uses JWT with two token types: short-lived Access Tokens (15 minutes) and long-lived Refresh Tokens (7 days). Refresh Token Rotation ensures each token is single-use, preventing replay attacks. AuthGuard and RolesGuard are applied at the controller level to enforce role-based access control (TENANT/LANDLORD/ADMIN).

**N+1 Query Problem**

During development, querying a list of properties with their associated images and landlord information initially triggered N+1 queries — with 20 properties, the system was executing approximately 50 separate database queries. This was resolved by applying Prisma `include` to eager-load relations in a single JOIN query, combined with database indexing on frequently filtered columns (price, location, status). The result was a reduction in P95 response time from approximately 800ms to 120ms.

**Race Condition**

The scenario where two users simultaneously book the same viewing slot was handled using Pessimistic Locking (`SELECT FOR UPDATE` within a Prisma `$transaction`). This ensures only one booking is created successfully; concurrent requests receive a meaningful error response rather than producing inconsistent data.

**Property Image Storage**

Images are uploaded directly to Amazon S3 and served through CloudFront CDN. The frontend accesses images via time-limited presigned URLs rather than public URLs — reducing the risk of unauthorized access and providing control over bandwidth usage.

#### Development Phases

| Week | Phase | Key Deliverables |
|------|-------|-----------------|
| 1–2 | Preparation & Research | Onboarding, AWS fundamentals, Free Tier setup, Credits |
| 3 | Analysis & Design | Business domain, Use Cases, architecture, tech stack |
| 4 | Foundation | Database schema, Auth, S3/SES/Cognito integration |
| 5 | Feature Development | Property, Booking, Contract, Notification, Frontend |
| 6 | Performance Optimization | N+1 Query fix, indexing, cursor-based pagination |
| 7 | Security & Hardening | Race Condition, Transactions, OWASP, Token Rotation |
| 8 | Completion & Handover | E2E testing, CloudFront, documentation, product demo |

---

### 5. Timeline & Milestones

```
Week 1–2  │ ████ Preparation & AWS Fundamentals
Week 3    │ ██   Business Analysis & System Design
Week 4    │ ███  Database · Auth · AWS Integration
Week 5    │ ████ Core Feature Development · Frontend
Week 6    │ ██   N+1 Query Optimization & DB Performance
Week 7    │ ███  Race Condition · Security · Hardening
Week 8    │ ███  Completion · Testing · Docs · Handover
```

**Key Milestones:**

- **Week 2**: $200 AWS Credits received; AWS Budget configured.
- **Week 4**: Full authentication flow operational (register → verify → login); S3 upload functional.
- **Week 5**: Internal demo of core business features with mentor.
- **Week 6**: P95 response time improvement confirmed after N+1 optimization.
- **Week 8**: Final product demo; source code and documentation handover complete.

---

### 6. Budget Estimation

Operating costs during the development and demo environment are managed through the **AWS Free Tier** and the **$200 USD AWS Credits** received through the student support program.

#### AWS Infrastructure Costs (Development Environment Estimate)

| Service | Configuration | Estimated Cost |
|---------|--------------|----------------|
| Amazon RDS (PostgreSQL) | db.t3.micro, 20 GB SSD, Single-AZ | ~$15/month |
| Amazon S3 | ~5 GB storage, ~10,000 requests/month | ~$0.15/month |
| Amazon CloudFront | ~10 GB transfer/month | ~$0.85/month |
| Amazon SES | ~500 emails/month (sandbox) | $0 (Free Tier) |
| Amazon Cognito | <50,000 MAU | $0 (Free Tier) |
| **Total Estimate** | | **~$16/month** |

> All costs during the 8-week internship period fall within the $200 AWS Credits allocation, resulting in zero actual out-of-pocket expenses.

#### Cost Control Strategy

- Create **AWS Budget** with email alerts at $50 and $100 thresholds.
- Use **RDS Single-AZ** instead of Multi-AZ in the development environment to reduce costs.
- Configure **S3 Lifecycle Policy** to automatically delete test upload files after 30 days.
- Stop the RDS instance outside working hours when access is not required.

---

### 7. Risk Assessment

#### Risk Matrix

| Risk | Impact | Probability | Mitigation Strategy |
|------|--------|-------------|---------------------|
| Exceeding AWS Credits | Medium | Low | AWS Budget alerts; stop unused resources |
| Authentication security vulnerability | High | Low | Refresh Token Rotation, rate limiting, account lockout |
| Race Condition in Booking | High | Medium | Pessimistic Locking with `SELECT FOR UPDATE` |
| Database performance degradation | Medium | Medium | N+1 fix, indexing, cursor-based pagination |
| S3 presigned URL abuse | Low | Low | Short URL expiration; ownership verification before generation |

#### Contingency Plans

- **RDS failure**: Restore from automated snapshots (enabled by default on RDS).
- **S3 upload failure**: Retry logic with exponential backoff at the backend layer.
- **SES throttling**: Queue emails and retry — this does not block the core business workflow.
- **Cognito outage**: Fall back to the JWT-only internal Auth module flow; system remains functional.

---

### 8. Expected Outcomes

#### Technical Outcomes

By the end of the development phase, the system is expected to achieve:

- All core business workflows operating stably: registration, login, property listing, search, viewing bookings, and contract creation.
- An authentication system meeting foundational security standards: JWT Rotation, rate limiting, input validation, OWASP Top 10 compliance.
- Optimized database query performance: P95 response time under 200ms for paginated list APIs.
- Comprehensive technical documentation: README, Swagger API docs (40+ endpoints), architecture diagrams.

#### Learning Value and Skill Development

The project is structured to reflect a real-world software development environment — from business analysis and architecture design through implementation, performance optimization, security hardening, and formal handover. Core skills developed include: designing REST APIs to industry standards, integrating AWS services into production-grade applications, handling concurrency issues, and optimizing database performance — all of which have direct applicability to a software engineering career.

#### Extension Roadmap

The modular architecture of NestJS allows the system to scale without major refactoring. Potential future directions include: integrating an online payment gateway, adding real-time chat between landlords and tenants, or containerizing the entire system with Docker/ECS to support more flexible deployment on a production environment.