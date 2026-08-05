---
title: "Worklog Tuần 5"
date: 2026-07-20
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Phát triển các chức năng nghiệp vụ chính của hệ thống (Property, Booking, Payment, Notification).
* Hoàn thiện API và xử lý các nghiệp vụ phức tạp (trạng thái đặt lịch, xử lý thanh toán).
* Tích hợp giao diện Frontend (Next.js) với Backend API.
* Kiểm thử chức năng và sửa lỗi phát sinh.
* Đánh giá tiến độ với mentor cuối tuần.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Phát triển các chức năng chính của hệ thống: <br>&emsp; + **Property module**: CRUD bất động sản, filter/search theo giá, diện tích, vị trí, loại nhà <br>&emsp; + **Booking module**: đặt lịch xem nhà, cập nhật trạng thái (PENDING → CONFIRMED → CANCELLED) <br>&emsp; + **Contract module**: tạo hợp đồng thuê nhà, lưu thông tin hợp đồng <br>&emsp; + Validate dữ liệu đầu vào với class-validator | 20/07/2026 | <https://docs.nestjs.com/> |
| 3 | - Hoàn thiện API và xử lý nghiệp vụ: <br>&emsp; + Implement Notification module: tạo notification khi booking thay đổi trạng thái <br>&emsp; + Implement Payment flow: tích hợp cổng thanh toán (mock), cập nhật trạng thái hợp đồng <br>&emsp; + Implement pagination và sorting cho API danh sách property <br>&emsp; + Viết Swagger documentation cho tất cả API endpoints | 21/07/2026 | <https://docs.nestjs.com/openapi/introduction> |
| 4 | - Tích hợp giao diện Frontend với Backend: <br>&emsp; + Cấu hình Next.js App Router, tạo các page chính: Home, Property List, Property Detail, Booking <br>&emsp; + Gọi API từ Frontend: sử dụng axios/fetch với interceptor cho JWT token <br>&emsp; + Implement context/state management (Zustand hoặc React Context) cho auth state <br>&emsp; + Xây dựng component: PropertyCard, BookingForm, NavigationBar | 22/07/2026 | <https://nextjs.org/docs> |
| 5 | - Kiểm thử chức năng toàn hệ thống: <br>&emsp; + Test luồng: đăng ký → đăng nhập → đăng tin → tìm kiếm → đặt lịch <br>&emsp; + Kiểm tra phân quyền: TENANT không thể đăng tin, LANDLORD không thể tự confirm booking của mình <br>&emsp; + Fix bug phát sinh trong quá trình test <br>&emsp; + Kiểm tra responsive design trên mobile | 23/07/2026 | |
| 6 | - Đánh giá tiến độ tổng thể với mentor: <br>&emsp; + Demo các chức năng đã hoàn thành <br>&emsp; + Nhận feedback về code quality và UX <br>&emsp; + Lên kế hoạch cho tuần 6: tối ưu hóa hiệu năng | 24/07/2026 | |

---

### Kết quả đạt được tuần 5:

* Hoàn thành các module nghiệp vụ chính:
  * **Property**: CRUD đầy đủ với filter/search, upload ảnh qua S3
  * **Booking**: quản lý vòng đời đặt lịch với state machine (PENDING → CONFIRMED/CANCELLED)
  * **Contract**: tạo và lưu trữ hợp đồng thuê nhà
  * **Notification**: tự động gửi thông báo khi trạng thái booking thay đổi

* Hoàn thiện **Swagger documentation** cho tất cả API endpoint, hỗ trợ test trực tiếp từ giao diện.

* Tích hợp thành công **Next.js Frontend** với Backend API:
  * Các page chính hoạt động: Home, Property Listing (với filter), Property Detail, Booking Form
  * JWT interceptor tự động refresh token khi hết hạn
  * Auth state được quản lý toàn cục qua context

* Xác nhận phân quyền hoạt động đúng theo từng role trong toàn bộ luồng nghiệp vụ.

* Nhận phản hồi tích cực từ mentor, đề xuất cải thiện: cần tối ưu số lượng query đến database.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu sâu về state machine trong quản lý trạng thái đặt lịch — cần định nghĩa rõ ràng các transition hợp lệ để tránh dữ liệu không nhất quán.
* Học cách implement pagination chuẩn (cursor-based vs offset-based): offset phù hợp cho UI phân trang, cursor phù hợp cho infinite scroll.
* Nắm được cách tổ chức Next.js App Router: Server Components vs Client Components, khi nào dùng `use client`.
* Kinh nghiệm thực tế: JWT interceptor cần xử lý race condition khi nhiều request cùng gọi refresh token endpoint.
