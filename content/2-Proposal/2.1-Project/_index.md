---
title: "Live project application"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 2.1. </b> "
---

#### Live application demo

The **Real Estate Rental Management System** is fully deployed and accessible online. You can interact with the live client application hosted on Vercel and connected to the AWS cloud backend:

{{% notice tip %}}
**Live Production URL:** [https://real-estate-client-one-eta.vercel.app/](https://real-estate-client-one-eta.vercel.app/)
{{% /notice %}}


#### System architecture & technology stack

The project is architected as a monorepo (`pnpm workspaces`) combining a modern frontend, a robust backend REST & WebSocket service, and enterprise AWS cloud services.

![System architecture](/images/2-Proposal/AWS_architect.png)

#### Technology stack breakdown

| Layer | Technologies & libraries | Purpose |
|---|---|---|
| **Frontend** | Next.js 14 (App Router), TypeScript, Redux Toolkit / RTK Query, Tailwind CSS | High-performance user interface with server-side rendering (SSR) and client state management |
| **Backend** | NestJS, TypeScript, Socket.IO, Class-Validator | Modular RESTful API and WebSocket gateway for real-time communication |
| **Database & ORM** | Amazon RDS PostgreSQL, PostGIS, Prisma ORM | Relational data persistence with spatial geography querying (`ST_DWithin`) |
| **Authentication** | Amazon Cognito User Pool, AWS Amplify SDK | User identity management, JWT token issuance, and role-based authorization |
| **Media Storage** | Amazon S3, AWS SDK v3 (Presigned URLs) | Secure, direct browser-to-S3 image uploads using short-lived presigned URLs |
| **Location & Maps** | Amazon Location Service | Address geocoding, autocomplete suggestions, and interactive map tile rendering |
| **Notifications** | Amazon SES (Simple Email Service) | Automated transactional email notifications for application status updates |

---

#### Core application modules

#### 1. Interactive property search & spatial map

Tenants can discover rental properties using multi-criteria filters (price range, bedroom count, amenities) combined with interactive location search.

- **Geocoding integration:** Address strings entered during listing creation are automatically converted to geographic coordinates `(Latitude, Longitude)` via **Amazon Location Service**.
- **Spatial queries:** PostGIS spatial indexes allow searching properties within a specified radius from any point on the map.

![Location Service Demo](/images/5-Workshop/5.5-Location-Service/5.5.4-demo.png)

#### 2. Rental application & digital contract workflow

The application automates the full lifecycle of a tenancy agreement:

1. **Submission:** Tenants submit rental applications with personal details and proposed move-in dates.
2. **Review:** Managers receive real-time notifications in their dashboard to approve or reject applications.
3. **Lease generation:** When approved, the system generates a lease contract inside a database transaction (`prisma.$transaction`).
4. **Digital signature:** Managers sign the contract digitally on a canvas pad. Once signed, the contract is locked and sent to the tenant for final acceptance.

#### 3. Real-time messaging & email notifications

- **In-app chat:** Built-in WebSocket messaging allows tenants and landlords to communicate directly regarding specific property listings.
- **Email alerts:** Automated emails are dispatched via **Amazon SES** whenever an application state changes (e.g., application received, approved, or lease contract generated).

---

#### Technical highlights & performance optimizations

- **N+1 query elimination:** Eager loading with Prisma `include` reduced API response latencies from ~800ms to ~120ms (P95).
- **Concurrency control:** Database transactions combined with pessimistic locking (`SELECT ... FOR UPDATE`) prevent double-booking or race conditions during simultaneous application approvals.
- **Direct S3 upload:** Offloads media file transfer from the backend server to S3 presigned URLs, reducing server bandwidth consumption by 90%.
