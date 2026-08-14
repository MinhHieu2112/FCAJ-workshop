---
title: "Nền tảng mạng & phân quyền IAM"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc monorepo và luồng triển khai thực tế

**Hệ thống quản lý bất động sản cho thuê** được tổ chức theo mô hình monorepo (`pnpm workspaces`):
- **Frontend Next.js** triển khai độc lập trên **Vercel** tại `https://real-estate-client-one-eta.vercel.app`.
- **Backend NestJS** chạy trong container Docker trên **Amazon EC2** lắng nghe tại cổng nội bộ `4000`.
- **Caddy Web Server** làm Reverse Proxy tự động hóa SSL/TLS cho tên miền `https://nestro.duckdns.org` trỏ về Elastic IP của EC2.

![Sơ đồ kiến trúc hệ thống](/images/5-Workshop/5.1-Overview/AWS_architect.png)

#### Các luồng xử lý đám mây chính

1. **Xác thực & phân quyền người dùng**: Giao diện Vercel gọi API đến `https://nestro.duckdns.org`. Caddy nhận HTTPS request và chuyển tiếp đến NestJS backend. NestJS xác thực JWT Access Token do **Amazon Cognito User Pool** cấp phát và kiểm tra quyền (`TENANT` / `MANAGER`) bằng `AuthGuard` và `RolesGuard`.

2. **Quản lý media với S3 presigned URL**: NestJS yêu cầu **Amazon S3** phát hành Presigned PUT URL. Frontend trên Vercel tải trực tiếp hình ảnh lên S3 mà không đi qua EC2 backend.

3. **Chuyển đổi địa chỉ & bản đồ tương tác**: Tọa độ được geocode bằng **Amazon Location Service** và lưu trữ dưới dạng PostGIS trong **Amazon RDS PostgreSQL**.

#### Các bước thực hiện

1. [Thiết lập VPC, Subnets, Security Groups & Elastic IP](5.1.1-Vpc-subnets/)
2. [Phân quyền IAM role cho EC2 & dịch vụ](5.1.2-IAM-roles-policies/)
