---
title: "Security, authentication & integrated services"
date: 2026-08-06
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Authentication & authorization architecture overview

In this module, you will set up **Amazon Cognito User Pool** to manage user registration, sign-in, and JSON Web Token (JWT) issuance.

The **Real Estate Rental Management System** enforces role-based access control (RBAC) across two primary roles:
- **`TENANT`**: Can search property listings, submit lease applications, and communicate with property managers.
- **`MANAGER`**: Can create and edit listings, upload property images, review applications, and issue lease contracts.

```
┌──────────────┐          1. Authenticate (User/Pass)         ┌──────────────────────┐
│              ├─────────────────────────────────────────────►│                      │
│   Next.js    │          2. Returns Access Token (JWT)       │ Amazon Cognito       │
│   Client     │◄─────────────────────────────────────────────┤ User Pool            │
│              │                                              └──────────────────────┘
└──────┬───────┘
       │ 3. Send REST request with Bearer JWT
       ▼
┌────────────────────────────────────────────────────────────────────────────────────┐
│ NestJS backend server                                                              │
│  ┌───────────────────────┐   4. Verify Token    ┌───────────────────────────────┐  │
│  │ AuthGuard             ├─────────────────────►│ Cognito JWKS / PublicKey      │  │
│  └───────────┬───────────┘                      └───────────────────────────────┘  │
│              │ 5. Extract payload (`custom:role`)                                  │
│              ▼                                                                     │
│  ┌───────────────────────┐   6. Validate Role   ┌───────────────────────────────┐  │
│  │ RolesGuard            ├─────────────────────►│ Controller Method (@Roles)    │  │
│  └───────────────────────┘                      └───────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────────────────┘
```

#### Module steps

1. [Initializing Amazon Cognito User Pool](5.4.1-cognito-auth/)
2. [Integrating NestJS AuthGuard & RolesGuard](5.4.2-nestjs-guards/)
3. [Geocoding & mapping with Amazon Location Service](5.4.3-location-service/)
4. [Transactional email notifications with SMTP](5.4.4-ses-email/)
