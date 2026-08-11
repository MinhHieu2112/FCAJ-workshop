---
title: "Tổng quan kiến trúc & chuẩn bị môi trường"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc monorepo của hệ thống

**Hệ thống quản lý bất động sản cho thuê** được tổ chức dưới dạng monorepo (`pnpm workspaces`), bao gồm giao diện **Next.js**, dịch vụ backend **NestJS** và gói khai báo kiểu dữ liệu dùng chung (`@shared/types`).

![Sơ đồ kiến trúc hệ thống](/images/5-Workshop/5.1-Overview/AWS_architect.png)

#### Các luồng xử lý đám mây chính

1. **Xác thực và phân quyền người dùng**: Người dùng đăng ký/đăng nhập trực tiếp thông qua **Amazon Cognito User Pool**. Cognito phát hành JWT Access Token chứa thuộc tính vai trò tùy chỉnh (`custom:role`). NestJS backend xác thực Bearer Token thông qua `AuthGuard` và kiểm tra quyền (`TENANT` hoặc `MANAGER`) bằng `RolesGuard`.

2. **Quản lý media với S3 presigned URL**: Thay vì gửi ảnh lớn qua máy chủ backend, NestJS yêu cầu **Amazon S3** tạo **Presigned PUT URL** có thời hạn ngắn. Frontend Next.js tải file ảnh trực tiếp lên S3, giúp tiết kiệm băng thông và giảm tải cho backend.

3. **Chuyển đổi địa chỉ & bản đồ tương tác**: Khi chủ nhà tạo bất động sản mới, chuỗi địa chỉ được gửi đến **Amazon Location Service** để chuyển thành tọa độ `(Vĩ độ, Kinh độ)`. Tọa độ được lưu trong cơ sở dữ liệu **Amazon RDS (PostgreSQL + PostGIS)** phục vụ tìm kiếm bất động sản theo bán kính.

#### Các bước thực hiện

1. [Thiết lập VPC, public/private subnet & security groups](5.1.1-vpc-subnet/)
2. [Phân quyền IAM role cho EC2 & services](5.1.2-iam-roles/)
