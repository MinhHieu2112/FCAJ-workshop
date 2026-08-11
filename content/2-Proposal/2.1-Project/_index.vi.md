---
title: "Trải nghiệm ứng dụng thực tế"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 2.1. </b> "
---

#### Trải nghiệm ứng dụng thực tế

**Hệ thống quản lý bất động sản cho thuê** đã được triển khai hoàn chỉnh và sẵn sàng trải nghiệm trực tiếp. Bạn có thể truy cập ứng dụng client được deploy trên Vercel và kết nối với backend đám mây AWS:

{{% notice tip %}}
**Đường dẫn ứng dụng thực tế (Production):** [https://real-estate-client-one-eta.vercel.app/](https://real-estate-client-one-eta.vercel.app/)
{{% /notice %}}

#### Kiến trúc hệ thống & stack công nghệ

Dự án được thiết kế theo mô hình monorepo (`pnpm workspaces`) kết hợp giao diện hiện đại, dịch vụ REST & WebSocket backend mạnh mẽ và các dịch vụ đám mây AWS tiêu chuẩn doanh nghiệp.

![Sơ đồ kiến trúc](/images/2-Proposal/AWS_architect.png)

#### Phân tích stack công nghệ

| Tầng hệ thống | Công nghệ & thư viện | Mục đích sử dụng |
|---|---|---|
| **Frontend** | Next.js 14 (App Router), TypeScript, Redux Toolkit / RTK Query, Tailwind CSS | Giao diện người dùng hiệu năng cao, hỗ trợ rendering phía server (SSR) và quản lý trạng thái client |
| **Backend** | NestJS, TypeScript, Socket.IO, Class-Validator | RESTful API dạng module hóa và cổng WebSocket giao tiếp thời gian thực |
| **Database & ORM** | Amazon RDS PostgreSQL, PostGIS, Prisma ORM | Lưu trữ dữ liệu quan hệ kết hợp truy vấn dữ liệu không gian vị trí (`ST_DWithin`) |
| **Xác thực** | Amazon Cognito User Pool, AWS Amplify SDK | Quản lý định danh người dùng, phát hành chuỗi mã JWT và phân quyền theo vai trò |
| **Lưu trữ media** | Amazon S3, AWS SDK v3 (Presigned URLs) | Tải ảnh trực tiếp an toàn từ trình duyệt lên S3 thông qua presigned URL có thời hạn ngắn |
| **Định vị & bản đồ** | Amazon Location Service | Chuyển đổi địa chỉ thành tọa độ (geocoding), gợi ý địa chỉ tự động và hiển thị bản đồ |
| **Thông báo** | Amazon SES (Simple Email Service) | Gửi email thông báo tự động khi trạng thái đơn xin thuê hoặc hợp đồng thay đổi |

---

#### Các mô-đun chức năng cốt lõi

#### 1. Tìm kiếm bất động sản & bản đồ không gian tương tác

Người thuê có thể tìm kiếm bất động sản thông qua bộ lọc đa tiêu chí (khoảng giá, số phòng ngủ, tiện nghi) kết hợp với bản đồ vị trí trực quan.

- **Tích hợp geocoding:** Chuỗi địa chỉ nhập vào khi tạo tin đăng được tự động chuyển đổi thành tọa độ địa lý `(Vĩ độ, Kinh độ)` thông qua **Amazon Location Service**.
- **Truy vấn không gian:** Chỉ mục PostGIS cho phép tìm kiếm các bất động sản nằm trong bán kính chỉ định tính từ vị trí bất kỳ trên bản đồ.

![Demo Amazon Location Service](/images/5-Workshop/5.5-Location-Service/5.5.4-demo.png)

#### 2. Quyền trình duyệt đơn thuê & hợp đồng số

Hệ thống tự động hóa toàn bộ vòng đời của giao dịch cho thuê nhà ở:

1. **Gửi đơn:** Người thuê nộp đơn xin thuê kèm thông tin cá nhân và ngày dự kiến chuyển vào.
2. **Xét duyệt:** Chủ nhà nhận thông báo thời gian thực trên bảng điều khiển để chấp thuận hoặc từ chối đơn.
3. **Khởi tạo hợp đồng:** Khi đơn được chấp thuận, hệ thống tự động tạo hợp đồng thuê trong một transaction CSDL an toàn (`prisma.$transaction`).
4. **Chữ ký số:** Chủ nhà ký hợp đồng trực tiếp trên bảng vẽ chữ ký số. Sau khi ký, hợp đồng được khóa và chuyển đến người thuê để xác nhận cuối cùng.

#### 3. Nhắn tin thời gian thực & email thông báo

- **Trò chuyện trong ứng dụng:** Kênh nhắn tin WebSocket tích hợp sẵn cho phép người thuê và chủ nhà trao đổi trực tiếp theo từng bất động sản.
- **Cảnh báo email:** Email tự động được gửi qua **Amazon SES** khi có thay đổi trạng thái đơn thuê (ví dụ: đã nhận đơn, đơn được duyệt, hoặc hợp đồng được khởi tạo).

---

#### Điểm sáng kỹ thuật & tối ưu hiệu năng

- **Xóa bỏ N+1 query:** Sử dụng eager loading với Prisma `include` giúp giảm thời gian phản hồi API từ ~800ms xuống còn ~120ms (P95).
- **Kiểm soát truy cập đồng thời:** Transaction CSDL kết hợp khóa bi quan (`SELECT ... FOR UPDATE`) ngăn ngừa tình trạng trùng lặp đơn thuê hoặc race condition.
- **Tải ảnh trực tiếp lên S3:** Giảm 90% băng thông máy chủ backend bằng cách cho phép client tải tệp trực tiếp lên S3 qua presigned URL.
