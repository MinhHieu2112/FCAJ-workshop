---
title: "Quản lý hình ảnh với Amazon S3"
date: 2026-08-06
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Kiến trúc upload bằng Amazon S3 Presigned URL

Tin đăng bất động sản yêu cầu tải lên nhiều hình ảnh chất lượng cao. Việc gửi file ảnh trực tiếp qua máy chủ NestJS gây lãng phí CPU, RAM và dung lượng đường truyền backend.

Hệ thống giải quyết bài toán này bằng giải pháp **Amazon S3 Presigned URL**:

```
┌──────────────┐         1. Request Presigned URL (POST /properties/upload-url)       ┌──────────────┐
│              ├──────────────────────────────────────────────────────────────────────►│              │
│   Next.js    │         2. Returns short-lived Presigned PUT URL                     │   NestJS     │
│   Client     │◄──────────────────────────────────────────────────────────────────────┤   Backend    │
│              │                                                                      └──────────────┘
│              │         3. Direct Binary PUT Upload (image/jpeg)
│              ├──────────────────────────────────────────────────────────────────────┐
│              │                                                                      ▼
│              │         4. Returns 200 OK                                    ┌──────────────┐
│              │◄─────────────────────────────────────────────────────────────┤  Amazon S3   │
│              │                                                              │  Bucket      │
└──────────────┘                                                              └──────────────┘
```

#### Ưu điểm của Presigned URL
+ **Tối ưu hiệu năng backend**: Luồng tải ảnh trực tiếp giữa Trình duyệt và S3, không qua trung gian server backend.
+ **Bảo mật cao**: URL tải lên chỉ có hiệu lực ngắn hạn (ví dụ: 15 phút).
+ **Hiển thị công khai**: Hình ảnh bất động sản có thể truy cập đọc công khai (Public Read) qua URL tiêu chuẩn của S3.

#### Các bước thực hiện

1. [Khởi tạo Amazon S3 Bucket & cấu hình CORS](5.4.1-s3-bucket-setup/)
2. [Tích hợp Presigned Upload URL trong NestJS](5.4.2-presigned-urls/)
