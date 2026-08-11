---
title: "Worklog Tuần 3"
date: 2026-07-06
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Thiết kế cơ sở dữ liệu quan hệ và định nghĩa Prisma schema cho hệ thống quản lý cho thuê bất động sản (PostgreSQL + PostGIS).
* Phát triển module **tenant** và **manager**: xây dựng API quản lý hồ sơ người dùng theo từng vai trò.
* Phát triển module **application**: xây dựng API cho người thuê tạo đơn xin thuê và theo dõi trạng thái đơn.
* Phát triển module **lease**: xây dựng API cho quản lý xét duyệt đơn thuê, tự động tạo hợp đồng và lưu lịch sử thanh toán.
* Kiểm thử các API endpoint quản lý người dùng, đơn thuê, hợp đồng và hoàn thiện tài liệu Swagger.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Thiết kế ERD và định nghĩa schema Prisma: <br>&emsp; + Tạo schema cho các bảng: User, Tenant, Manager, Application, Lease, Payment <br>&emsp; + Thiết lập các mối quan hệ (1-1, 1-N, N-N) và enum trạng thái (ApplicationStatus, LeaseStatus) <br>&emsp; + Thực thi Prisma migration khởi tạo cấu trúc cơ sở dữ liệu trên PostgreSQL | 06/07/2026 | <https://www.prisma.io/docs/> |
| 3 | - Phát triển module **tenant** và module **manager**: <br>&emsp; + Xây dựng API lấy thông tin profile và cập nhật thông tin cá nhân cho tenant và manager <br>&emsp; + Xây dựng service đồng bộ dữ liệu người dùng từ Amazon Cognito vào PostgreSQL qua `cognitoId` <br>&emsp; + Validate dữ liệu đầu vào với class-validator DTO | 07/07/2026 | <https://docs.nestjs.com/techniques/validation> |
| 4 | - Phát triển module **application** (quản lý đơn thuê): <br>&emsp; + Xây dựng API cho tenant tạo đơn xin thuê (submit application) kèm thông tin cá nhân và thời gian thuê mong muốn <br>&emsp; + Xây dựng API cho tenant xem danh sách đơn thuê đã nộp và hủy đơn đang ở trạng thái PENDING <br>&emsp; + Xây dựng API cho manager xem danh sách đơn thuê cần xử lý theo từng bất động sản | 08/07/2026 | |
| 5 | - Phát triển module **lease** (quản lý hợp đồng & thanh toán): <br>&emsp; + Xây dựng API cho manager xét duyệt đơn thuê (APPROVED / DENIED) <br>&emsp; + Triển khai logic tự động khởi tạo hợp đồng (Lease) khi đơn thuê được duyệt trong một `prisma.$transaction` <br>&emsp; + Xây dựng API tra cứu danh sách hợp đồng và liên kết lịch sử thanh toán (Payment) theo từng hợp đồng | 09/07/2026 | <https://www.prisma.io/docs/concepts/components/prisma-client/transactions> |
| 6 | - Kiểm thử và viết tài liệu API: <br>&emsp; + Kiểm thử các luồng tạo đơn thuê, duyệt đơn, phát sinh hợp đồng bằng Postman <br>&emsp; + Kiểm tra các ràng buộc dữ liệu (ví dụ: không cho phép tạo hợp đồng trùng lặp) <br>&emsp; + Tích hợp Swagger (OpenAPI) mô tả chi tiết các endpoint của 4 module | 10/07/2026 | <https://docs.nestjs.com/openapi/introduction> |

---

### Kết quả đạt được tuần 3:

* Hoàn thành thiết kế **ERD** và **Prisma schema** đầy đủ cho các entity cốt lõi: User, Tenant, Manager, Application, Lease, Payment; chạy migration thành công trên cơ sở dữ liệu PostgreSQL.

* Phát triển xong **Tenant module** và **Manager module**: cung cấp đầy đủ API quản lý thông tin tài khoản, hồ sơ cá nhân và đồng bộ với Amazon Cognito.

* Phát triển xong **Application module**: cho phép tenant nộp đơn xin thuê, theo dõi trạng thái và cho phép manager xem danh sách đơn thuê của bất động sản.

* Phát triển xong **Lease module**: hỗ trợ manager xét duyệt đơn thuê và tự động sinh hợp đồng thuê (Lease) tương ứng trong một transaction an toàn dữ liệu.

* Kiểm thử toàn bộ API của 4 module với Postman và xuất tài liệu **Swagger API documentation** cho các endpoint đã xây dựng.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu sâu về thiết kế cơ sở dữ liệu quan hệ cho bài toán cho thuê nhà ở: quản lý vòng đời từ Application → Lease → Payment.
* Thành thạo cách định nghĩa quan hệ 1-N, 1-1 và enum trong Prisma schema, cũng như quản lý lịch sử migration bằng Prisma CLI.
* Nắm vững cách sử dụng `prisma.$transaction` để đảm bảo tính nguyên tố (atomicity) khi thực hiện nhiều thao tác CSDL liên quan (duyệt đơn + tạo hợp đồng).
* Học cách sử dụng DTO và class-validator để chặn dữ liệu không hợp lệ ngay tại tầng Controller.
