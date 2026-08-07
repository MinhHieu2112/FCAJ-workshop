---
title: "Blog 1"
date: 2026-08-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# AUTHENTICATION WITH AWS COGNITO

During the development of the Real Estate Rental Management System, our team chose **Amazon Cognito** to handle user authentication instead of building a custom JWT-based authentication solution. Beyond enabling user sign-in, the integration also had to address role-based access control (RBAC) for two different user roles: **Manager** and **Tenant**.

One of the main challenges was determining which token should be used when communicating with the Backend. Initially, only the **Access Token** was sent with each request. However, the Access Token did not include the `custom:role` attribute configured in Amazon Cognito. As a result, the `RolesGuard` could not identify the user's role, causing many protected endpoints to return **401 Unauthorized** or **403 Forbidden** responses.

After several rounds of research, testing, and optimization, the authentication and authorization workflow was refined as follows:

- Use the **Access Token** as the Bearer Token to authenticate every request sent from the Frontend to the Backend through `JwtAuthGuard`.
- Extract the user's role (`custom:role`) from the **ID Token** on the Frontend and pass it to the Backend through `X-User-Role` or the token payload for authorization.
- Implement a multi-layer validation mechanism in `JwtAuthGuard`, which prioritizes reading the role from the token payload, then falls back to querying the database via Prisma, and finally checks the `X-User-Role` header when necessary.
- Apply `RolesGuard` to verify whether the authenticated user has sufficient permissions (**Manager** or **Tenant**) before accessing protected API endpoints.
- Configure CORS in the NestJS Backend to allow the `X-User-Role` header, preventing Preflight (`OPTIONS`) request issues when communicating with the Frontend.
- Use `fetchAuthSession()` from Amplify v6 to automatically manage the user session and refresh expired Access Tokens, providing a seamless user experience without requiring users to sign in again while the Refresh Token remains valid.

## Architecture Overview

![Overview](/images/3-BlogsPosted/Cognito_Auth_Architecture.png)

## References

- https://docs.aws.amazon.com/cognito/
- https://docs.aws.amazon.com/amplify/
- https://docs.nestjs.com/security/authentication
- https://docs.nestjs.com/security/authorization