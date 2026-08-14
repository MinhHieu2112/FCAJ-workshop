---
title: "Workshop"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

#### Tổng quan

Trong bài thực hành này, bạn sẽ xây dựng và triển khai **Hệ thống quản lý bất động sản cho thuê** (Real Estate Rental Management System) với kiến trúc thực tế:

- **Frontend Next.js** – Triển khai trên **Vercel** (`https://real-estate-client-one-eta.vercel.app`) chạy trên giao thức **HTTPS**.
- **Backend NestJS** – Đóng gói container Docker chạy trên **Amazon EC2** lắng nghe nội bộ tại cổng **4000**.
- **Elastic IP & DuckDNS** – Gán Elastic IP cố định cho EC2 và trỏ tên miền **DuckDNS** (`https://nestro.duckdns.org`) về địa chỉ IP này.
- **Caddy Web Server** – Chạy làm Reverse Proxy trên EC2, tự động cấp phát và gia hạn chứng chỉ SSL/TLS (HTTPS) cho `https://nestro.duckdns.org` và chuyển tiếp lưu lượng đến backend Docker.
- **Xử lý Mixed Content & CORS** – Giải quyết triệt để lỗi Mixed Content khi frontend HTTPS gọi API, đồng thời cấu hình CORS linh hoạt trong NestJS với biến môi trường `CORS_ORIGIN`.
- **Amazon RDS (PostgreSQL + PostGIS)** – Cơ sở dữ liệu quan hệ lưu trữ dữ liệu ứng dụng và không gian tọa độ bất động sản.
- **Amazon Cognito, S3 & SES** – Xác thực người dùng, lưu trữ ảnh media qua presigned URL và gửi email tự động.
- **GitHub Actions & CloudWatch** – Tự động hóa CI/CD và giám sát hệ thống.

#### Nội dung bài lab

1. [Nền tảng mạng & phân quyền IAM](5.1-Network-IAM-Foundation/)
2. [Cấu hình & lưu trữ dữ liệu](5.2-Configuration-Storage/)
3. [Triển khai máy chủ & backend](5.3-Compute-Backend/)
4. [Bảo mật & xác thực người dùng](5.4-Security-authentication/)
5. [Định tuyến tên miền DuckDNS, Caddy HTTPS & CORS](5.5-Routing-Domain-SSL/)
6. [Tự động hóa CI/CD & giám sát](5.6-DevOps-Observability/)
7. [Dọn dẹp tài nguyên](5.7-Cleanup/)
