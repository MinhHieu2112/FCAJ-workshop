---
title: "System Architecture & Overview"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Monorepo Application Architecture

The **Real Estate Rental Management System** is structured as a monorepo (`pnpm workspaces`) comprising a **Next.js** frontend, a **NestJS** backend, and shared TypeScript type packages.

![System Architecture Diagram](/images/5-Workshop/5.1-Overview/architecture-diagram.png)
> 💡 **Note for Author:** *[Bổ sung hình ảnh sơ đồ kiến trúc hệ thống kết nối giữa Next.js, NestJS và các dịch vụ AWS]*

#### Key AWS Services & Integration Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                         │
│              Next.js App (App Router, SSR/CSR)              │
│        React Components · Redux Toolkit / RTK Query         │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS REST API + WebSocket
┌────────────────────────▼────────────────────────────────────┐
│                       Backend Layer                         │
│              NestJS (TypeScript · Modular DI)               │
│   Property · Application · Lease · Tenant · Manager ·       │
│   Message (Chat Real-time) · Notification · Location        │
│         AuthGuard · RolesGuard · Class-Validator            │
└────┬──────────────┬──────────────┬──────────────┬───────────┘
     │              │              │              │
┌────▼────┐   ┌─────▼─────┐  ┌─────▼─────┐  ┌─────▼───────────┐
│ AWS RDS │   │ Amazon S3 │  │ Amazon SES│  │ Amazon Location │
│PostgreSQL│  │ (property │  │ (email    │  │ Service         │
│+ PostGIS│   │ images)   │  │ notify)   │  │ (Geocode & Map) │
└─────────┘   └───────────┘  └───────────┘  └─────────────────┘
                     Amazon Cognito (User Pool & App Client)
                     — Client authentication & JWT token issuance —
```

#### Core Cloud Workflows Covered in This Workshop

1. **User Authentication & Authorization**:
   - Users authenticate against **Amazon Cognito User Pool**.
   - Cognito issues Access Tokens and ID Tokens containing custom claims (`custom:role`).
   - The NestJS backend verifies incoming Bearer JWT tokens using `AuthGuard` and enforces role access (`TENANT` vs `MANAGER`) using `RolesGuard`.

2. **Media Upload with S3 Presigned URLs**:
   - Rather than streaming large image files through the NestJS server, the backend requests a short-lived **Presigned PUT URL** from **Amazon S3**.
   - The Next.js frontend uploads the binary image directly to S3, reducing server workload and bandwidth.

3. **Geocoding & Spatial Mapping**:
   - When a landlord creates a property listing, text addresses are converted into geographic coordinates `(Latitude, Longitude)` via **Amazon Location Service Place Index**.
   - Coordinates are stored in **Amazon RDS (PostgreSQL + PostGIS)** for radius distance queries.
