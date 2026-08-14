---
title: "Rentiful - Rental property management application"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 2.1. </b> "
---

### 1. Problem statement

The current residential rental market relies heavily on informal channels such as social media groups, direct messages, or third-party brokers. The workflow from property search to contract signing is often fragmented, lacks dedicated management tools, and presents information transparency risks for both landlords and tenants.

Key limitations include:
- Landlords and property managers: Lack a centralized dashboard to track property portfolios, room availability, incoming applications, and lease history.
- Tenants: Lack intuitive map-integrated search tools with multi-criteria filtering (price range, bedroom count, amenities); lack centralized application status tracking and communication channels.
- Communication and notifications: Exchanges via personal messaging apps lead to scattered information; lack automated email alerts when rental application or lease contract status changes.

---

### 2. Solution
![User role authorization overview](/images/2-Proposal/Role.png)
The Real Estate Rental Management System was developed to address these limitations through a centralized web application platform.

Core features include:
- Property portfolio management: Landlords can create listings, edit details, upload images, and manage property availability statuses.
- Property search and map interaction: Tenants discover properties using multi-criteria filters combined with interactive spatial maps.
- Rental application management: Tenants submit online applications; landlords receive notifications and review (approve or reject) applications on their dashboard.
- Lease creation and digital signature: Upon application approval, the system automatically generates a lease contract inside a secure database transaction and supports direct signature on the UI.
- Real-time chat: Integrated direct messaging via WebSocket connection between tenants and landlords for each property listing.
- Automated notifications: Automated email notifications sent via SMTP (or Amazon SES) upon application or lease status updates.

---

### 3. Technologies used

The system is built as a monorepo utilizing modern technologies:

- Frontend: Next.js (App Router), TypeScript, Redux Toolkit, RTK Query, and Tailwind CSS.
- Backend: NestJS, TypeScript, Socket.IO (WebSocket), and Class-Validator.
- Database: Amazon RDS PostgreSQL with PostGIS spatial extension, accessed via Prisma ORM.
- Authentication and authorization: Amazon Cognito User Pool paired with NestJS AuthGuard and RolesGuard (`TENANT` and `MANAGER` roles).
- Media storage: Amazon S3 (direct browser uploads via short-lived Presigned URLs).
- Geocoding and mapping: Amazon Location Service (address geocoding and map rendering).
- Email notification service: Automated email dispatch via SMTP (Nodemailer) / Amazon SES.
- Host server and reverse proxy: Amazon EC2 running Docker runtime and Caddy HTTP/HTTPS reverse proxy.

---

### 4. Infrastructure architecture

The system is deployed on AWS cloud infrastructure utilizing a two-tier VPC network (public and private subnets):

- Frontend Vercel (HTTPS): Next.js client app hosted on Vercel, securely communicating with the backend via DuckDNS domain name and automated SSL/TLS certificates from Caddy.
- Public Subnet tier: Static Elastic IP attached, running Caddy reverse proxy on EC2 to handle incoming HTTP/HTTPS traffic (ports 80/443) and forward requests to the NestJS backend container.
- Private Subnet tier: Amazon RDS PostgreSQL (PostGIS) provisioned in a private subnet, allowing internal connections only from the EC2 server via security group rules (`sg-rds-private`).
- Secrets and access management: Environment variables stored in AWS Secrets Manager and IAM Role (`RentifulEC2SecretManagerRole`) assigned to EC2 instance for automatic secret retrieval on startup.

Overall architecture diagram:

![System architecture](/images/5-Workshop/5.1-Overview/AWS_architect.png)

---

### 5. Estimated costs

System operational costs are managed strictly within the AWS Free Tier allocation and AWS Credits program:
![Estimated cost](/images/2-Proposal/Cost.png)
Cost management measures:
- Configured AWS Budget automated email alerts at $50 and $100 spending thresholds.
- Provisioned Single-AZ RDS instances during the development phase.
- Applied client-side debouncing to reduce geocoding API requests sent to Amazon Location Service.

---

### 6. Demo

The production application is fully deployed and accessible online:

- Production App URL: [https://real-estate-client-one-eta.vercel.app/](https://real-estate-client-one-eta.vercel.app/)

Feature screenshots:
![Property location map](/images/5-Workshop/5.5-Location-Service/5.5.4-demo.png)


