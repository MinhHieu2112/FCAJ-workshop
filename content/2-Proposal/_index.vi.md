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

### 1. Tóm tắt

Hệ thống quản lý cho thuê bất động sản là một nền tảng phần mềm hỗ trợ toàn bộ vòng đời của giao dịch cho thuê nhà ở — từ đăng tin bất động sản, tìm kiếm kết hợp bản đồ địa lý, nộp đơn xin thuê, xét duyệt đơn, tự động tạo hợp đồng thuê, quản lý lịch sử thanh toán đến nhắn tin thời gian thực và tự động gửi thông báo qua email — trên một nền tảng thống nhất.

Hệ thống phục vụ hai nhóm người dùng chính: **chủ nhà/quản lý (manager)** đăng tin, quản lý danh sách bất động sản đang cho thuê, xét duyệt đơn xin thuê và theo dõi hợp đồng; **người thuê (tenant)** tìm kiếm bất động sản theo nhiều tiêu chí kết hợp vị trí bản đồ, nộp đơn xin thuê trực tuyến, theo dõi trạng thái đơn và trao đổi trực tiếp với chủ nhà qua hệ thống trò chuyện thời gian thực.

Về mặt kỹ thuật, hệ thống được xây dựng trên kiến trúc monorepo (`pnpm workspaces`) với backend **NestJS** (TypeScript), frontend **Next.js** (App Router) dùng **Redux Toolkit / RTK Query**, cơ sở dữ liệu **PostgreSQL** mở rộng phần không gian **PostGIS** truy cập qua **Prisma ORM**, cùng sự tích hợp của các dịch vụ AWS bao gồm **Amazon Cognito** (xác thực người dùng), **Amazon S3** (lưu trữ hình ảnh), **Amazon RDS** (cơ sở dữ liệu đám mây), **Amazon SES** (gửi email thông báo) và **Amazon Location Service** (geocoding địa chỉ và hiển thị bản đồ). Dự án hướng đến việc triển khai một giải pháp phần mềm hoàn chỉnh về chức năng, tối ưu về hiệu năng và tuân thủ các tiêu chuẩn bảo mật trong môi trường đám mây.

---

### 2. Đặt vấn đề

#### Bối cảnh

Thị trường cho thuê nhà ở hiện nay vẫn phụ thuộc nhiều vào các kênh không chính thức như nhóm mạng xã hội, tin nhắn cá nhân hoặc môi giới trung gian. Quy trình từ đăng tin, tìm kiếm, thỏa thuận đến ký hợp đồng thường kéo dài, thiếu công cụ quản lý chuyên biệt và tiềm ẩn nhiều rủi ro về thông tin.

Cụ thể, các hạn chế nổi bật bao gồm:

- **Phía chủ nhà**: Không có bảng điều khiển tập trung để quản lý danh sách nhiều bất động sản; việc theo dõi trạng thái phòng trống, danh sách đơn xin thuê và thông tin hợp đồng phải thực hiện thủ công qua bảng tính hoặc sổ sách cá nhân.
- **Phía người thuê**: Thiếu công cụ tìm kiếm kết hợp bản đồ vị trí trực quan và bộ lọc đa tiêu chí (giá thuê, diện tích, số phòng, tiện nghi); không có kênh theo dõi tiến độ xét duyệt đơn xin thuê và giao tiếp tập trung.
- **Về giao tiếp và thông báo**: Việc trao đổi qua các ứng dụng nhắn tin cá nhân bên ngoài dễ làm thất lạc thông tin; thiếu cơ chế gửi email thông báo tự động khi phát sinh sự thay đổi trạng thái đơn thuê hoặc hợp đồng.

#### Giải pháp đề xuất

Hệ thống được phát triển nhằm giải quyết triệt để các hạn chế trên thông qua một ứng dụng web tập trung:

- Chủ nhà được trang bị bảng điều khiển (Manager Dashboard) để quản lý danh mục bất động sản, theo dõi các đơn xin thuê cần xử lý và xem chi tiết hợp đồng đã khởi tạo.
- Người thuê có giao diện tìm kiếm bất động sản trực quan theo bộ lọc và vị trí bản đồ, nộp đơn xin thuê trực tuyến và theo dõi trạng thái xử lý theo thời gian thực.
- Khi đơn thuê được duyệt, hệ thống tự động lập hợp đồng thuê (Lease) trong một transaction CSDL an toàn, đảm bảo kiểm soát chặt chẽ lịch thuê và ngăn ngừa trùng lặp.
- Người thuê và chủ nhà có thể nhắn tin trực tiếp qua kênh trò chuyện thời gian thực (real-time chat) được gắn theo từng bất động sản.
- Hệ thống tự động gửi email thông báo qua Amazon SES khi người thuê nộp đơn hoặc khi chủ nhà cập nhật trạng thái xét duyệt đơn.

---

### 3. Kiến trúc giải pháp

Hệ thống được tổ chức theo mô hình **monorepo** (`pnpm workspaces`), bao gồm backend (`apps/server`), frontend (`apps/client`) và thư viện kiểu dữ liệu dùng chung (`packages/types`, gói `@shared/types`) nhằm đảm bảo tính đồng bộ dữ liệu giữa hai phía.

#### Sơ đồ kiến trúc tổng thể

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                         │
│              Next.js App (App Router, SSR/CSR)              │
│        React Components · Redux Toolkit / RTK Query         │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS REST API + WebSocket
┌────────────────────────▼────────────────────────────────────┐
│                       Backend Layer                         │
│              NestJS (TypeScript · Modular DI)               │
│   Property · Application · Lease · Tenant · Manager ·       │
│   Message (Chat Real-time) · Notification · Location        │
│         AuthGuard · RolesGuard · Class-Validator            │
└────┬──────────────┬──────────────┬──────────────┬───────────┘
     │              │              │              │
┌────▼────┐   ┌─────▼─────┐  ┌─────▼─────┐  ┌─────▼───────────┐
│ AWS RDS │   │ Amazon S3 │  │ Amazon SES│  │ Amazon Location │
│PostgreSQL│  │ (ảnh      │  │ (email    │  │ Service         │
│+ PostGIS│   │ property) │  │ thông báo)│  │ (Geocode & Map) │
└─────────┘   └───────────┘  └───────────┘  └─────────────────┘
                     Amazon Cognito (User Pool & App Client)
                     — xác thực client, phát hành token JWT —
```

#### Các dịch vụ AWS sử dụng

| Dịch vụ | Vai trò trong hệ thống |
|---------|------------------------|
| **Amazon RDS (PostgreSQL + PostGIS)** | Cơ sở dữ liệu quan hệ chính, lưu trữ toàn bộ dữ liệu nghiệp vụ; phần mở rộng PostGIS hỗ trợ các câu truy vấn dữ liệu không gian và tính khoảng cách vị trí |
| **Amazon S3** | Lưu trữ tập trung các tệp hình ảnh bất động sản được tải lên từ ứng dụng |
| **Amazon Cognito** | Quản lý đăng ký, đăng nhập và phát hành chuỗi mã xác thực JWT cho mọi yêu cầu tới backend |
| **Amazon SES** | Gửi email giao dịch và thông báo tự động tới người dùng khi có sự thay đổi trạng thái đơn thuê hoặc hợp đồng |
| **Amazon Location Service** | Chuyển đổi địa chỉ văn bản thành tọa độ địa lý (geocoding), hỗ trợ gợi ý địa điểm autocomplete và hiển thị bản đồ tương tác |

#### Thiết kế module backend

Backend NestJS được phân chia thành các module độc lập theo miền nghiệp vụ:

- **Auth module**: Tiếp nhận JWT token từ Amazon Cognito, triển khai `AuthGuard` và `RolesGuard` để xác thực và phân quyền người dùng.
- **Property module**: Xử lý các thao tác CRUD bất động sản, tải ảnh lên Amazon S3, tìm kiếm và lọc dữ liệu đa tiêu chí kết hợp hàm không gian PostGIS.
- **Application module**: Quản lý đơn xin thuê của người thuê và chức năng xét duyệt đơn của chủ nhà.
- **Lease module**: Khởi tạo hợp đồng thuê tự động trong một `prisma.$transaction` khi đơn thuê được chấp thuận, lưu trữ lịch sử thanh toán (Payment).
- **Message module**: Quản lý kết nối WebSocket Gateway, hỗ trợ truyền nhận tin nhắn trò chuyện thời gian thực giữa tenant và manager.
- **Notification module**: Lưu trữ thông báo trong ứng dụng (in-app notification) và gọi dịch vụ Amazon SES gửi email thông báo.
- **Location module**: Giao tiếp với Amazon Location Service để thực hiện geocoding địa chỉ và phục vụ truy vấn vị trí trên bản đồ.
- **Tenant / Manager module**: Quản lý thông tin hồ sơ cá nhân của từng nhóm người dùng, đồng bộ với thông tin Cognito qua `cognitoId`.

---

### 4. Triển khai kỹ thuật

#### Stack công nghệ và lý do lựa chọn

**Backend — NestJS (TypeScript)**

**NestJS** được lựa chọn làm nền tảng phát triển backend nhờ kiến trúc module hóa chặt chẽ, giúp phân tách rõ ràng các miền nghiệp vụ như quản lý bất động sản, đơn xin thuê, hợp đồng và tin nhắn. Cơ chế **Dependency Injection** giúp giảm sự phụ thuộc trực tiếp giữa các tầng ứng dụng, thuận tiện cho việc kiểm thử và bảo trì mã nguồn. **TypeScript** cung cấp cơ chế kiểm soát kiểu dữ liệu mạnh mẽ, kết hợp với **Prisma ORM** đảm bảo tính đồng bộ kiểu dữ liệu từ schema CSDL đến tầng ứng dụng.

**Frontend — Next.js (App Router)**

**Next.js** với kiến trúc **App Router** được sử dụng để xây dựng giao diện người dùng tối ưu. Hệ thống phân chia linh hoạt giữa **Server Components** cho các trang danh sách bất động sản nhằm tối ưu tốc độ tải và **Client Components** cho các thành phần tương tác cao như bản đồ, bộ lọc và khung chat. **Redux Toolkit** kết hợp **RTK Query** được triển khai để quản lý trạng thái đăng nhập tập trung, tự động lưu cache dữ liệu API và đính kèm JWT token vào các request HTTP gửi tới backend.

**Database — PostgreSQL + PostGIS + Prisma ORM**

**PostgreSQL** đáp ứng hoàn hảo mô hình dữ liệu quan hệ của ứng dụng cho thuê nhà ở. Phần mở rộng **PostGIS** cho phép lưu trữ tọa độ dưới dạng dữ liệu không gian (`geography`) và thực hiện các phép tính khoảng cách trực tiếp trong CSDL. **Prisma ORM** giúp quản lý migration an toàn và cung cấp Prisma Client type-safe, đồng thời cho phép thực thi các câu truy vấn SQL thô (`$queryRaw`) khi cần kết hợp các điều kiện lọc thông thường với hàm địa lý của PostGIS.

#### Các vấn đề kỹ thuật đã giải quyết

**Xác thực và phân quyền dựa trên vai trò**

Hệ thống sử dụng JWT token do **Amazon Cognito** phát hành. Tại backend NestJS, `AuthGuard` trích xuất và giải mã JWT token để xác minh tính hợp lệ, trong khi `RolesGuard` kiểm tra vai trò người dùng (TENANT hoặc MANAGER). Hệ thống áp dụng cơ chế **Refresh Token Rotation** nhằm vô hiệu hóa token cũ ngay khi phát hành token mới, hạn chế rủi ro cướp phiên làm việc.

**Giải quyết hiện tượng N+1 Query**

Trong quá trình phát triển, truy vấn lấy danh sách bất động sản kèm theo ảnh và thông tin chủ nhà ban đầu phát sinh bài toán N+1 Query, khiến CSDL phải thực hiện hàng chục câu truy vấn riêng biệt cho một danh sách. Hệ thống đã khắc phục triệt để bằng cách áp dụng tính năng **include** của Prisma để nạp trước (eager loading) toàn bộ dữ liệu liên quan trong một câu truy vấn JOIN duy nhất. Đồng thời, các chỉ mục **(index)** đã được thêm vào các cột thường xuyên lọc như `location`, `price`, `status`, giúp thời gian phản hồi P95 giảm từ ~800ms xuống còn ~120ms.

**Xử lý xung đột đồng thời (Race Condition)**

Khi nhiều yêu cầu xét duyệt đơn thuê hoặc tạo hợp đồng xảy ra đồng thời cho cùng một bất động sản, hệ thống có thể đối mặt với rủi ro ghi đè dữ liệu hoặc tạo trùng hợp đồng. Vấn đề này được giải quyết bằng cách bọc toàn bộ thao tác trong một **`prisma.$transaction`** kết hợp với cơ chế **Pessimistic Locking (`SELECT ... FOR UPDATE`)**. Khi một giao dịch bắt đầu, bản ghi bất động sản liên quan sẽ được khóa cho đến khi giao dịch hoàn tất, đảm bảo tính nhất quán tuyệt đối của dữ liệu.

**Tích hợp geocoding và bản đồ vị trí**

Địa chỉ văn bản nhập vào khi tạo tin đăng bất động sản được gửi tới **Amazon Location Service** để chuyển đổi thành tọa độ địa lý (latitude, longitude). Tọa độ này được lưu vào bảng `Location` có tích hợp cột PostGIS. Khi người dùng tìm kiếm bất động sản theo vị trí trên bản đồ giao diện Next.js, backend thực hiện câu truy vấn không gian `ST_DWithin` để trả về các bất động sản nằm trong bán kính được chọn.

#### Tiến độ 8 tuần phát triển

| Tuần | Giai đoạn | Nội dung chính |
|------|-----------|----------------|
| 1 | Onboarding & Chuẩn bị | Tìm hiểu quy định FCAJ, làm quen với AWS, đăng ký AWS Free Tier, nhận 200 USD AWS Credits, tạo AWS Budget |
| 2 | Phân tích & Tích hợp Cognito | Phân tích yêu cầu hệ thống, thiết kế kiến trúc, refactor monorepo, tích hợp Amazon Cognito, xây dựng `AuthGuard` và `RolesGuard` |
| 3 | Xây dựng CSDL & Module cốt lõi | Định nghĩa Prisma schema (PostgreSQL + PostGIS), phát triển module **tenant**, **manager**, **application** và **lease** |
| 4 | Phát triển Property & Tìm kiếm | Phát triển module **property**, tích hợp upload ảnh **Amazon S3**, xây dựng bộ lọc tìm kiếm đa tiêu chí, hoàn thiện trang cài đặt Profile |
| 5 | Dashboard & Chuyển CSDL RDS | Hoàn thiện Manager Dashboard, xây dựng chức năng chỉnh sửa tin đăng & quản lý ảnh S3, chuyển CSDL sang **Amazon RDS PostgreSQL** |
| 6 | Real-time Chat & Email SES | Phát triển module **message** (trò chuyện thời gian thực qua WebSocket), module **notification** (in-app notification), tích hợp **Amazon SES** gửi email |
| 7 | Tối ưu & Bảo mật hệ thống | Tối ưu N+1 query (Prisma `include`), xử lý Race Condition (Pessimistic Locking `SELECT ... FOR UPDATE`), tích hợp **Amazon Location Service** |
| 8 | Kiểm thử & Demo | Kiểm thử tổng thể (E2E testing), sửa lỗi tồn đọng, hoàn thiện README, Swagger API docs, báo cáo Worklog & Proposal, demo và bàn giao |

---

### 5. Lộ trình & Mốc triển khai

```
Tuần 1    │ ████ Onboarding · AWS Fundamentals · AWS Budget
Tuần 2    │ ████ Thiết kế kiến trúc · Monorepo · Amazon Cognito · Guards
Tuần 3    │ ████ Prisma Schema · PostgreSQL · Module Tenant/Manager/Application/Lease
Tuần 4    │ ████ Module Property · Upload ảnh Amazon S3 · Filter · Profile Pages
Tuần 5    │ ████ Manager Dashboard · Chỉnh sửa tin đăng · Migrate CSDL sang Amazon RDS
Tuần 6    │ ████ WebSocket Real-time Chat · Notification · Amazon SES Email
Tuần 7    │ ████ Tối ưu N+1 Query · Race Condition Locking · Amazon Location Service
Tuần 8    │ ████ E2E Testing · Tài liệu README & Swagger · Proposal & Demo bàn giao
```

**Các mốc quan trọng:**

- **Tuần 2**: Hoàn thành onboarding, nhận 200 USD AWS Credits, thiết lập AWS Budget và hoàn thiện luồng xác thực Cognito.
- **Tuần 4**: Hoàn thành luồng đăng tin bất động sản với tải ảnh Amazon S3 và bộ lọc tìm kiếm đa tiêu chí.
- **Tuần 5**: Chuyển đổi thành công cơ sở dữ liệu lên Amazon RDS và demo nội bộ Manager Dashboard với mentor.
- **Tuần 6**: Triển khai thành công tính năng nhắn tin thời gian thực qua WebSocket và gửi email tự động qua Amazon SES.
- **Tuần 7**: Khắc phục dứt điểm N+1 Query, ngăn ngừa Race Condition bằng Pessimistic Locking và hoàn thiện bản đồ vị trí.
- **Tuần 8**: Nghiệm thu kiểm thử tổng thể, hoàn thiện tài liệu kỹ thuật, demo sản phẩm thành công và bàn giao hệ thống.

---

### 6. Ước tính ngân sách

Toàn bộ chi phí vận hành hệ thống trong suốt 8 tuần thực tập được tối ưu nhằm nằm trọn trong hạn mức **AWS Free Tier** và số tiền **200 USD AWS Credits** nhận được từ chương trình hỗ trợ.

#### Chi phí hạ tầng AWS (ước tính môi trường development)

| Dịch vụ | Cấu hình sử dụng | Chi phí ước tính |
|---------|------------------|------------------|
| Amazon RDS (PostgreSQL) | db.t3.micro, 20 GB SSD, Single-AZ | ~15 USD/tháng |
| Amazon S3 | ~5 GB lưu trữ hình ảnh, ~10,000 lượt yêu cầu/tháng | ~0.15 USD/tháng |
| Amazon SES | ~500 email thông báo/tháng (môi trường Sandbox) | 0 USD (Free Tier) |
| Amazon Cognito | <50,000 người dùng hoạt động hàng tháng (MAU) | 0 USD (Free Tier) |
| Amazon Location Service | Yêu cầu geocoding địa chỉ và nạp bản đồ trong hạn mức trải nghiệm | ~0.50 USD/tháng |
| **Tổng chi phí ước tính** | | **~15.65 USD/tháng** |

> Tổng chi phí thực tế cho 8 tuần phát triển và kiểm thử ước tính khoảng 32 USD, được chi trả hoàn toàn bằng gói 200 USD AWS Credits.

#### Biện pháp kiểm soát chi phí

- Cấu hình **AWS Budget** gửi cảnh báo email tự động khi chi phí vượt các mốc 50 USD và 100 USD.
- Sử dụng mô hình **RDS Single-AZ** cho môi trường phát triển để tiết kiệm chi phí so với Multi-AZ.
- Giới hạn số lượng request gọi tới Amazon Location Service phía client bằng kỹ thuật debounce khi người dùng gõ tìm kiếm địa chỉ.
- Tắt instance Amazon RDS ngoài giờ làm việc khi không có nhu cầu thao tác dữ liệu.

---

### 7. Đánh giá rủi ro

#### Ma trận rủi ro

| Rủi ro phát sinh | Mức độ | Xác suất | Giải pháp xử lý & giảm thiểu |
|------------------|--------|----------|------------------------------|
| Vượt chi phí AWS Credits | Trung bình | Thấp | Thiết lập AWS Budget alert; tắt các tài nguyên RDS ngoài giờ làm việc |
| Race Condition khi duyệt đơn thuê đồng thời | Cao | Trung bình | Áp dụng `prisma.$transaction` kết hợp Pessimistic Locking (`SELECT ... FOR UPDATE`) |
| Suy giảm hiệu năng do N+1 Query | Trung bình | Trung bình | Nạp trước dữ liệu bằng Prisma `include` và bổ sung database index cho các cột tìm kiếm |
| Thất lạc kết nối WebSocket chat thời gian thực | Trung bình | Thấp | Xây dựng cơ chế tự động kết nối lại (auto-reconnect) ở client và lưu lịch sử tin nhắn trong CSDL |
| Tải file ảnh dung lượng quá lớn lên Amazon S3 | Thấp | Thấp | Validate dung lượng và định dạng file ảnh ở tầng Controller trước khi gọi AWS SDK upload |

#### Kế hoạch dự phòng

- **Cơ sở dữ liệu Amazon RDS**: Khôi phục dữ liệu từ bản sao lưu tự động (Automated Snapshot) được cấu hình hàng ngày trên Amazon RDS nếu xảy ra sự cố hỏng dữ liệu.
- **Tải ảnh Amazon S3**: Bổ sung cơ chế thử lại (retry logic) ở backend khi kết nối tới S3 bucket bị ngắt đoạn tạm thời.
- **Gửi email Amazon SES**: Lưu lịch sử thông báo vào bảng `Notification` trong CSDL; nếu Amazon SES bị gián đoạn, người dùng vẫn có thể xem thông báo trực tiếp trên giao diện ứng dụng.

---

### 8. Kết quả kỳ vọng

#### Kết quả kỹ thuật đạt được

Sau 8 tuần triển khai, dự án đạt được các kết quả kỹ thuật cụ thể:

- **Vận hành hoàn chỉnh luồng nghiệp vụ**: Người thuê tìm kiếm bất động sản theo vị trí bản đồ, nộp đơn thuê, nhắn tin thời gian thực; Chủ nhà quản lý tin đăng, chỉnh sửa hình ảnh, xét duyệt đơn và tự động khởi tạo hợp đồng thuê.
- **Bảo mật & Phân quyền chuẩn hóa**: 100% các API endpoint được bảo vệ bằng `AuthGuard` và `RolesGuard`, triển khai Refresh Token Rotation và validate dữ liệu chặt chẽ.
- **Hiệu năng tối ưu**: Xử lý triệt để bài toán N+1 Query giúp thời gian phản hồi API danh sách đạt ~120ms; kiểm soát hoàn toàn xung đột đồng thời bằng Pessimistic Locking.
- **Tài liệu kỹ thuật đầy đủ**: Xuất bản tệp `README.md` hướng dẫn triển khai, tài liệu **Swagger API documentation** cho hơn 40 endpoints và sơ đồ kiến trúc hệ thống chuẩn hóa.

#### Giá trị học tập và phát triển kỹ năng

Quá trình thực hiện dự án giúp củng cố toàn diện các kỹ năng kỹ thuật thực tế:

- Tư duy phân tích nghiệp vụ và thiết kế kiến trúc phần mềm theo mô hình monorepo chuyên nghiệp.
- Kỹ năng tích hợp và vận hành các dịch vụ đám mây AWS (Cognito, S3, RDS, SES, Location Service) trong ứng dụng sản phẩm thực tế.
- Thành thạo kỹ thuật tối ưu hóa cơ sở dữ liệu quan hệ, xử lý truy vấn dữ liệu không gian PostGIS và kiểm soát giao dịch đồng thời (concurrency management).
- Kỹ năng viết tài liệu kỹ thuật, báo cáo công việc và trình bày sản phẩm phần mềm theo chuẩn doanh nghiệp.

#### Định hướng phát triển tiếp theo

Hệ thống được thiết kế theo kiến trúc module hóa linh hoạt, tạo tiền đề thuận lợi cho các hướng mở rộng trong tương lai:

- Tích hợp cổng thanh toán trực tuyến (VNPay, ZaloPay, Stripe) để xử lý giao dịch thanh toán tiền thuê nhà hàng tháng.
- Xây dựng ứng dụng di động (Mobile App) dựa trên React Native để tăng trải nghiệm tiện lợi cho người dùng.
- Đóng gói ứng dụng bằng Docker và triển khai lên hạ tầng Amazon ECS / EKS để hỗ trợ khả năng tự động mở rộng (auto-scaling) khi lượng truy cập tăng cao.