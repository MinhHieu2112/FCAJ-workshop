---
title: "Triển khai máy chủ & backend"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Tổng quan

Trong phần này, bạn sẽ thiết lập môi trường máy chủ và triển khai container ứng dụng backend NestJS lên dịch vụ tính toán đám mây:

- Một **Amazon EC2** instance có cài đặt **Docker** và **Docker Compose** runtime nằm trong mạng VPC.
- Container dịch vụ **NestJS Backend** được cấu hình biến môi trường, chạy các lệnh migrate cơ sở dữ liệu **Prisma** và phục vụ REST/GraphQL API.

#### Các bước thực hiện

1. [Khởi tạo EC2 instance & cài đặt Docker runtime](5.3.1-ec2-docker/)
2. [Triển khai ứng dụng NestJS backend](5.3.2-nestjs-app-deploy/)
