---
title: "Cấu hình ALB & AWS WAF"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Bước 1: Tạo target group

1. Truy cập [Bảng điều khiển EC2](https://console.aws.amazon.com/ec2/home) → **Target groups** → **Create target group**.
2. Cấu hình:
   - **Target type**: Instances
   - **Target group name**: `real-estate-backend-tg`
   - **Protocol**: HTTP | **Port**: `4000`
   - **VPC**: chọn VPC của bạn
   - **Health check path**: `/health`
3. Đăng ký EC2 instance và nhấn **Create target group**.

#### Bước 2: Tạo Application Load Balancer

1. Vào **Load Balancers** → **Create load balancer** → **Application Load Balancer**.
2. Cấu hình:
   - **Name**: `real-estate-alb`
   - **Scheme**: Internet-facing
   - **IP address type**: IPv4
   - **VPC**: chọn VPC của bạn
   - **Subnets**: chọn cả hai **public subnet** (public subnet A và B)
3. Tại **Security groups**, chọn hoặc tạo `sg-alb-public`:

| Loại | Giao thức | Cổng | Nguồn |
|---|---|---|---|
| HTTP | TCP | 80 | 0.0.0.0/0 |
| HTTPS | TCP | 443 | 0.0.0.0/0 |

4. Tại **Listeners and routing**, cài đặt:
   - Listener HTTP:80 → Redirect sang HTTPS:443
   - Listener HTTPS:443 → Forward đến `real-estate-backend-tg`
5. Nhấn **Create load balancer**.

#### Bước 3: Gắn AWS WAF

1. Truy cập [Bảng điều khiển AWS WAF](https://console.aws.amazon.com/wafv2/homev2).
2. Chọn **Web ACLs** → **Create web ACL**.
3. Cấu hình:
   - **Name**: `real-estate-waf`
   - **Resource type**: Regional resources
   - **Region**: region triển khai của bạn
4. Thêm **managed rule groups**:
   - `AWSManagedRulesCommonRuleSet` – chặn các khai thác web phổ biến (XSS, SQLi).
   - `AWSManagedRulesAmazonIpReputationList` – chặn các IP độc hại đã biết.
5. Tại **Associated AWS resources**, thêm ALB (`real-estate-alb`).
6. Nhấn **Create web ACL**.
