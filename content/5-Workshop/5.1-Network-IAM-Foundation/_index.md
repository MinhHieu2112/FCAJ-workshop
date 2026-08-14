---
title: "Network & IAM foundation"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Monorepo system architecture & deployment layout

The **Real Estate Rental Management System** is organized as a monorepo (`pnpm workspaces`):
- **Next.js Frontend** is deployed independently on **Vercel** at `https://real-estate-client-one-eta.vercel.app`.
- **NestJS Backend** runs inside a Docker container on an **Amazon EC2** instance listening on internal port `4000`.
- **Caddy Web Server** operates as a reverse proxy providing automated SSL/TLS termination for `https://nestro.duckdns.org` pointing to the EC2 Elastic IP.

![System architecture diagram](/images/5-Workshop/5.1-Overview/AWS_architect.png)

#### Core cloud workflows

1. **Authentication & authorization flow**: The Vercel frontend issues API calls to `https://nestro.duckdns.org`. Caddy receives HTTPS traffic on port 443 and proxies requests to the NestJS backend container. NestJS verifies Bearer JWT tokens issued by **Amazon Cognito User Pool** and enforces role restrictions (`TENANT` / `MANAGER`) using `AuthGuard` and `RolesGuard`.

2. **Media management with S3 presigned URLs**: NestJS generates short-lived Presigned PUT URLs from **Amazon S3**. The Vercel frontend uploads images directly to S3.

3. **Geocoding & spatial mapping**: Addresses are geocoded via **Amazon Location Service** and stored as spatial geometries in **Amazon RDS (PostgreSQL + PostGIS)**.

#### Module steps

1. [Setting up VPC, subnets, security groups & Elastic IP](5.1.1-Vpc-subnets/)
2. [Configuring IAM roles for EC2 & services](5.1.2-IAM-roles-policies/)
