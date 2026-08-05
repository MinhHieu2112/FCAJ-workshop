---
title: "Worklog Tuần 4"
date: 2026-07-13
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Thiết kế cơ sở dữ liệu và định nghĩa schema Prisma cho hệ thống.
* Xây dựng chức năng xác thực người dùng (Authentication & Authorization).
* Tích hợp cơ sở dữ liệu và thiết lập lưu trữ file với AWS S3.
* Tích hợp các dịch vụ AWS: S3, SES, Cognito vào backend.
* Kiểm thử và chỉnh sửa các chức năng đã xây dựng.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Thiết kế cơ sở dữ liệu và API: <br>&emsp; + Vẽ ERD (Entity Relationship Diagram) cho toàn bộ hệ thống <br>&emsp; + Định nghĩa các entities: User, Property, Booking, Contract, Payment, Notification <br>&emsp; + Viết Prisma schema: model, relation, enum <br>&emsp; + Thiết kế API endpoints chi tiết (method, path, request/response body) <br>&emsp; + Tạo migration và seed data | 13/07/2026 | <https://www.prisma.io/docs/> |
| 3 | - Xây dựng chức năng xác thực người dùng: <br>&emsp; + Implement JWT-based authentication (Access Token + Refresh Token) <br>&emsp; + Xây dựng Auth module: register, login, logout, refresh token <br>&emsp; + Implement bcrypt để hash password <br>&emsp; + Viết AuthGuard và RolesGuard để phân quyền <br>&emsp; + Implement Google OAuth2 login (Passport.js) | 14/07/2026 | <https://docs.nestjs.com/security/authentication> |
| 4 | - Tích hợp cơ sở dữ liệu và lưu trữ: <br>&emsp; + Kết nối Prisma với PostgreSQL (local + AWS RDS) <br>&emsp; + Viết repository pattern cho từng module <br>&emsp; + Tích hợp AWS S3: upload ảnh bất động sản, generate presigned URL <br>&emsp; + Cấu hình Multer để xử lý file upload từ client | 15/07/2026 | <https://docs.aws.amazon.com/s3/> |
| 5 | - Tích hợp các dịch vụ AWS vào hệ thống: <br>&emsp; + **AWS SES**: gửi email xác thực tài khoản, thông báo đặt lịch <br>&emsp; + **AWS Cognito**: tích hợp User Pool, cấu hình App Client <br>&emsp; + **AWS S3**: hoàn thiện logic upload/delete ảnh, quản lý folder theo property ID <br>&emsp; + Viết unit test cho Auth module | 16/07/2026 | <https://docs.aws.amazon.com/ses/> |
| 6 | - Kiểm thử và chỉnh sửa chức năng: <br>&emsp; + Test các API endpoint với Postman/Thunder Client <br>&emsp; + Fix bug liên quan đến token refresh và role guard <br>&emsp; + Kiểm tra S3 upload với file lớn (multipart upload) <br>&emsp; + Review code và refactor theo best practices của NestJS | 17/07/2026 | |

---

### Kết quả đạt được tuần 4:

* Hoàn thành **ERD** và **Prisma schema** đầy đủ cho 6 entity chính: User, Property, Booking, Contract, Payment, Notification; chạy migration thành công trên PostgreSQL.

* Xây dựng hệ thống **Authentication** hoàn chỉnh:
  * JWT với Access Token (15 phút) và Refresh Token (7 ngày)
  * Bcrypt password hashing
  * AuthGuard và RolesGuard hoạt động đúng cho cả 3 roles: TENANT, LANDLORD, ADMIN
  * Google OAuth2 login tích hợp thành công

* Tích hợp thành công **AWS S3**: upload và xóa ảnh bất động sản, generate presigned URL để client truy cập ảnh an toàn.

* Tích hợp **AWS SES**: gửi email xác thực tài khoản và email thông báo đặt lịch xem nhà.

* Tích hợp **AWS Cognito**: cấu hình User Pool và App Client, đồng bộ với Auth module của NestJS.

* Kiểm thử toàn bộ API Authentication với Postman, xác nhận flow đăng ký → xác thực email → đăng nhập hoạt động đúng.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu sâu về cơ chế JWT: Access Token ngắn hạn (stateless) kết hợp Refresh Token dài hạn giúp cân bằng giữa security và UX.
* Nắm được cách thiết kế Prisma schema với quan hệ 1-N và N-N, sử dụng enum để quản lý trạng thái (BookingStatus, PropertyStatus).
* Học cách tích hợp AWS SDK v3 vào NestJS: cấu hình credential, region, và sử dụng các command (PutObjectCommand, GetObjectCommand).
* Hiểu được sự khác biệt giữa public S3 URL và presigned URL — presigned URL an toàn hơn và có thời hạn sử dụng.
* Kinh nghiệm debug: lỗi CORS khi tích hợp Cognito cần cấu hình đúng App Client callback URL.
