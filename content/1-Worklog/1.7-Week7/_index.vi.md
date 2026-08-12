---
title: "Nhật ký tuần 7"
date: 2026-08-03
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

* Tối ưu hệ thống: phát hiện và xử lý dứt điểm vấn đề **N+1 Query** trong truy vấn dữ liệu.
* Phân tích và xử lý bài toán **Race Condition** khi duyệt đơn thuê và tạo hợp đồng bằng **Transaction** và **Pessimistic Locking**.
* Tăng cường bảo mật toàn hệ thống: rà soát phân quyền, triển khai Refresh Token Rotation, Rate Limiting và HTTP security headers.
* Hoàn thiện tích hợp **Amazon Location Service** phục vụ geocoding địa chỉ và bản đồ tương tác.
* Tối ưu hóa truy vấn dữ liệu không gian PostGIS kết hợp với Amazon Location Service.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Tối ưu truy vấn CSDL và giải quyết **N+1 Query**: <br>&emsp; + Phân tích Prisma query logs, đo đạc số lượng query phát sinh ở endpoint danh sách bất động sản và đơn thuê <br>&emsp; + Thay thế lazy loading bằng Prisma `include` / `select` eager loading <br>&emsp; + Tạo database index trên các cột thường xuyên query (`location`, `price`, `status`, `createdAt`) | 03/08/2026 | <https://www.prisma.io/docs/guides/performance-and-optimization> |
| 3 | - Xử lý bài toán **Race Condition**: <br>&emsp; + Phân tích kịch bản xung đột: nhiều thao tác duyệt đơn thuê hoặc tạo hợp đồng xảy ra đồng thời cho cùng một khoảng thời gian <br>&emsp; + Áp dụng `prisma.$transaction` và khóa dữ liệu Pessimistic Locking (`SELECT FOR UPDATE`) <br>&emsp; + Kiểm thử tạo nhiều request đồng thời (concurrent requests) để đảm bảo dữ liệu luôn nhất quán | 04/08/2026 | <https://www.postgresql.org/docs/current/explicit-locking.html> |
| 4 | - Tăng cường bảo mật toàn bộ hệ thống: <br>&emsp; + Kiểm tra bảo vệ 100% các endpoint mutative bằng `AuthGuard` và `RolesGuard` <br>&emsp; + Triển khai cơ chế **Refresh Token Rotation**: vô hiệu hóa token cũ ngay khi vừa làm mới token <br>&emsp; + Tích hợp Helmet.js cho HTTP security headers và `@nestjs/throttler` cho Rate Limiting chống brute-force <br>&emsp; + Validate và sanitize kỹ lưỡng mọi user input bằng DTO class-validator | 05/08/2026 | <https://owasp.org/www-project-top-ten/> |
| 5 | - Hoàn thiện tích hợp **Amazon Location Service**: <br>&emsp; + Khởi tạo Place Index và Map resources trên AWS Console <br>&emsp; + Xây dựng `LocationService` ở backend NestJS gọi API geocode địa chỉ thành tọa độ (latitude, longitude) <br>&emsp; + Tích hợp MapLibre GL JS / Amazon Location Service SDK hiển thị bản đồ tìm kiếm tương tác trên giao diện Next.js | 06/08/2026 | <https://docs.aws.amazon.com/location/> |
| 6 | - Tối ưu truy vấn dữ liệu không gian PostGIS và tổng hợp: <br>&emsp; + Tối ưu hóa truy vấn tìm kiếm bất động sản theo bán kính khoảng cách (`ST_DWithin` / `ST_Distance`) kết hợp tọa độ từ Amazon Location Service <br>&emsp; + Đo đạc lại thời gian phản hồi API sau tối ưu (response time giảm rõ rệt) <br>&emsp; + Đánh giá tổng thể độ an toàn và hiệu năng của hệ thống với mentor | 07/08/2026 | |

---

### Kết quả đạt được tuần 7:

* Khắc phục triệt để vấn đề **N+1 Query**: giảm số lượng query truy vấn CSDL từ hàng chục câu query xuống chỉ còn 1 query JOIN duy nhất bằng Prisma `include` hợp lý.

* Xử lý thành công **Race Condition**: áp dụng `prisma.$transaction` và `SELECT FOR UPDATE` ngăn chặn hoàn toàn việc khởi tạo hợp đồng trùng lặp khi có thao tác đồng thời.

* Nâng cao toàn diện **bảo mật hệ thống**: triển khai Refresh Token Rotation, Rate Limiting, Helmet.js HTTP headers và bảo vệ 100% API endpoints bằng `AuthGuard` và `RolesGuard`.

* Tích hợp thành công **Amazon Location Service**: hỗ trợ tự động chuyển đổi địa chỉ thành tọa độ địa lý (geocoding) và hiển thị bản đồ tìm kiếm bất động sản trực quan trên giao diện.

* Tối ưu hóa câu truy vấn spatial query PostGIS giúp tốc độ lọc bất động sản theo vị trí đạt thời gian phản hồi nhanh dưới 50ms.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu sâu bản chất N+1 Query trong ORM và phương pháp eager loading để tối ưu hóa thời gian phản hồi của ứng dụng.
* Nắm vững kỹ thuật xử lý concurrency (xung đột dữ liệu đồng thời) trong PostgreSQL bằng `prisma.$transaction` và Pessimistic Locking (`SELECT FOR UPDATE`).
* Học cách áp dụng các nguyên tắc OWASP Top 10: Refresh Token Rotation chống cướp phiên đăng nhập, Rate Limiting chống DDoS/brute-force.
* Nắm vững cách tích hợp Amazon Location Service vào backend và frontend để giải quyết bài toán định vị địa lý và hiển thị bản đồ chuyên nghiệp.
