---
title: "Domain (DuckDNS) & SSL/TLS certificate configuration"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Step 1: Register a free DuckDNS subdomain

1. Visit [https://www.duckdns.org](https://www.duckdns.org) and log in with your GitHub or Google account.
2. Under **Add domain**, enter a subdomain name (e.g., `real-estate-api`).
3. Click **Add domain**. You will receive a free domain: `real-estate-api.duckdns.org`.
4. Copy the **ALB DNS name** from the EC2 Console → **Load Balancers** → select your ALB → **DNS name**.
5. In DuckDNS, update the IP field:
   - If your ALB provides an IP, enter it directly.
   - If it provides a hostname (e.g., `real-estate-alb-xxx.us-east-1.elb.amazonaws.com`), use a CNAME approach. Since DuckDNS only supports A records, resolve the ALB hostname to an IP and enter that. (For production, use Route 53 with an alias record instead.)

#### Step 2: Request an SSL/TLS certificate with ACM

1. Open the [AWS Certificate Manager Console](https://console.aws.amazon.com/acm/home).
2. Click **Request a certificate** → **Request a public certificate**.
3. Enter your fully qualified domain name (FQDN), for example:
   - `real-estate-api.duckdns.org`
4. Choose **DNS validation** as the validation method.
5. Click **Request**.
6. Copy the **CNAME name** and **CNAME value** shown in the certificate details.
7. In your DuckDNS domain settings, add a CNAME record using the provided values to complete validation.

#### Step 3: Attach the certificate to the ALB HTTPS listener

1. Open the EC2 Console → **Load Balancers** → select your ALB.
2. Select the **Listeners** tab → click on the HTTPS:443 listener → **Edit**.
3. Under **Default SSL/TLS certificate**, click **Add certificate** and select the ACM certificate you just created.
4. Click **Save changes**.

{{% notice tip %}}
ACM certificates attached to ALB are **automatically renewed** before expiry. No manual renewal is needed.
{{% /notice %}}
