---
title: "Week 4 Worklog"
date: 2026-07-13
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Design the database and define the Prisma schema for the system.
* Build the user authentication and authorization module.
* Integrate the database and set up file storage with AWS S3.
* Integrate AWS services: S3, SES, and Cognito into the backend.
* Test and fix built features.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Design the database and API: <br>&emsp; + Draw ERD (Entity Relationship Diagram) for the entire system <br>&emsp; + Define entities: User, Property, Booking, Contract, Payment, Notification <br>&emsp; + Write Prisma schema: models, relations, enums <br>&emsp; + Design detailed API endpoints (method, path, request/response body) <br>&emsp; + Create migrations and seed data | 13/07/2026 | <https://www.prisma.io/docs/> |
| Tue | - Build user authentication module: <br>&emsp; + Implement JWT-based authentication (Access Token + Refresh Token) <br>&emsp; + Build Auth module: register, login, logout, refresh token <br>&emsp; + Implement bcrypt for password hashing <br>&emsp; + Write AuthGuard and RolesGuard for authorization <br>&emsp; + Implement Google OAuth2 login (Passport.js) | 14/07/2026 | <https://docs.nestjs.com/security/authentication> |
| Wed | - Integrate database and file storage: <br>&emsp; + Connect Prisma with PostgreSQL (local + AWS RDS) <br>&emsp; + Implement repository pattern for each module <br>&emsp; + Integrate AWS S3: upload property images, generate presigned URLs <br>&emsp; + Configure Multer for handling file uploads from the client | 15/07/2026 | <https://docs.aws.amazon.com/s3/> |
| Thu | - Integrate AWS services into the system: <br>&emsp; + **AWS SES**: send account verification and booking notification emails <br>&emsp; + **AWS Cognito**: integrate User Pool, configure App Client <br>&emsp; + **AWS S3**: finalize upload/delete image logic, organize folders by property ID <br>&emsp; + Write unit tests for the Auth module | 16/07/2026 | <https://docs.aws.amazon.com/ses/> |
| Fri | - Test and fix features: <br>&emsp; + Test all API endpoints with Postman/Thunder Client <br>&emsp; + Fix bugs related to token refresh and role guards <br>&emsp; + Test S3 upload with large files (multipart upload) <br>&emsp; + Code review and refactor following NestJS best practices | 17/07/2026 | |

---

### Week 4 Achievements:

* Completed the **ERD** and full **Prisma schema** for 6 core entities: User, Property, Booking, Contract, Payment, Notification; successfully ran migrations on PostgreSQL.

* Built a complete **Authentication** system:
  * JWT with Access Token (15 minutes) and Refresh Token (7 days)
  * Bcrypt password hashing
  * AuthGuard and RolesGuard correctly enforced for 3 roles: TENANT, LANDLORD, ADMIN
  * Google OAuth2 login successfully integrated

* Successfully integrated **AWS S3**: upload and delete property images, generate presigned URLs for secure client access.

* Integrated **AWS SES**: send account verification emails and booking notification emails.

* Integrated **AWS Cognito**: configured User Pool and App Client, synchronized with the NestJS Auth module.

* Tested all Authentication APIs with Postman, confirmed the full flow: register → verify email → login works correctly.

---

### Knowledge / Experience Gained:

* Gained deep understanding of JWT mechanics: short-lived Access Tokens (stateless) combined with long-lived Refresh Tokens balance security and user experience.
* Learned how to design Prisma schemas with 1-N and N-N relationships, and use enums to manage state (BookingStatus, PropertyStatus).
* Learned how to integrate AWS SDK v3 into NestJS: configuring credentials, region, and using commands (PutObjectCommand, GetObjectCommand).
* Understood the difference between public S3 URLs and presigned URLs — presigned URLs are more secure and have expiration times.
* Debugging experience: CORS errors when integrating Cognito required correctly configuring the App Client callback URL.
