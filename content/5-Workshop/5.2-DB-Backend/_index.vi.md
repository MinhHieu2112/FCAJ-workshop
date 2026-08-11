---
title: "Triển khai cơ sở dữ liệu & server backend"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Tổng quan

Trong phần này, bạn sẽ khởi tạo tầng tính toán và dữ liệu cốt lõi của hệ thống:

- Một **Amazon RDS PostgreSQL** với phần mở rộng **PostGIS** nằm trong private subnet.
- Một **Amazon EC2** instance có Docker runtime trong public subnet để chạy container NestJS backend.

#### Các bước thực hiện

1. [Khởi tạo Amazon RDS PostgreSQL (PostGIS) trong private subnet](5.2.1-rds-postgresql/)
2. [Khởi tạo EC2 instance & cài đặt Docker runtime](5.2.2-ec2-docker/)
