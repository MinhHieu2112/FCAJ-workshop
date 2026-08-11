---
title: "Configuring ALB & AWS WAF"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Step 1: Create a target group

1. Open the [EC2 Console](https://console.aws.amazon.com/ec2/home) → **Target groups** → **Create target group**.
2. Configure:
   - **Target type**: Instances
   - **Target group name**: `real-estate-backend-tg`
   - **Protocol**: HTTP | **Port**: `4000`
   - **VPC**: select your VPC
   - **Health check path**: `/health`
3. Register your EC2 instance and click **Create target group**.

#### Step 2: Create an Application Load Balancer

1. Go to **Load Balancers** → **Create load balancer** → **Application Load Balancer**.
2. Configure:
   - **Name**: `real-estate-alb`
   - **Scheme**: Internet-facing
   - **IP address type**: IPv4
   - **VPC**: select your VPC
   - **Subnets**: select both **public subnets** (public subnet A and B)
3. Under **Security groups**, select or create `sg-alb-public`:

| Type | Protocol | Port | Source |
|---|---|---|---|
| HTTP | TCP | 80 | 0.0.0.0/0 |
| HTTPS | TCP | 443 | 0.0.0.0/0 |

4. Under **Listeners and routing**, set:
   - Listener HTTP:80 → Redirect to HTTPS:443
   - Listener HTTPS:443 → Forward to `real-estate-backend-tg`
5. Click **Create load balancer**.

#### Step 3: Attach AWS WAF

1. Open the [AWS WAF Console](https://console.aws.amazon.com/wafv2/homev2).
2. Select **Web ACLs** → **Create web ACL**.
3. Configure:
   - **Name**: `real-estate-waf`
   - **Resource type**: Regional resources
   - **Region**: your deployment region
4. Add **managed rule groups**:
   - `AWSManagedRulesCommonRuleSet` – blocks common web exploits (XSS, SQLi).
   - `AWSManagedRulesAmazonIpReputationList` – blocks known malicious IPs.
5. Under **Associated AWS resources**, add your ALB (`real-estate-alb`).
6. Click **Create web ACL**.
