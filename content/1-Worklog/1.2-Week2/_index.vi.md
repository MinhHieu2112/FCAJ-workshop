---
title: "Nhật ký tuần 2"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Phân tích chi tiết yêu cầu nghiệp vụ của hệ thống quản lý cho thuê bất động sản và các nhóm người dùng (tenant, manager).
* Thiết kế kiến trúc tổng thể hệ thống và tái cấu trúc dự án thành mô hình monorepo (pnpm workspaces).
* Tích hợp dịch vụ Amazon Cognito để quản lý người dùng và phát hành JWT authentication.
* Xây dựng cơ chế xác thực và phân quyền ở backend NestJS bằng `AuthGuard` và `RolesGuard`.
* Kiểm thử luồng đăng ký, đăng nhập và phân quyền người dùng trước khi phát triển các module nghiệp vụ.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Phân tích yêu cầu hệ thống quản lý cho thuê bất động sản: <br>&emsp; + Xác định các nhóm người dùng: người thuê (tenant), chủ nhà/quản lý (manager) <br>&emsp; + Phân tích luồng nghiệp vụ cốt lõi: đăng tin, nộp đơn thuê, xét duyệt, hợp đồng <br> - Tái cấu trúc (refactor) dự án thành mô hình monorepo sử dụng pnpm workspaces (`apps/server`, `apps/client`, `packages/types`) | 29/06/2026 | <https://cloudjourney.awsstudygroup.com/1-explore/> |
| 3 | - Thiết kế kiến trúc tổng thể hệ thống (high-level architecture): <br>&emsp; + Backend: NestJS (TypeScript, modular architecture, DI) <br>&emsp; + Frontend: Next.js (App Router, Redux Toolkit / RTK Query) <br>&emsp; + Database: PostgreSQL với Prisma ORM <br>&emsp; + Xác định sơ đồ tích hợp các dịch vụ AWS (Cognito, S3, RDS, Location Service) | 30/06/2026 | <https://docs.nestjs.com/> |
| 4 | - Tích hợp Amazon Cognito và JWT Authentication: <br>&emsp; + Tạo và cấu hình Amazon Cognito User Pool & App Client <br>&emsp; + Thiết lập luồng xác thực đăng ký, xác nhận email và đăng nhập qua Cognito <br>&emsp; + Đồng bộ thông tin người dùng từ Cognito với backend qua `cognitoId` | 01/07/2026 | <https://docs.aws.amazon.com/cognito/> |
| 5 | - Xây dựng cơ chế xác thực và phân quyền ở backend NestJS: <br>&emsp; + Xây dựng `AuthGuard` xác thực chữ ký và thời hạn của JWT token <br>&emsp; + Xây dựng `RolesGuard` kiểm tra quyền truy cập theo vai trò (TENANT, MANAGER) <br>&emsp; + Áp dụng decorator phân quyền cho các endpoint thử nghiệm | 02/07/2026 | <https://docs.nestjs.com/guards> |
| 6 | - Kiểm thử và tổng hợp tiến độ tuần 2: <br>&emsp; + Kiểm thử các API xác thực và phân quyền với Postman (đăng ký, đăng nhập, lấy token) <br>&emsp; + Kiểm tra việc ngăn chặn các request không hợp lệ hoặc sai vai trò <br>&emsp; + Báo cáo tiến độ và trao đổi định hướng tuần 3 với mentor | 03/07/2026 | |

---

### Kết quả đạt được tuần 2:

* Hoàn thành phân tích yêu cầu hệ thống và xác định rõ 2 vai trò người dùng chính: **tenant** (người thuê) và **manager** (chủ nhà/quản lý).

* Tái cấu trúc thành công dự án sang mô hình **monorepo** với pnpm workspaces (`apps/server`, `apps/client`, `packages/types`), giúp chia sẻ DTO và interface dễ dàng giữa frontend và backend.

* Hoàn thành bản thiết kế kiến trúc tổng thể của ứng dụng kết hợp giữa NestJS, Next.js, PostgreSQL và các dịch vụ AWS.

* Tích hợp thành công **Amazon Cognito**: khởi tạo User Pool, App Client, thực hiện luồng xác thực và phát hành JWT token.

* Xây dựng và áp dụng thành công **`AuthGuard`** và **`RolesGuard`** tại backend NestJS, bảo vệ các endpoint và phân quyền chính xác theo vai trò (TENANT, MANAGER).

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu cách tổ chức dự án dạng monorepo với pnpm workspaces, giúp quản lý nguồn mã nguồn tập trung và nhất quán type giữa client và server.
* Nắm vững cơ chế ủy quyền xác thực cho Amazon Cognito — giúp giảm thiểu rủi ro tự lưu trữ và quản lý mật khẩu người dùng tại cơ sở dữ liệu nội bộ.
* Nắm chắc phương pháp xây dựng Guard trong NestJS (ExecutionContext, Reflector) để triển khai Authentication & Role-based Access Control (RBAC).
* Bước đầu áp dụng tư duy thiết kế hệ thống theo hướng chia nhỏ module (modular design) trước khi triển khai các tính năng chi tiết.
