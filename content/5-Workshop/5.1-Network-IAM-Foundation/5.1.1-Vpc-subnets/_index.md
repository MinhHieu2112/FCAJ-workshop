---
title: "VPC, subnets, security groups & Elastic IP setup"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1.1. </b> "
---

### 1. Overview

The network topology is designed for secure, high-availability connectivity:

- **Public subnet**: Houses the **EC2 instance** running Caddy Web Server as a reverse proxy alongside the NestJS Docker container.
- **Private subnet**: Houses the **Amazon RDS PostgreSQL database**, isolated with no public IP address, accepting inbound traffic solely on PostgreSQL port 5432 from the EC2 security group.
- **Elastic IP (EIP)**: A static public IPv4 address assigned to EC2, ensuring the **DuckDNS** domain (`nestro.duckdns.org`) consistently maps to the server.

---

### 2. Step-by-step implementation

#### Step 1: Initialize VPC and subnets

1. Open the [Amazon VPC Console](https://console.aws.amazon.com/vpc/home).
2. Select **Your VPCs** -> Click **Create VPC**.
![Create VPC](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-1.png)

3. Select **VPC and more** to generate the VPC, subnets, route tables, and Internet Gateway.
4. Configure parameters:
   - **Name tag auto-generation**: `my-app-vpc`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - **Number of Availability Zones (AZs)**: `2`
   - **Number of public subnets**: `2`
   - **Number of private subnets**: `2`
![VPC configuration 1](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-2.png)
![VPC configuration 2](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-3.png)

5. Click **Create VPC**.
![VPC preview](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-4.png)

---

#### Step 2: Configure security groups for EC2 and RDS

1. Select **Security Groups** -> **Create security group**.
2. **Create `sg-ec2-backend` for the EC2 host:**
   - **Name**: `sg-ec2-backend`
   - **Inbound rules**:

| Type | Port | Source | Purpose |
|---|---|---|---|
| **HTTP** | `80` | `0.0.0.0/0` | Enables Caddy to satisfy ACME HTTP-01 challenges and redirect HTTP requests to HTTPS |
| **HTTPS** | `443` | `0.0.0.0/0` | Allows Vercel frontend HTTPS requests to reach `https://nestro.duckdns.org` |
| **SSH** | `22` | `My IP` or `0.0.0.0/0` | Server administration and GitHub Actions deployment |

{{% notice warning %}}
**Security rule**: Port **4000** must **NOT** be exposed in the Security Group. The Docker NestJS container communicates internally with Caddy via `localhost:4000`. All public incoming traffic must flow through Caddy on HTTPS port 443.
{{% /notice %}}

3. **Create `sg-rds-private` for Amazon RDS:**
   - **Name**: `sg-rds-private`
   - **Inbound rules**: PostgreSQL (`5432`), set **Source** to security group `sg-ec2-backend`.

---

#### Step 3: Allocate and associate an Elastic IP (EIP)

1. In the VPC Console, select **Elastic IPs** in the left menu.
2. Click **Allocate Elastic IP address** -> click **Allocate**.
3. Select the allocated Elastic IP -> **Actions** -> **Associate Elastic IP address**.
4. Set **Resource type** to `Instance`, choose your EC2 instance, and click **Associate**.
5. Note down this Elastic IP (e.g., `54.210.xx.xx`) for configuring your DuckDNS A record in Module 5.5.
