---
title: "Rentiful - Ứng dụng quản lý cho thuê bất động sản"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 2.1. </b> "
---

### 1. Đặt vấn đề

Thị trường cho thuê nhà ở hiện nay vẫn phụ thuộc nhiều vào các kênh truyền thống không chính thức như nhóm mạng xã hội, tin nhắn cá nhân hoặc nhà môi giới trung gian. Quy trình từ tìm kiếm tin đăng, thỏa thuận đến ký kết hợp đồng thường kéo dài, thiếu công cụ quản lý chuyên biệt và tiềm ẩn nhiều rủi ro cho cả hai bên.

Các hạn chế chính bao gồm:
- Phía chủ nhà và quản lý bất động sản: Thiếu bảng điều khiển tập trung để theo dõi danh mục bất động sản, trạng thái phòng trống, danh sách đơn xin thuê và lịch sử hợp đồng.
- Phía người thuê: Thiếu công cụ tìm kiếm kết hợp vị trí bản đồ trực quan và bộ lọc đa tiêu chí (mức giá, số phòng, tiện nghi); không có kênh theo dõi tiến độ xét duyệt đơn và giao tiếp tập trung.
- Giao tiếp và thông báo: Việc trao đổi qua các ứng dụng tin nhắn bên ngoài dễ gây thất lạc thông tin; thiếu cơ chế tự động gửi email thông báo khi trạng thái đơn xin thuê hoặc hợp đồng có sự thay đổi.

---

### 2. Giải pháp
![Sơ đồ phân quyền vai trò người dùng](/images/2-Proposal/Role.png)
Hệ thống quản lý cho thuê bất động sản được phát triển nhằm giải quyết triệt để các hạn chế trên thông qua một nền tảng ứng dụng web tập trung.

Các chức năng chính bao gồm:
- Quản lý danh mục bất động sản: Chủ nhà có thể tạo tin đăng, chỉnh sửa thông tin, tải lên hình ảnh và quản lý trạng thái các bất động sản đang cho thuê.
- Tìm kiếm bất động sản và tương tác bản đồ: Người thuê tìm kiếm bất động sản theo bộ lọc đa tiêu chí kết hợp bản đồ địa lý không gian.
- Quản lý đơn xin thuê: Người thuê nộp đơn xin thuê trực tuyến; chủ nhà nhận thông báo và thực hiện xét duyệt (chấp thuận hoặc từ chối) đơn trên bảng điều khiển.
- Khởi tạo hợp đồng và chữ ký số: Khi đơn xin thuê được chấp thuận, hệ thống tự động lập hợp đồng thuê trong một giao dịch cơ sở dữ liệu an toàn và hỗ trợ ký hợp đồng trực tiếp trên giao diện.
- Trò chuyện thời gian thực: Tích hợp kênh nhắn tin trực tiếp qua kết nối WebSocket giữa người thuê và chủ nhà cho từng bất động sản.
- Tự động hóa thông báo: Tự động gửi email thông báo qua giao thức SMTP (hoặc Amazon SES) khi có cập nhật về trạng thái đơn xin thuê hoặc hợp đồng.

---

### 3. Các công nghệ sử dụng

Hệ thống được phát triển theo mô hình monorepo với các công nghệ hiện đại:

- Frontend: Next.js (App Router), TypeScript, Redux Toolkit, RTK Query và Tailwind CSS.
- Backend: NestJS, TypeScript, Socket.IO (WebSocket) và Class-Validator.
- Cơ sở dữ liệu: Amazon RDS PostgreSQL tích hợp phần mở rộng không gian PostGIS, truy cập qua Prisma ORM.
- Xác thực và phân quyền: Amazon Cognito User Pool kết hợp NestJS AuthGuard và RolesGuard (phân quyền vai trò `TENANT` và `MANAGER`).
- Lưu trữ tệp media: Amazon S3 (tải ảnh trực tiếp từ trình duyệt qua Presigned URL).
- Định vị địa lý và bản đồ: Amazon Location Service (geocoding địa chỉ và hiển thị bản đồ).
- Dịch vụ thông báo email: Gửi email tự động qua SMTP (Nodemailer) / Amazon SES.
- Máy chủ và reverse proxy: Amazon EC2 chạy Docker runtime và Caddy HTTP/HTTPS reverse proxy.

---

### 4. Kiến trúc hạ tầng

Hệ thống được triển khai trên hạ tầng điện toán đám mây AWS với mô hình mạng VPC hai tầng (public subnet và private subnet):

- Frontend Vercel (HTTPS): Ứng dụng client Next.js được triển khai trên Vercel, giao tiếp an toàn với backend thông qua tên miền DuckDNS và chứng chỉ SSL/TLS tự động từ Caddy.
- Tầng Public Subnet: Gắn Elastic IP cố định, chạy Caddy reverse proxy trên EC2 để tiếp nhận traffic HTTP/HTTPS (port 80/443) và chuyển tiếp tới ứng dụng backend NestJS trong container Docker.
- Tầng Private Subnet: Khởi tạo Amazon RDS PostgreSQL (PostGIS) trong private subnet, chỉ cho phép kết nối nội bộ từ máy chủ EC2 thông qua quy tắc security group (`sg-rds-private`).
- Quản lý bí mật và quyền truy cập: Lưu trữ các biến môi trường nhạy cảm trên AWS Secrets Manager và gán IAM Role (`RentifulEC2SecretManagerRole`) cho EC2 instance để đọc secret tự động khi khởi động.

Sơ đồ kiến trúc tổng thể:

![Sơ đồ kiến trúc](/images/5-Workshop/5.1-Overview/AWS_architect.png)

---

### 5. Chi phí dự kiến

Toàn bộ chi phí vận hành hệ thống được tối ưu nhằm nằm trong hạn mức AWS Free Tier và gói hỗ trợ AWS Credits:
![Estimated cost](/images/2-Proposal/Cost.png)
Biện pháp kiểm soát chi phí:
- Thiết lập cảnh báo AWS Budget tự động gửi email khi chi phí chạm mốc 50 USD và 100 USD.
- Sử dụng mô hình RDS Single-AZ trong giai đoạn phát triển.
- Áp dụng kỹ thuật debounce ở phía client nhằm giảm số lượng request geocoding gửi đến Amazon Location Service.

---

### 6. Demo

Ứng dụng thực tế đã được triển khai hoàn chỉnh trên môi trường production và sẵn sàng truy cập trải nghiệm:

- Đường dẫn ứng dụng thực tế (Production App): [https://real-estate-client-one-eta.vercel.app/](https://real-estate-client-one-eta.vercel.app/)

Hình ảnh minh họa chức năng hệ thống:

![Bản đồ vị trí bất động sản](/images/5-Workshop/5.5-Location-Service/5.5.4-demo.png)

