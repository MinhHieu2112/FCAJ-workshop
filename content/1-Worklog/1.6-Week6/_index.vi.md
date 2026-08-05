---
title: "Worklog Tuần 6"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

* Tối ưu truy vấn cơ sở dữ liệu — xác định và giải quyết vấn đề **N+1 Query**.
* Áp dụng kỹ thuật **eager loading** và **batching** để tối ưu hiệu năng.
* Kiểm thử hiệu năng hệ thống sau khi tối ưu (so sánh trước/sau).
* Tổng hợp kết quả tối ưu và lập tài liệu kỹ thuật.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Tối ưu truy vấn cơ sở dữ liệu: <br>&emsp; + Phân tích các query hiện tại bằng Prisma logging và query count <br>&emsp; + Xác định các điểm có vấn đề N+1 Query trong hệ thống: Property listing với images, Booking với user và property info <br>&emsp; + Đo benchmark số lượng query trước khi tối ưu <br>&emsp; + Thiết lập database query logging để theo dõi | 27/07/2026 | <https://www.prisma.io/docs/concepts/components/prisma-client/logging> |
| 3 | - Tìm hiểu và xử lý vấn đề N+1 Query: <br>&emsp; + Phân tích nguyên nhân: ORM lazy loading gây ra N+1 khi lặp qua danh sách và truy cập relation <br>&emsp; + Nghiên cứu giải pháp: eager loading (Prisma `include`), batching (DataLoader pattern) <br>&emsp; + So sánh `include` vs `select` trong Prisma để chỉ lấy field cần thiết <br>&emsp; + Áp dụng `include` cho các API endpoint có N+1 | 28/07/2026 | <https://www.prisma.io/docs/guides/performance-and-optimization/query-optimization-performance> |
| 4 | - Áp dụng eager loading, batching và tối ưu truy vấn: <br>&emsp; + Refactor Property listing: `include` images, landlord info trong một query duy nhất <br>&emsp; + Refactor Booking listing: `include` property và tenant info <br>&emsp; + Áp dụng **database indexing**: tạo index trên các column thường xuyên filter/search (location, price, status) <br>&emsp; + Implement **cursor-based pagination** để tránh OFFSET scan toàn bảng | 29/07/2026 | |
| 5 | - Kiểm thử hiệu năng sau tối ưu: <br>&emsp; + Đo lại số lượng query sau khi refactor (so sánh N+1 → 1) <br>&emsp; + Dùng `EXPLAIN ANALYZE` trong PostgreSQL để kiểm tra query plan <br>&emsp; + Load test bằng Artillery hoặc `k6` với 100 concurrent users <br>&emsp; + Ghi lại kết quả: response time, query count, throughput | 30/07/2026 | |
| 6 | - Tổng hợp kết quả tối ưu: <br>&emsp; + Viết báo cáo kỹ thuật về N+1 Query: nguyên nhân, giải pháp, kết quả đo lường <br>&emsp; + Cập nhật code với các best practices tối ưu query <br>&emsp; + Review lại toàn bộ code base để tìm các điểm tối ưu còn sót lại | 31/07/2026 | |

---

### Kết quả đạt được tuần 6:

* Phát hiện và xác định **5 điểm N+1 Query** trong hệ thống: Property listing (ảnh + landlord), Booking listing (property + user), Notification listing (booking details).

* Giải quyết toàn bộ N+1 bằng cách áp dụng **Prisma `include`** — giảm từ N+1 queries xuống còn 1 query với JOIN:
  * Property listing: từ ~50 queries → 1 query (với 20 records)
  * Booking listing: từ ~30 queries → 1 query (với 10 records)

* Áp dụng **database indexing** trên các column: `location`, `price`, `status`, `createdAt` — giảm thời gian query filter từ ~200ms xuống ~15ms.

* Implement **cursor-based pagination** thay thế offset, hiệu quả hơn với bảng lớn (không cần full table scan).

* Kết quả load test (100 concurrent users):
  * Trước tối ưu: P95 response time ~800ms
  * Sau tối ưu: P95 response time ~120ms (**giảm 85%**)

* Hoàn thành báo cáo kỹ thuật về N+1 Query với số liệu đo lường cụ thể.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu sâu về vấn đề N+1 Query — một trong những bottleneck phổ biến nhất trong các ứng dụng sử dụng ORM. ORM lazy loading tiện lợi nhưng nguy hiểm về hiệu năng khi làm việc với danh sách lớn.
* Học được cách dùng `EXPLAIN ANALYZE` trong PostgreSQL để đọc query execution plan, xác định sequential scan vs index scan.
* Nắm được khi nào dùng cursor-based pagination: phù hợp với dữ liệu lớn, real-time feed; offset phù hợp với UI phân trang truyền thống.
* Kinh nghiệm: selective `include` (chỉ join field cần thiết) tốt hơn full `include` — tránh lấy quá nhiều dữ liệu không cần thiết.
