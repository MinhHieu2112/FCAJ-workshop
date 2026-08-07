---
title: "Tổng quan kiến trúc & dịch vụ AWS"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc monorepo của hệ thống

**Hệ thống quản lý bất động sản cho thuê** được tổ chức dưới dạng monorepo (`pnpm workspaces`), bao gồm giao diện **Next.js**, dịch vụ backend **NestJS** và gói khai báo kiểu dữ liệu dùng chung (`@shared/types`).

![System Architecture Diagram](/images/5-Workshop/5.1-Overview/architecture-diagram.png)
> 💡 **Lưu ý tác giả:** *[Bổ sung hình ảnh sơ đồ kiến trúc hệ thống kết nối giữa Next.js, NestJS và các dịch vụ AWS]*

#### Sơ đồ phối hợp các dịch vụ đám mây AWS

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                         │
│              Next.js App (App Router, SSR/CSR)              │
│        React Components · Redux Toolkit / RTK Query         │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS REST API + WebSocket
┌────────────────────────▼────────────────────────────────────┐
│                       Backend Layer                         │
│              NestJS (TypeScript · Modular DI)               │
│   Property · Application · Lease · Tenant · Manager ·       │
│   Message (Chat Real-time) · Notification · Location        │
│         AuthGuard · RolesGuard · Class-Validator            │
└────┬──────────────┬──────────────┬──────────────┬───────────┘
     │              │              │              │
┌────▼────┐   ┌─────▼─────┐  ┌─────▼─────┐  ┌─────▼───────────┐
│ AWS RDS │   │ Amazon S3 │  │ Amazon SES│  │ Amazon Location │
│PostgreSQL│  │ (property │  │ (email    │  │ Service         │
│+ PostGIS│   │ images)   │  │ notify)   │  │ (Geocode & Map) │
└─────────┘   └───────────┘  └───────────┘  └─────────────────┘
                     Amazon Cognito (User Pool & App Client)
                     — Client authentication & JWT token issuance —
```

#### Các luồng xử lý đám mây chính trong workshop

1. **Xác thực và phân quyền người dùng**:
   - Người dùng đăng ký/đăng nhập trực tiếp thông qua **Amazon Cognito User Pool**.
   - Cognito phát hành JWT Access Token chứa thuộc tính vai trò tùy chỉnh (`custom:role`).
   - NestJS backend xác thực Bearer Token thông qua `AuthGuard` và kiểm tra quyền (`TENANT` hoặc `MANAGER`) bằng `RolesGuard`.

2. **Quản lý media với S3 Presigned URL**:
   - Thay vì gửi ảnh lớn qua máy chủ backend, NestJS yêu cầu **Amazon S3** tạo **Presigned PUT URL** có thời hạn ngắn (ví dụ: 15 phút).
   - Frontend Next.js tải file ảnh trực tiếp lên S3, giúp tiết kiệm băng thông và giảm tải cho backend.

3. **Chuyển đổi địa chỉ & bản đồ tương tác**:
   - Khi chủ nhà tạo bất động sản mới, chuỗi địa chỉ được gửi đến **Amazon Location Service Place Index** để chuyển thành tọa độ `(Vĩ độ, Kinh độ)`.
   - Tọa độ được lưu trong cơ sở dữ liệu **Amazon RDS (PostgreSQL + PostGIS)** phục vụ tìm kiếm bất động sản theo bán kính.
