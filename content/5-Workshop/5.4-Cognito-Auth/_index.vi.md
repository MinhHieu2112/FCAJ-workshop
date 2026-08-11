---
title: "Xác thực & phân quyền với Amazon Cognito"
date: 2026-08-06
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Tổng quan luồng xác thực & phân quyền

Trong mô-đun này, bạn sẽ thiết lập **Amazon Cognito User Pool** để quản lý đăng ký, đăng nhập và phát hành chuỗi JSON Web Token (JWT).

Hệ thống phân quyền hai vai trò người dùng chính:
- **`TENANT` (Người thuê)**: Tìm kiếm bất động sản, gửi đơn đăng ký thuê, trò chuyện trực tiếp với chủ nhà.
- **`MANAGER` (Chủ nhà / Quản lý)**: Tạo và chỉnh sửa bất động sản, tải lên hình ảnh, duyệt đơn đăng ký thuê, tạo hợp đồng.

```
┌──────────────┐          1. Authenticate (User/Pass)         ┌──────────────────────┐
│              ├─────────────────────────────────────────────►│                      │
│   Next.js    │          2. Returns Access Token (JWT)       │ Amazon Cognito       │
│   Client     │◄─────────────────────────────────────────────┤ User Pool            │
│              │                                              └──────────────────────┘
└──────┬───────┘
       │ 3. Send REST request with Bearer JWT
       ▼
┌────────────────────────────────────────────────────────────────────────────────────┐
│ NestJS backend server                                                              │
│  ┌───────────────────────┐   4. Verify Token    ┌───────────────────────────────┐  │
│  │ AuthGuard             ├─────────────────────►│ Cognito JWKS / PublicKey      │  │
│  └───────────┬───────────┘                      └───────────────────────────────┘  │
│              │ 5. Extract payload (`custom:role`)                                  │
│              ▼                                                                     │
│  ┌───────────────────────┐   6. Validate Role   ┌───────────────────────────────┐  │
│  │ RolesGuard            ├─────────────────────►│ Controller Method (@Roles)    │  │
│  └───────────────────────┘                      └───────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────────────────┘
```

#### Các bước thực hiện

1. [Khởi tạo Amazon Cognito User Pool](5.4.1-cognito-setup/)
2. [Tích hợp NestJS AuthGuard & RolesGuard](5.4.2-nestjs-guards/)
