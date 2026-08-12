---
title: "Nhật ký tuần 6"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

* Phát triển module **message**: xây dựng tính năng trò chuyện (chat) thời gian thực giữa người thuê và chủ nhà qua WebSocket.
* Xây dựng giao diện trò chuyện gắn theo từng bất động sản và lưu trữ lịch sử tin nhắn vào CSDL.
* Phát triển module **notification**: tạo hệ thống thông báo trong ứng dụng cho tenant và manager.
* Tích hợp dịch vụ **Amazon SES** để tự động gửi email thông báo khi trạng thái đơn thuê hoặc hợp đồng thay đổi.
* Kiểm thử luồng giao tiếp thời gian thực và gửi email thông báo qua Amazon SES.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Phát triển WebSocket Gateway cho module **message**: <br>&emsp; + Khởi tạo WebSocket Gateway bằng NestJS WebSockets (`@nestjs/websockets`) <br>&emsp; + Xây dựng middleware xác thực kết nối WebSocket bằng JWT token <br>&emsp; + Quản lý danh sách kết nối (socket instances) của tenant và manager | 27/07/2026 | <https://docs.nestjs.com/websockets/gateways> |
| 3 | - Hoàn thiện chức năng trò chuyện thời gian thực (Real-time Chat): <br>&emsp; + Định nghĩa Prisma model Message và API lấy lịch sử trò chuyện theo `propertyId` <br>&emsp; + Xây dựng giao diện khung chat trò chuyện thời gian thực ở frontend Next.js <br>&emsp; + Xử lý sự kiện gửi/nhận tin nhắn tức thì và tự động cuộn đến tin nhắn mới nhất | 28/07/2026 | <https://socket.io/docs/v4/> |
| 4 | - Tích hợp dịch vụ **Amazon SES** gửi email tự động: <br>&emsp; + Cấu hình Amazon SES (Simple Email Service) trên AWS Console, xác minh email người gửi <br>&emsp; + Xây dựng `SesService` trong NestJS sử dụng AWS SDK v3 `SendEmailCommand` <br>&emsp; + Tạo mẫu template email: thông báo nộp đơn xin thuê, thông báo đơn thuê được duyệt/từ chối | 29/07/2026 | <https://docs.aws.amazon.com/ses/> |
| 5 | - Xây dựng module **notification** (thông báo in-app): <br>&emsp; + Định nghĩa Prisma model Notification và các type thông báo (APPLICATION_SUBMITTED, APPLICATION_APPROVED, LEASE_CREATED) <br>&emsp; + Xây dựng API lấy danh sách thông báo và đánh dấu đã đọc <br>&emsp; + Hiển thị badge số thông báo chưa đọc trên thanh thanh menu giao diện | 30/07/2026 | |
| 6 | - Kiểm thử luồng giao tiếp và thông báo: <br>&emsp; + Kiểm thử trò chuyện thời gian thực giữa người thuê và chủ nhà trên 2 trình duyệt khác nhau <br>&emsp; + Xử lý cơ chế tự động kết nối lại (reconnect) khi rớt mạng WebSocket <br>&emsp; + Kiểm thử việc nhận email thông báo thực tế từ Amazon SES khi thực hiện các thao tác duyệt đơn thuê | 31/07/2026 | |

---

### Kết quả đạt được tuần 6:

* Phát triển hoàn thành **Message module**: hỗ trợ trò chuyện thời gian thực giữa tenant và manager qua WebSocket với xác thực JWT an toàn.

* Xây dựng thành công **giao diện khung chat** thời gian thực ở ứng dụng Next.js, hiển thị lịch sử trò chuyện chính xác theo từng bất động sản.

* Tích hợp thành công **Amazon SES**: hệ thống tự động gửi email thông báo chuẩn định dạng HTML tới người dùng khi đơn thuê được duyệt hoặc khởi tạo hợp đồng.

* Xây dựng xong **Notification module**: cung cấp hệ thống thông báo trực tiếp trên giao diện (in-app notification) kèm trạng thái đã đọc/chưa đọc.

* Kiểm thử luồng giao tiếp hoàn tất: đảm bảo tin nhắn truyền tải thời gian thực mượt mà và email gửi qua Amazon SES đến hòm thư người nhận ổn định.

---

### Kiến thức / Kinh nghiệm học được:

* Nắm vững kiến trúc WebSocket trong NestJS Gateway: phân biệt giữa HTTP REST API (stateless) và WebSocket Connection (stateful).
* Hiểu cách xác thực kết nối WebSocket bằng JWT bearer token ngay từ bước handshake để bảo mật kênh giao tiếp.
* Học cách tích hợp AWS SDK v3 cho Amazon SES để gửi email giao dịch (transactional email) trong ứng dụng backend.
* Kinh nghiệm xử lý giao diện trò chuyện: quản lý tin nhắn thời gian thực với React state, xử lý tự động cuộn trang (scroll to bottom) và trạng thái online/offline.
