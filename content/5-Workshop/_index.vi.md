---
title: "Workshop"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng Hệ thống quản lý bất động sản cho thuê trên AWS

#### Tổng quan

Trong bài thực hành (workshop) này, bạn sẽ học cách tích hợp và cấu hình các dịch vụ điện toán đám mây AWS cốt lõi cho **Hệ thống quản lý bất động sản cho thuê** (Real Estate Rental Management System) được phát triển bằng **NestJS** và **Next.js**.

Bài lab hướng dẫn từng bước tích hợp các dịch vụ AWS tiêu chuẩn doanh nghiệp vào ứng dụng thực tế:
+ **Amazon Cognito** - Quản lý định danh người dùng, xác thực đăng nhập, phân quyền theo vai trò (RBAC - Tenant/Manager) và xử lý JWT Token.
+ **Amazon S3** - Lưu trữ hình ảnh bất động sản an toàn với cơ chế tải ảnh trực tiếp thông qua Presigned URL.
+ **Amazon Location Service** - Chuyển đổi địa chỉ thành tọa độ (Geocoding) và hiển thị vị trí bất động sản trên bản đồ tương tác.
+ **Amazon RDS (PostgreSQL + PostGIS)** - Cơ sở dữ liệu đám mây quản lý quy trình nộp đơn thuê, hợp đồng và truy vấn không gian.

{{% notice tip %}}
Workshop được thiết kế bám sát kiến trúc thực tế của dự án, sử dụng AWS SDK v3 trên nền ngôn ngữ TypeScript và kiến trúc mô-đun của NestJS.
{{% /notice %}}

#### Nội dung bài lab

1. [Tổng quan kiến trúc & dịch vụ AWS](5.1-Overview/)
2. [Chuẩn bị môi trường & quyền IAM](5.2-Prerequisites/)
3. [Xác thực & phân quyền với Amazon Cognito](5.3-Cognito-Auth/)
4. [Quản lý hình ảnh với Amazon S3](5.4-S3-Storage/)
5. [Định vị & bản đồ với Amazon Location Service](5.5-Location-Service/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)
