---
title: "Workshop"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

#### Tổng quan

Trong bài thực hành này, bạn sẽ xây dựng và triển khai **Hệ thống quản lý bất động sản cho thuê** (Real Estate Rental Management System) phát triển bằng **NestJS** và **Next.js** trên hạ tầng đám mây AWS theo mô hình production.

Bài lab hướng dẫn thiết lập mạng, tính toán, cơ sở dữ liệu, xác thực, lưu trữ, giám sát và tự động hóa CI/CD với các dịch vụ AWS tiêu chuẩn doanh nghiệp:

- **Amazon VPC, EC2, RDS** – Mạng nội bộ, máy chủ và cơ sở dữ liệu quan hệ có quản lý.
- **Application Load Balancer (ALB) & AWS WAF** – Định tuyến HTTPS và tường lửa ứng dụng web layer-7.
- **Amazon Cognito** – Quản lý định danh người dùng, xác thực và phân quyền theo vai trò (RBAC).
- **Amazon S3** – Lưu trữ hình ảnh bất động sản an toàn qua cơ chế presigned URL.
- **Amazon Location Service** – Chuyển đổi địa chỉ thành tọa độ và hiển thị bản đồ tương tác.
- **Amazon SES** – Gửi email thông báo tự động.
- **GitHub Actions & Amazon CloudWatch** – Tự động hóa CI/CD và giám sát hệ thống.

#### Nội dung bài lab

1. [Tổng quan kiến trúc & chuẩn bị môi trường](5.1-Overview/)
2. [Triển khai cơ sở dữ liệu & server backend](5.2-DB-Backend/)
3. [Định tuyến, bảo mật & HTTPS](5.3-Routing-Security/)
4. [Xác thực & phân quyền với Amazon Cognito](5.4-Cognito-Auth/)
5. [Quản lý lưu trữ & dịch vụ mở rộng](5.5-Storage-Services/)
6. [Tự động hóa CI/CD & giám sát](5.6-CICD-Monitoring/)
7. [Dọn dẹp tài nguyên](5.7-Cleanup/)
