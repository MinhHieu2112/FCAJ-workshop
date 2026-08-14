---
title: "Configuration & storage"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Overview

In this module, you will provision the application configuration management, relational database layer, and object storage service:

- **AWS Secrets Manager / SSM Parameter Store** – Secure management of database credentials, JWT secrets, and environment parameters.
- **Amazon RDS PostgreSQL** – Managed relational database with the **PostGIS** spatial extension deployed inside private subnets.
- **Amazon S3** – Object storage configured with CORS policies and **Presigned URLs** enabling direct client-side media uploads.

#### Module steps

1. [Managing configuration & secrets with Secrets Manager](5.2.1-secrets-manager/)
2. [Initializing Amazon RDS PostgreSQL (PostGIS)](5.2.2-rds-postgresql/)
3. [Media storage with Amazon S3 & presigned URLs](5.2.3-s3-presigned/)
