---
title: "Routing, security & HTTPS"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Overview

This module sets up the public entry point to the backend API:

- An **Application Load Balancer (ALB)** receives HTTPS traffic on port 443 and forwards it to the EC2 backend on port 4000.
- **AWS WAF** is attached to the ALB to block common web attack patterns (SQL injection, XSS, rate limiting).
- A free **DuckDNS** domain is pointed to the ALB, and an **AWS Certificate Manager (ACM)** SSL/TLS certificate is provisioned for HTTPS.

#### Module steps

1. [Configuring Application Load Balancer (ALB) & AWS WAF](5.3.1-alb-waf/)
2. [Configuring domain (DuckDNS) & SSL/TLS certificate](5.3.2-domain-ssl/)
