---
title: "Worklog Tuần 3"
date: 2026-07-06
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Tìm hiểu nghiệp vụ hệ thống bất động sản — xác định các actors, use cases và luồng nghiệp vụ chính.
* Phân tích yêu cầu và xây dựng danh sách tính năng (feature list) cho hệ thống.
* Thiết kế kiến trúc tổng thể của hệ thống (high-level architecture).
* Lựa chọn công nghệ phù hợp cho Backend, Frontend và Database.
* Xác định các dịch vụ AWS sẽ tích hợp vào hệ thống.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Nghiên cứu nghiệp vụ hệ thống bất động sản: <br>&emsp; + Xác định các actors: Tenant (người thuê), Landlord (chủ nhà), Admin <br>&emsp; + Phân tích các luồng nghiệp vụ chính: đăng tin, tìm kiếm, đặt lịch xem nhà, ký hợp đồng, thanh toán <br>&emsp; + Tìm hiểu các hệ thống bất động sản thực tế (Mogi, Batdongsan.com.vn) để lấy tham khảo | 06/07/2026 | Tham khảo thực tế |
| 3 | - Phân tích yêu cầu hệ thống: <br>&emsp; + Viết Use Case Diagram cho từng actor <br>&emsp; + Liệt kê functional requirements và non-functional requirements <br>&emsp; + Xác định các ràng buộc nghiệp vụ (business rules) <br> - Thảo luận với mentor về phạm vi hệ thống | 07/07/2026 | Tài liệu phân tích nghiệp vụ |
| 4 | - Thiết kế kiến trúc tổng thể hệ thống: <br>&emsp; + Vẽ high-level architecture diagram <br>&emsp; + Xác định các module chính: Auth, Property, Booking, Payment, Notification <br>&emsp; + Xác định giao tiếp giữa các module (REST API, message queue) <br>&emsp; + Thiết kế sơ bộ API endpoints (RESTful conventions) | 08/07/2026 | |
| 5 | - Lựa chọn công nghệ: <br>&emsp; + **Backend**: NestJS (TypeScript) — modular architecture, dependency injection <br>&emsp; + **Frontend**: Next.js (React) — SSR/SSG, routing, TailwindCSS <br>&emsp; + **Database**: PostgreSQL (relational) + Prisma ORM <br>&emsp; + Lý do lựa chọn từng công nghệ (trade-off analysis) | 09/07/2026 | |
| 6 | - Xác định các dịch vụ AWS tích hợp vào hệ thống: <br>&emsp; + **S3**: lưu trữ ảnh bất động sản <br>&emsp; + **SES**: gửi email thông báo, xác thực <br>&emsp; + **Cognito**: quản lý xác thực người dùng <br>&emsp; + **RDS**: cơ sở dữ liệu production <br>&emsp; + **EC2 / ECS**: deploy backend <br> - Tổng hợp tài liệu thiết kế, cập nhật vào repository | 10/07/2026 | |

---

### Kết quả đạt được tuần 3:

* Hiểu rõ nghiệp vụ hệ thống bất động sản cho thuê với 3 actor chính: **Tenant**, **Landlord** và **Admin**.

* Hoàn thành bản phân tích yêu cầu bao gồm:
  * Use Case Diagram cho từng actor
  * Danh sách Functional Requirements (đăng tin, tìm kiếm, đặt lịch, thanh toán, thông báo)
  * Non-functional Requirements (performance, security, scalability)

* Hoàn thành bản thiết kế kiến trúc tổng thể với các module: **Auth**, **Property**, **Booking**, **Payment**, **Notification**, **Admin**.

* Quyết định tech stack:
  * Backend: **NestJS** (TypeScript, modular, DI)
  * Frontend: **Next.js** (App Router, SSR)
  * ORM: **Prisma** với **PostgreSQL**
  * Styling: **TailwindCSS**

* Lập danh sách đầy đủ các dịch vụ AWS sẽ tích hợp: S3, SES, Cognito, RDS, EC2/ECS.

---

### Kiến thức / Kinh nghiệm học được:

* Học được phương pháp phân tích nghiệp vụ hệ thống thực tế: bắt đầu từ actor → use case → business rule → functional requirement.
* Hiểu được tầm quan trọng của việc thiết kế kiến trúc trước khi code — giúp tránh các thay đổi lớn sau này.
* Nắm được trade-off khi chọn công nghệ: NestJS cho Backend phù hợp với kiến trúc module hóa; Prisma giúp type-safe database access.
* Học cách kết hợp dịch vụ AWS vào thiết kế hệ thống ngay từ giai đoạn planning thay vì tích hợp sau.
