---
title: "Bản đề xuất"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Hệ thống quản lý cho thuê bất động sản
## Giải pháp phần mềm tích hợp AWS cho thị trường cho thuê nhà ở

---

### 1. Tóm tắt điều hành

Hệ thống quản lý cho thuê bất động sản là một nền tảng phần mềm hỗ trợ toàn bộ vòng đời của giao dịch cho thuê nhà ở — từ đăng tin, tìm kiếm, đặt lịch xem nhà, ký hợp đồng đến quản lý thanh toán và thông báo — trên một nền tảng thống nhất.

Hệ thống phục vụ ba nhóm người dùng chính: **chủ nhà (landlord)** quản lý danh sách bất động sản và xử lý yêu cầu thuê; **người thuê (tenant)** tìm kiếm, đặt lịch và ký hợp đồng trực tuyến; **quản trị viên (admin)** kiểm duyệt nội dung và giám sát hoạt động toàn hệ thống.

Về mặt kỹ thuật, hệ thống được xây dựng trên kiến trúc monorepo với backend **NestJS** (TypeScript), frontend **Next.js** (App Router), cơ sở dữ liệu **PostgreSQL** truy cập qua **Prisma ORM**, và tích hợp các dịch vụ AWS bao gồm **Amazon S3**, **Amazon SES**, **Amazon Cognito**, **Amazon RDS** và **Amazon CloudFront**. Mục tiêu của dự án là đưa vào vận hành một hệ thống đủ tính năng, có khả năng mở rộng, và đáp ứng các tiêu chuẩn bảo mật cơ bản trong môi trường cloud.

---

### 2. Tuyên bố vấn đề

#### Bối cảnh

Thị trường cho thuê nhà ở hiện nay vẫn phụ thuộc nhiều vào các kênh không chính thức: nhóm Facebook, Zalo, tờ rơi hoặc môi giới trung gian. Quy trình từ đăng tin đến ký hợp đồng thường kéo dài và thiếu minh bạch, gây khó khăn cho cả chủ nhà lẫn người thuê.

Cụ thể, các vấn đề nổi bật bao gồm:

- **Phía chủ nhà**: Không có công cụ quản lý tập trung cho nhiều bất động sản; thông tin phòng trống, lịch xem nhà và hợp đồng phải theo dõi thủ công qua bảng tính hoặc ghi chép cá nhân.
- **Phía người thuê**: Thiếu bộ lọc tìm kiếm theo nhiều tiêu chí (giá, diện tích, vị trí, tiện nghi); không có cơ chế xác nhận lịch hẹn xem nhà hoặc theo dõi trạng thái yêu cầu thuê.
- **Về bảo mật thông tin**: Hợp đồng và thông tin cá nhân thường được trao đổi qua kênh không bảo mật, tiềm ẩn rủi ro dữ liệu.

#### Giải pháp đề xuất

Hệ thống được xây dựng để giải quyết từng điểm trên thông qua một nền tảng web tập trung, trong đó:

- Chủ nhà có dashboard quản lý toàn bộ bất động sản, lịch xem nhà và hợp đồng.
- Người thuê có giao diện tìm kiếm với bộ lọc đa tiêu chí, đặt lịch trực tuyến và theo dõi trạng thái yêu cầu theo thời gian thực.
- Hệ thống xác thực và phân quyền đảm bảo mỗi vai trò chỉ truy cập được dữ liệu thuộc phạm vi cho phép.
- Các thông báo quan trọng (xác nhận lịch hẹn, cập nhật hợp đồng) được gửi tự động qua email.

---

### 3. Kiến trúc giải pháp

Hệ thống được tổ chức theo mô hình **monorepo**, tách biệt rõ ràng giữa backend, frontend và thư viện dùng chung (`@shared/types`).

#### Sơ đồ kiến trúc tổng thể

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                         │
│              Next.js App (App Router, SSR/CSR)              │
│         React Components · Zustand · Axios Interceptor       │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS / REST API
┌────────────────────────▼────────────────────────────────────┐
│                       Backend Layer                         │
│              NestJS (TypeScript · Modular DI)               │
│  Auth · Property · Booking · Contract · Notification · Admin │
│         JWT Guards · Role Guards · Class-Validator           │
└────┬──────────────┬──────────────┬──────────────────────────┘
     │              │              │
┌────▼────┐   ┌─────▼─────┐  ┌────▼────────────────────────┐
│ AWS RDS │   │ Amazon S3 │  │  AWS Services                │
│PostgreSQL│  │(+CloudFront│  │  SES · Cognito               │
└─────────┘   └───────────┘  └─────────────────────────────┘
```

#### Các dịch vụ AWS sử dụng

| Dịch vụ | Vai trò trong hệ thống |
|---------|------------------------|
| **Amazon RDS (PostgreSQL)** | Cơ sở dữ liệu chính, lưu trữ toàn bộ dữ liệu nghiệp vụ |
| **Amazon S3** | Lưu trữ ảnh bất động sản; truy cập qua presigned URL |
| **Amazon CloudFront** | CDN phân phối ảnh từ S3, giảm latency cho client |
| **Amazon SES** | Gửi email xác thực tài khoản và thông báo nghiệp vụ |
| **Amazon Cognito** | Quản lý xác thực người dùng, tích hợp với JWT flow |

#### Thiết kế module backend

Backend NestJS được tổ chức theo module độc lập, mỗi module đảm nhiệm một miền nghiệp vụ riêng:

- **Auth module**: đăng ký, đăng nhập, refresh token, Google OAuth2, refresh token rotation.
- **Property module**: CRUD bất động sản, upload ảnh qua S3, filter/search đa tiêu chí.
- **Booking module**: quản lý lịch xem nhà với state machine (PENDING → CONFIRMED/CANCELLED).
- **Contract module**: tạo và lưu trữ hợp đồng thuê nhà.
- **Notification module**: gửi thông báo email qua SES khi trạng thái booking thay đổi.
- **Admin module**: dashboard thống kê, duyệt/từ chối property listing.

---

### 4. Triển khai kỹ thuật

#### Stack công nghệ và lý do lựa chọn

**Backend — NestJS (TypeScript)**

NestJS được chọn vì kiến trúc module hóa rõ ràng phù hợp với hệ thống nhiều miền nghiệp vụ. Cơ chế Dependency Injection giúp viết unit test dễ dàng và tách biệt concern giữa các layer (Controller → Service → Repository). TypeScript đảm bảo type safety xuyên suốt, giảm lỗi runtime khi tích hợp với Prisma ORM.

**Frontend — Next.js (App Router)**

Next.js với App Router cho phép kết hợp Server Components (tăng tốc initial load, SEO) và Client Components (interactive UI) một cách linh hoạt. Axios interceptor xử lý JWT refresh tự động, giúp trải nghiệm người dùng liền mạch ngay cả khi access token hết hạn.

**Database — PostgreSQL + Prisma ORM**

PostgreSQL phù hợp với mô hình dữ liệu quan hệ của hệ thống (User ↔ Property ↔ Booking ↔ Contract). Prisma cung cấp type-safe database client, schema migration và query builder — giảm lỗi SQL thủ công và tăng tốc phát triển.

#### Các vấn đề kỹ thuật được giải quyết

**Xác thực và phân quyền**

Hệ thống sử dụng JWT với hai loại token: access token ngắn hạn (15 phút) và refresh token dài hạn (7 ngày). Refresh token rotation đảm bảo mỗi token chỉ được dùng một lần, ngăn chặn replay attack. `AuthGuard` và `RolesGuard` được áp dụng ở cấp controller để đảm bảo phân quyền theo role (TENANT/LANDLORD/ADMIN).

**N+1 Query**

Trong quá trình phát triển, việc truy vấn danh sách bất động sản kèm ảnh và thông tin chủ nhà ban đầu gây ra N+1 queries — với 20 bất động sản, hệ thống thực hiện ~50 queries riêng lẻ. Vấn đề được giải quyết bằng cách áp dụng Prisma `include` để eager load các relation trong một query duy nhất, kết hợp với database indexing trên các column thường dùng để filter (price, location, status). Kết quả: P95 response time giảm từ ~800ms xuống ~120ms.

**Race Condition**

Kịch bản hai người dùng đặt lịch cùng một slot thời gian được xử lý bằng Pessimistic Locking (`SELECT FOR UPDATE` trong Prisma `$transaction`). Giải pháp đảm bảo chỉ một booking được tạo thành công, request còn lại nhận phản hồi lỗi có nghĩa thay vì gây ra dữ liệu không nhất quán.

**Lưu trữ ảnh bất động sản**

Ảnh được upload trực tiếp lên Amazon S3 và phân phối qua CloudFront CDN. Frontend truy cập ảnh qua presigned URL có thời hạn thay vì public URL — giảm rủi ro truy cập trái phép và kiểm soát được băng thông.

#### Các giai đoạn phát triển

| Tuần | Giai đoạn | Nội dung chính |
|------|-----------|----------------|
| 1–2 | Chuẩn bị & nghiên cứu | Onboarding, tìm hiểu AWS, đăng ký Free Tier & Credits |
| 3 | Phân tích & thiết kế | Nghiệp vụ, use case, kiến trúc, tech stack |
| 4 | Xây dựng nền tảng | Database schema, auth, tích hợp S3/SES/Cognito |
| 5 | Phát triển chức năng | Property, booking, contract, notification, frontend |
| 6 | Tối ưu hiệu năng | N+1 query, indexing, cursor pagination |
| 7 | Bảo mật & hardening | Race condition, transaction, OWASP, token rotation |
| 8 | Hoàn thiện & bàn giao | E2E testing, CloudFront, documentation, demo |

---

### 5. Lộ trình & Mốc triển khai

```
Tuần 1–2  │ ████ Chuẩn bị & AWS fundamentals
Tuần 3    │ ██   Phân tích nghiệp vụ & thiết kế hệ thống
Tuần 4    │ ███  Database · Auth · Tích hợp AWS
Tuần 5    │ ████ Phát triển tính năng chính · Frontend
Tuần 6    │ ██   Tối ưu N+1 query & hiệu năng database
Tuần 7    │ ███  Race condition · Bảo mật · Hardening
Tuần 8    │ ███  Hoàn thiện · Kiểm thử · Tài liệu · Bàn giao
```

**Mốc quan trọng:**

- **Tuần 2**: Nhận 200 USD AWS Credits, thiết lập AWS Budget.
- **Tuần 4**: Auth flow hoàn chỉnh (register → verify → login), S3 upload hoạt động.
- **Tuần 5**: Demo nội bộ các chức năng nghiệp vụ chính với mentor.
- **Tuần 6**: Xác nhận cải thiện P95 response time sau tối ưu N+1.
- **Tuần 8**: Demo sản phẩm hoàn chỉnh, bàn giao source code và tài liệu.

---

### 6. Ước tính ngân sách

Chi phí vận hành hệ thống trong môi trường phát triển và demo được kiểm soát thông qua **AWS Free Tier** và **200 USD AWS Credits** nhận được từ chương trình hỗ trợ sinh viên.

#### Chi phí hạ tầng AWS (ước tính môi trường development)

| Dịch vụ | Cấu hình | Chi phí ước tính |
|---------|----------|-----------------|
| Amazon RDS (PostgreSQL) | db.t3.micro, 20 GB SSD, single-AZ | ~15 USD/tháng |
| Amazon S3 | ~5 GB storage, ~10,000 requests/tháng | ~0.15 USD/tháng |
| Amazon CloudFront | ~10 GB transfer/tháng | ~0.85 USD/tháng |
| Amazon SES | ~500 email/tháng (trong sandbox) | 0 USD (Free Tier) |
| Amazon Cognito | <50,000 MAU | 0 USD (Free Tier) |
| **Tổng ước tính** | | **~16 USD/tháng** |

> Toàn bộ chi phí trong thời gian thực tập (8 tuần) nằm trong phạm vi 200 USD AWS Credits, không phát sinh chi phí thực tế.

#### Chiến lược kiểm soát chi phí

- Tạo **AWS Budget** với ngưỡng cảnh báo email ở mức 50 USD và 100 USD.
- Sử dụng **RDS single-AZ** thay vì Multi-AZ trong môi trường dev để giảm chi phí.
- Cấu hình **S3 lifecycle policy** để tự động xóa các file upload thử nghiệm sau 30 ngày.
- Tắt RDS instance ngoài giờ làm việc khi không có nhu cầu truy cập.

---

### 7. Đánh giá rủi ro

#### Ma trận rủi ro

| Rủi ro | Mức độ ảnh hưởng | Xác suất | Chiến lược giảm thiểu |
|--------|-----------------|----------|----------------------|
| Vượt ngưỡng AWS Credits | Trung bình | Thấp | AWS Budget alert; tắt tài nguyên khi không dùng |
| Lỗ hổng bảo mật auth | Cao | Thấp | Refresh token rotation, rate limiting, account lockout |
| Race condition trong booking | Cao | Trung bình | Pessimistic locking với `SELECT FOR UPDATE` |
| Hiệu năng database suy giảm | Trung bình | Trung bình | N+1 fix, indexing, cursor pagination |
| S3 presigned URL bị lạm dụng | Thấp | Thấp | Thời hạn URL ngắn, kiểm tra ownership trước khi generate |

#### Kế hoạch dự phòng

- Nếu RDS gặp sự cố: rollback từ automated snapshot (bật theo mặc định trên RDS).
- Nếu S3 upload thất bại: retry logic với exponential backoff ở tầng backend.
- Nếu SES bị throttle: queue email và gửi lại sau, không ảnh hưởng đến luồng nghiệp vụ chính.
- Nếu Cognito gặp sự cố: fallback về JWT-only flow (hệ thống vẫn hoạt động với auth module nội bộ).

---

### 8. Kết quả kỳ vọng

#### Kết quả kỹ thuật

Kết thúc giai đoạn phát triển, hệ thống dự kiến đạt được:

- Toàn bộ luồng nghiệp vụ chính vận hành ổn định: đăng ký, đăng nhập, đăng tin bất động sản, tìm kiếm, đặt lịch xem nhà, tạo hợp đồng.
- Hệ thống xác thực đạt tiêu chuẩn bảo mật cơ bản: JWT rotation, rate limiting, input validation, OWASP Top 10 compliance.
- Hiệu năng truy vấn database được tối ưu: P95 response time dưới 200ms cho các API danh sách với phân trang.
- Tài liệu kỹ thuật đầy đủ: README, Swagger API docs (40+ endpoints), sơ đồ kiến trúc.

#### Giá trị học tập và phát triển kỹ năng

Dự án được thiết kế để phản ánh môi trường phát triển phần mềm thực tế trong doanh nghiệp — từ giai đoạn phân tích nghiệp vụ, thiết kế kiến trúc, triển khai, tối ưu hiệu năng, đến bảo mật và bàn giao. Các kỹ năng cốt lõi tích lũy được bao gồm: thiết kế REST API theo chuẩn, tích hợp dịch vụ AWS trong ứng dụng thực tế, xử lý concurrency và tối ưu database — những kỹ năng có giá trị trực tiếp trong công việc kỹ sư phần mềm sau khi tốt nghiệp.

#### Định hướng mở rộng

Kiến trúc module hóa của NestJS cho phép mở rộng hệ thống mà không cần tái cấu trúc lớn. Các hướng phát triển tiếp theo có thể bao gồm: tích hợp thanh toán trực tuyến, bổ sung real-time chat giữa chủ nhà và người thuê, hoặc containerize toàn bộ hệ thống với Docker/ECS để hỗ trợ deployment linh hoạt hơn trên môi trường production.