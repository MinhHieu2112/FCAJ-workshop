---
title: "Initializing Amazon RDS PostgreSQL (PostGIS)"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2.2. </b> "
---

#### Why RDS in a private subnet?

Placing the database in a **private subnet** ensures it is never directly accessible from the internet. Only EC2 instances within the same VPC (via security group rules) can establish connections.

#### Step 1: Create DB subnet group

1. Access the [Amazon RDS console](https://console.aws.amazon.com/rds/home).
2. Select **Subnet groups** -> **Create DB subnet group**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Create-db-subnet-group.png)
3. Enter:
   - **Name**: `real-estate-rds-subnet-group`
   - **Description**: `DB subnet group for real-estate-rental-db`
   - **VPC**: Select the VPC created in step 5.1.1.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Information.png)
4. Under the Add subnets section:
   - **Availability Zones**: Select the AZs corresponding to your private subnets (for example: select us-east-1a and us-east-1b).
   - **Subnets**: In the Select subnets field, select the exact 2 IDs of Private Subnet A and Private Subnet B created previously.
(Note: After selecting, the list of selected subnets will appear in the Subnets selected table below).
5. Click **Create**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Add-subnet.png)

#### Step 2: Initialize RDS PostgreSQL instance

1. In the RDS Console, select **Databases** -> **Create database**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-1.png)
2. Select **PostgreSQL** and choose **Full Configuration**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-2.png)
3. Select **Dev/test** and choose **Single AZ**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-3.png)
4. Configure settings:
- Select the database version (**Engine version**)
- Set the DB name (**DB instance identifier**)
- Select **Self managed** to set up your own database credentials
- Enter credential settings: **master username**, **master password**
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-4.png)
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-5.png)
5. Configure instance and storage settings:
- Select **Burstable classes** basic tier to save costs
- Select DB instance class **db.t3.micro**
- For storage type, select **SSD gp3** and set **20 GiB**
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-6.png)
6. Turn off **Autoscaling** and click **Create database**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-7.png)

#### Step 3: Enable PostGIS extensions

Once the database finishes initializing, connect via your EC2 instance and run:

```sql
-- Connect to rental_db
\c rental_db

-- Enable PostGIS spatial extensions
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology;

-- Verify successful installation
SELECT PostGIS_Version();
```

#### Step 4: Add database URL to environment variables

Update `apps/server/.env` with the RDS endpoint:

```env
DATABASE_URL="postgresql://postgres:<password>@<rds-endpoint>:5432/rental_db?schema=public"
```
