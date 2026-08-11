---
title: "Initializing Amazon RDS PostgreSQL (PostGIS) in a private subnet"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.2.1. </b> "
---

#### Why RDS in a private subnet?

Placing the database in a **private subnet** ensures it is never directly reachable from the internet. Only EC2 instances in the same VPC (via security group rules) can establish a connection.

#### Step 1: Create a DB subnet group

1. Open the [Amazon RDS Console](https://console.aws.amazon.com/rds/home).
2. Select **Subnet groups** → **Create DB subnet group**.
3. Enter:
   - **Name**: `real-estate-rds-subnet-group`
   - **VPC**: select the VPC created in section 5.1.1.
   - **Subnets**: select both **private subnets** (private subnet A and private subnet B).
4. Click **Create**.

#### Step 2: Launch an RDS PostgreSQL instance

1. In the RDS Console, select **Databases** → **Create database**.
2. Choose **Standard create** and select **PostgreSQL**.
3. Configure:

| Setting | Value |
|---|---|
| DB instance identifier | `real-estate-rental-db` |
| Master username | `postgres` |
| Master password | (use a strong password, save in Secrets Manager) |
| DB instance class | `db.t3.micro` (Free Tier eligible) |
| Storage | 20 GB SSD (gp2) |
| Multi-AZ deployment | No (development environment) |
| VPC | Select your VPC |
| DB subnet group | `real-estate-rds-subnet-group` |
| Public access | **No** |
| VPC security group | `sg-rds-private` |
| Initial database name | `rental_db` |

4. Click **Create database**.

#### Step 3: Enable PostGIS extension

Once the database is running, connect via your EC2 instance and run:

```sql
-- Connect to rental_db
\c rental_db

-- Enable PostGIS spatial extensions
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology;

-- Verify installation
SELECT PostGIS_Version();
```

#### Step 4: Add the database URL to environment variables

Update `apps/server/.env` with the RDS endpoint:

```env
DATABASE_URL="postgresql://postgres:<password>@<rds-endpoint>:5432/rental_db?schema=public"
```

{{% notice tip %}}
Find your RDS endpoint under **Databases** → select your DB instance → **Connectivity & security** → **Endpoint**.
{{% /notice %}}
