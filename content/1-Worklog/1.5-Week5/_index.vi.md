---
title: "Worklog Tuần 5"
date: 2026-07-20
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Hoàn thiện giao diện bảng điều khiển (dashboard) dành cho chủ nhà/quản lý (manager) ở ứng dụng Next.js.
* Xây dựng chức năng chỉnh sửa bất động sản: cập nhật thông tin tin đăng, tải bổ sung hoặc xóa ảnh cũ trên Amazon S3.
* Chuyển đổi cơ sở dữ liệu từ môi trường local sang **Amazon RDS (PostgreSQL + PostGIS managed)**.
* Sửa lỗi hệ thống và tối ưu hóa kết nối, hiệu năng truy vấn dữ liệu.
* Báo cáo tiến độ và demo các chức năng hoàn thành với mentor.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Hoàn thiện giao diện bảng điều khiển cho chủ nhà (Manager Dashboard): <br>&emsp; + Xây dựng trang tổng quan danh sách bất động sản đang quản lý với các tab trạng thái (Available, Rented, Pending) <br>&emsp; + Xây dựng bảng theo dõi danh sách đơn thuê (Application) cần xét duyệt <br>&emsp; + Hiển thị thẻ thống kê nhanh: số tin đăng, số đơn thuê mới, hợp đồng đang hoạt động | 20/07/2026 | <https://nextjs.org/docs> |
| 3 | - Xây dựng chức năng chỉnh sửa bất động sản: <br>&emsp; + Xây dựng form chỉnh sửa tin đăng ở frontend với đầy đủ thông tin: giá thuê, diện tích, tiện nghi, vị trí <br>&emsp; + Xây dựng logic quản lý ảnh: cho phép upload thêm ảnh mới lên Amazon S3 hoặc chọn xóa ảnh không còn dùng <br>&emsp; + Tích hợp API `PATCH /properties/:id` ở backend NestJS | 21/07/2026 | <https://docs.aws.amazon.com/s3/> |
| 4 | - Chuyển đổi cơ sở dữ liệu sang Amazon RDS: <br>&emsp; + Khởi tạo instance Amazon RDS PostgreSQL (db.t3.micro, single-AZ) trên AWS Console <br>&emsp; + Cấu hình Security Group cho phép kết nối an toàn từ IP backend <br>&emsp; + Cài đặt phần mở rộng PostGIS trên Amazon RDS <br>&emsp; + Thực thi `prisma db push` / `prisma migrate deploy` chuyển đổi schema và seed dữ liệu lên RDS | 22/07/2026 | <https://docs.aws.amazon.com/rds/> |
| 5 | - Sửa lỗi và tối ưu hiệu năng hệ thống: <br>&emsp; + Cấu hình connection pooling cho Prisma kết nối tới Amazon RDS <br>&emsp; + Khắc phục các lỗi ngoại lệ khi cập nhật trạng thái bất động sản và đơn thuê <br>&emsp; + Kiểm tra độ trễ (latency) khi gọi CSDL trên đám mây và tối ưu các câu truy vấn chậm | 23/07/2026 | <https://www.prisma.io/docs/guides/performance-and-optimization> |
| 6 | - Kiểm thử toàn bộ dashboard chủ nhà và Amazon RDS: <br>&emsp; + Test luồng hoàn chỉnh: manager tạo tin đăng → sửa tin → quản lý ảnh S3 → duyệt đơn thuê trên database Amazon RDS <br>&emsp; + Đánh giá tiến độ làm việc tuần 5 với mentor và ghi nhận phản hồi để tối ưu ở tuần 6 | 24/07/2026 | |

---

### Kết quả đạt được tuần 5:

* Hoàn thiện giao diện Manager Dashboard: cung cấp công cụ quản lý bất động sản, theo dõi đơn thuê và hợp đồng trực quan cho chủ nhà.

* Hoàn thành chức năng chỉnh sửa bất động sản: hỗ trợ cập nhật thông tin tin đăng linh hoạt, đồng bộ việc thêm/xóa ảnh trực tiếp trên Amazon S3.

* Chuyển đổi thành công cơ sở dữ liệu sang **Amazon RDS (PostgreSQL + PostGIS)**: môi trường cơ sở dữ liệu được quản lý tập trung trên AWS, hoạt động ổn định và bảo mật.

* Khắc phục các lỗi về cập nhật dữ liệu đồng thời, thiết lập thành công connection pooling cho Prisma giúp tối ưu kết nối tới Amazon RDS.

* Demo thành công tiến độ tuần 5 với mentor, xác nhận luồng quản lý bất động sản trên Cloud RDS vận hành đúng yêu cầu.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu cách khởi tạo và cấu hình cơ sở dữ liệu quan hệ Amazon RDS PostgreSQL trên đám mây, thiết lập Security Group theo nguyên tắc tối thiểu quyền (least privilege).
* Nắm được kỹ thuật kích hoạt và quản lý phần mở rộng PostGIS trên Amazon RDS để phục vụ các truy vấn dữ liệu địa lý.
* Nắm vững kỹ thuật xử lý form chỉnh sửa phức tạp kết hợp quản lý trạng thái tải ảnh mới và xóa ảnh cũ trên Amazon S3.
* Kinh nghiệm tối ưu connection pool cho ORM khi chuyển từ cơ sở dữ liệu local sang cơ sở dữ liệu managed trên đám mây.
