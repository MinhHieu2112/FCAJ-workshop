---
title: "Authentication & authorization with Amazon Cognito"
date: 2026-08-06
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Authentication & authorization overview

In this module, you will set up **Amazon Cognito User Pool** to manage user registrations, authentication flows, and issue JSON Web Tokens (JWT).

In the **Real Estate Rental Management System**, users have one of two primary roles:
- **`TENANT`**: Can search for properties, submit rental applications, and message landlords.
- **`MANAGER`**: Can create and edit property listings, upload images, review incoming applications, and issue leases.

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

1. [Initialize Amazon Cognito User Pool](5.4.1-cognito-setup/)
2. [Integrate NestJS AuthGuard & RolesGuard](5.4.2-nestjs-guards/)
