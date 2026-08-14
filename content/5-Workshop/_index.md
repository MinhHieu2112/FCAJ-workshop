---
title: "Workshop"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

#### Overview

In this hands-on workshop, you will build and deploy a production-grade **Real Estate Rental Management System** using an authentic deployment architecture:

- **Next.js Frontend** – Deployed on **Vercel** (`https://real-estate-client-one-eta.vercel.app`) operating natively over **HTTPS**.
- **NestJS Backend** – Packaged into a Docker container running on **Amazon EC2** listening internally on port **4000**.
- **Elastic IP & DuckDNS** – Assigned a static Elastic IP to EC2 and mapped to a free **DuckDNS** domain (`https://nestro.duckdns.org`).
- **Caddy Web Server** – Operating as a reverse proxy on EC2, automatically provisioning and renewing SSL/TLS certificates for `https://nestro.duckdns.org` and proxying incoming HTTPS traffic to port 4000.
- **Mixed Content & CORS Resolution** – Resolving browser Mixed Content blocking caused by HTTPS frontend calling HTTP backend IPs, while configuring dynamic CORS in NestJS using `CORS_ORIGIN`.
- **Amazon RDS (PostgreSQL + PostGIS)** – Managed database storing relational entities and PostGIS spatial coordinates.
- **Amazon Cognito, S3 & SES** – Managed identity pools, presigned URL media uploads, and automated transactional emails.
- **GitHub Actions & CloudWatch** – Automated CI/CD pipeline deployment and system monitoring.

#### Content

1. [Network & IAM foundation](5.1-Network-IAM-Foundation/)
2. [Configuration & storage](5.2-Configuration-Storage/)
3. [Compute & backend deployment](5.3-Compute-Backend/)
4. [Security & authentication](5.4-Security-authentication/)
5. [DuckDNS domain, Caddy HTTPS reverse proxy & CORS](5.5-Routing-Domain-SSL/)
6. [DevOps & observability](5.6-DevOps-Observability/)
7. [Resource cleanup](5.7-Cleanup/)
