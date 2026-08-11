---
title: "Định tuyến, bảo mật & HTTPS"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Tổng quan

Phần này thiết lập điểm vào công khai cho backend API:

- Một **Application Load Balancer (ALB)** nhận lưu lượng HTTPS trên cổng 443 và chuyển tiếp đến EC2 backend trên cổng 4000.
- **AWS WAF** được gắn vào ALB để chặn các mẫu tấn công web phổ biến (SQL injection, XSS, rate limiting).
- Một tên miền **DuckDNS** miễn phí được trỏ về ALB, và chứng chỉ SSL/TLS **AWS Certificate Manager (ACM)** được cấp phát cho HTTPS.

#### Các bước thực hiện

1. [Cấu hình Application Load Balancer (ALB) & AWS WAF](5.3.1-alb-waf/)
2. [Cấu hình tên miền (DuckDNS) & chứng chỉ SSL/TLS](5.3.2-domain-ssl/)
