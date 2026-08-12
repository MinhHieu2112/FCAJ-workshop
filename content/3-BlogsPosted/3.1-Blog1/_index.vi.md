---
title: "Blog 1"
date: 2026-08-03
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# HỆ THỐNG XÁC THỰC VỚI AWS COGNITO

Trong quá trình phát triển hệ thống quản lý cho thuê bất động sản, em lựa chọn **Amazon Cognito** để quản lý xác thực người dùng thay vì tự xây dựng cơ chế đăng nhập bằng JWT. Quá trình tích hợp không chỉ dừng lại ở việc đăng nhập thành công mà còn phải giải quyết bài toán phân quyền giữa **Manager** và **Tenant** trong toàn bộ hệ thống.

Một trong những khó khăn gặp phải là xác định loại token phù hợp để gửi đến Backend. Ban đầu, hệ thống chỉ sử dụng **Access Token** để xác thực, tuy nhiên token này không chứa thuộc tính `custom:role` được cấu hình trong Cognito. Điều này khiến `RolesGuard` không thể xác định vai trò của người dùng và nhiều yêu cầu bị từ chối với mã lỗi **401 Unauthorized** hoặc **403 Forbidden**.

Sau quá trình tìm hiểu, thử nghiệm và tối ưu, cơ chế xác thực và phân quyền của hệ thống được hoàn thiện theo các nội dung sau:

- Sử dụng **Access Token** làm Bearer Token để xác thực (Authentication) mọi yêu cầu gửi từ Frontend đến Backend thông qua `JwtAuthGuard`.
- Trích xuất thông tin vai trò người dùng (`custom:role`) từ **ID Token** ở phía Frontend và truyền đến Backend thông qua `X-User-Role` hoặc Token Payload để phục vụ quá trình phân quyền (Authorization).
- Xây dựng `JwtAuthGuard` với cơ chế kiểm tra nhiều lớp: ưu tiên lấy vai trò từ Token Payload, sau đó truy vấn cơ sở dữ liệu bằng Prisma và cuối cùng sử dụng giá trị từ Header `X-User-Role` khi cần thiết.
- Xây dựng `RolesGuard` để kiểm tra quyền truy cập của người dùng theo từng vai trò (**Manager** hoặc **Tenant**) trước khi cho phép truy cập các API được bảo vệ.
- Cấu hình CORS trên NestJS để cho phép Header `X-User-Role`, đảm bảo các yêu cầu từ Frontend được xử lý đúng và tránh lỗi Preflight (`OPTIONS`).
- Sử dụng `fetchAuthSession()` của Amplify v6 để tự động quản lý phiên đăng nhập và làm mới Access Token khi hết hạn, giúp người dùng không cần đăng nhập lại trong thời gian Refresh Token còn hiệu lực.

## Hình ảnh minh họa

![Overview](/images/3-BlogsPosted/Cognito_Auth_Architecture.png)

## Tham khảo

- [AWS Cognito Documentation](https://docs.aws.amazon.com/cognito/)
- [AWS Amplify Documentation](https://docs.aws.amazon.com/amplify/)
- [NestJS Authentication](https://docs.nestjs.com/security/authentication)
- [NestJS Authorization](https://docs.nestjs.com/security/authorization)
