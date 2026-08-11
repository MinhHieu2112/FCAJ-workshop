---
title: "System architecture & environment preparation"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Monorepo application architecture

The **Real Estate Rental Management System** is structured as a monorepo (`pnpm workspaces`) comprising a **Next.js** frontend, a **NestJS** backend, and shared TypeScript type packages (`@shared/types`).

![System architecture diagram](/images/5-Workshop/5.1-Overview/AWS_architect.png)

#### Core cloud workflows

1. **User authentication & authorization**: Users authenticate through **Amazon Cognito User Pool**. Cognito issues Access Tokens containing custom role claims (`custom:role`). The NestJS backend verifies Bearer JWT tokens using `AuthGuard` and enforces role-based access (`TENANT` vs `MANAGER`) using `RolesGuard`.

2. **Media upload with S3 presigned URLs**: Rather than streaming large image files through the NestJS server, the backend requests a short-lived **Presigned PUT URL** from **Amazon S3**. The Next.js frontend uploads images directly to S3, reducing server workload and bandwidth.

3. **Geocoding & spatial mapping**: Text addresses entered during property creation are sent to **Amazon Location Service** for conversion to geographic coordinates `(Latitude, Longitude)`. Coordinates are stored in **Amazon RDS (PostgreSQL + PostGIS)** for radius-based spatial queries.

#### Module steps

1. [Setting up VPC, public/private subnets & security groups](5.1.1-vpc-subnet/)
2. [Configuring IAM roles for EC2 & services](5.1.2-iam-roles/)
