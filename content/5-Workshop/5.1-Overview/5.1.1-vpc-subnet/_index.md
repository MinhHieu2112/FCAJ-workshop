---
title: "VPC, subnets & security groups setup"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1.1. </b> "
---

#### VPC design overview

The system uses a standard **two-tier VPC** with public and private subnets across two availability zones for high availability:

| Resource | CIDR / Detail |
|---|---|
| VPC | `10.0.0.0/16` |
| Public subnet A | `10.0.1.0/24` (EC2, ALB) |
| Public subnet B | `10.0.2.0/24` (ALB secondary) |
| Private subnet A | `10.0.3.0/24` (RDS primary) |
| Private subnet B | `10.0.4.0/24` (RDS standby) |

#### Step 1: Create a VPC

1. Open the [Amazon VPC Console](https://console.aws.amazon.com/vpc/home).
2. Select **Your VPCs** → **Create VPC**.
3. Choose **VPC and more** to auto-generate subnets and route tables.
4. Set:
   - **IPv4 CIDR**: `10.0.0.0/16`
   - **Number of availability zones**: `2`
   - **Number of public subnets**: `2`
   - **Number of private subnets**: `2`
   - **NAT gateways**: `None` (for cost savings in a development environment)
5. Click **Create VPC**.

#### Step 2: Configure security groups

Create two security groups under the VPC you created:

**Security group: `sg-ec2-backend`** (attached to the EC2 instance)

| Type | Protocol | Port | Source |
|---|---|---|---|
| SSH | TCP | 22 | Your IP address |
| Custom TCP | TCP | 4000 | `sg-alb-public` (ALB security group) |

**Security group: `sg-rds-private`** (attached to the RDS instance)

| Type | Protocol | Port | Source |
|---|---|---|---|
| Custom TCP | TCP | 5432 | `sg-ec2-backend` |

{{% notice warning %}}
Do **not** expose port 5432 (PostgreSQL) to the public internet. Always restrict database access to the EC2 security group.
{{% /notice %}}
