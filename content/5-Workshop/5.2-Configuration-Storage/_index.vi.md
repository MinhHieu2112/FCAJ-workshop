---
title: "Cấu hình & lưu trữ dữ liệu"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Tổng quan

Trong phần này, bạn sẽ thiết lập tầng cấu hình bí mật, cơ sở dữ liệu quan hệ và dịch vụ lưu trữ media cho hệ thống:

- **AWS Secrets Manager / SSM Parameter Store** – Quản lý an toàn các chuỗi kết nối, khóa bí mật JWT và biến môi trường.
- **Amazon RDS PostgreSQL** tích hợp phần mở rộng không gian **PostGIS** nằm trong private subnet.
- **Amazon S3** kèm cấu hình CORS và cơ chế **Presigned URL** giúp ứng dụng client tải ảnh bất động sản trực tiếp lên đám mây.

#### Các bước thực hiện

1. [Quản lý cấu hình & bí mật với Secrets Manager](5.2.1-secrets-manager/)
2. [Khởi tạo Amazon RDS PostgreSQL (PostGIS)](5.2.2-rds-postgresql/)
3. [Lưu trữ media với Amazon S3 & presigned URL](5.2.3-s3-presigned/)
