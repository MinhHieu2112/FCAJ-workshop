---
title: "Worklog Tuần 7"
date: 2026-08-03
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

* Tìm hiểu và xử lý vấn đề **Race Condition** trong hệ thống.
* Áp dụng **Transaction** và **Locking** (Optimistic/Pessimistic) để đảm bảo tính nhất quán dữ liệu.
* Tìm hiểu và triển khai các biện pháp bảo mật hệ thống.
* Hoàn thiện **Authentication**, **Authorization** và **Input Validation**.
* Kiểm thử bảo mật và sửa lỗi.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Tìm hiểu Race Condition trong hệ thống: <br>&emsp; + Xác định các điểm có nguy cơ Race Condition: Booking cùng lúc cho 1 property, cập nhật số dư thanh toán đồng thời <br>&emsp; + Phân tích kịch bản: 2 users cùng book 1 phòng ở cùng thời điểm <br>&emsp; + Tìm hiểu các cơ chế xử lý: Optimistic Locking (version field), Pessimistic Locking (SELECT FOR UPDATE) <br>&emsp; + So sánh trade-off giữa Optimistic và Pessimistic Locking | 03/08/2026 | <https://www.postgresql.org/docs/current/explicit-locking.html> |
| 3 | - Áp dụng Transaction và Locking: <br>&emsp; + Implement **Prisma Transaction** (`$transaction`) cho các thao tác cần atomic <br>&emsp; + Áp dụng **Pessimistic Locking** (`SELECT FOR UPDATE`) cho Booking creation để tránh double-booking <br>&emsp; + Áp dụng **Optimistic Locking** với version field cho Payment update <br>&emsp; + Viết integration test để verify race condition đã được xử lý | 04/08/2026 | <https://www.prisma.io/docs/concepts/components/prisma-client/transactions> |
| 4 | - Tìm hiểu các biện pháp bảo mật hệ thống: <br>&emsp; + **OWASP Top 10**: SQL Injection, XSS, CSRF, Broken Authentication, Sensitive Data Exposure <br>&emsp; + Rate limiting để chống brute-force <br>&emsp; + HTTPS/TLS, CORS configuration <br>&emsp; + Helmet.js để set security HTTP headers <br>&emsp; + Input sanitization để ngăn XSS <br>&emsp; + Environment variable management (không hardcode secrets) | 05/08/2026 | <https://owasp.org/www-project-top-ten/> |
| 5 | - Hoàn thiện Authentication, Authorization và Validation: <br>&emsp; + Implement **Refresh Token Rotation**: invalidate token cũ sau mỗi lần refresh <br>&emsp; + Implement account lockout sau nhiều lần đăng nhập sai <br>&emsp; + Bổ sung validation đầy đủ cho tất cả DTO (class-validator decorators) <br>&emsp; + Implement global Exception Filter để xử lý lỗi nhất quán <br>&emsp; + Sanitize tất cả user input trước khi lưu vào database | 06/08/2026 | <https://docs.nestjs.com/exception-filters> |
| 6 | - Kiểm thử bảo mật và sửa lỗi: <br>&emsp; + Test các kịch bản tấn công cơ bản: SQL Injection, XSS attempt, CSRF <br>&emsp; + Kiểm tra rate limiting hoạt động đúng <br>&emsp; + Test Race Condition với concurrent requests (dùng Postman Runner hoặc k6) <br>&emsp; + Fix các lỗ hổng bảo mật phát hiện được <br>&emsp; + Review toàn bộ Authorization logic | 07/08/2026 | |

---

### Kết quả đạt được tuần 7:

* Xác định và xử lý thành công **Race Condition** trong chức năng đặt lịch xem nhà:
  * Áp dụng `SELECT FOR UPDATE` trong Prisma Transaction, đảm bảo chỉ 1 booking được tạo khi có nhiều request đồng thời cho cùng 1 slot.
  * Test concurrent với 10 requests đồng thời → chỉ 1 booking thành công, 9 request còn lại nhận lỗi phù hợp.

* Áp dụng **Optimistic Locking** cho Payment update với version field — phát hiện xung đột và yêu cầu retry thay vì ghi đè dữ liệu.

* Triển khai các biện pháp bảo mật:
  * Helmet.js với security headers đầy đủ (CSP, HSTS, X-Frame-Options)
  * Rate limiting: 10 requests/phút cho login endpoint
  * CORS chỉ cho phép origin từ Frontend domain
  * Tất cả DTO được validate và sanitize

* Hoàn thiện **Refresh Token Rotation** — mỗi token chỉ dùng được 1 lần, tăng cường bảo mật đáng kể.

* Implement **account lockout** sau 5 lần đăng nhập sai liên tiếp (lockout 15 phút).

* Kiểm thử bảo mật xác nhận hệ thống đề kháng được các attack vectors cơ bản trong OWASP Top 10.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu rõ sự khác biệt giữa Optimistic và Pessimistic Locking: Optimistic phù hợp khi xung đột hiếm xảy ra (tốt cho performance), Pessimistic phù hợp khi xung đột thường xuyên (đảm bảo tính nhất quán).
* Nắm được nguyên tắc Refresh Token Rotation: token sau khi dùng phải bị invalidate ngay lập tức để ngăn replay attack.
* Học cách đọc OWASP Top 10 và áp dụng từng biện pháp phòng thủ cụ thể vào hệ thống thực tế.
* Kinh nghiệm: security không phải tính năng bổ sung cuối — nó phải được thiết kế từ đầu và kiểm tra liên tục.
