---
title: "Nhật ký tuần 4"
date: 2026-07-13
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Phát triển module **property**: xây dựng API CRUD bất động sản và tích hợp Amazon S3 để upload/lưu trữ hình ảnh.
* Xây dựng chức năng tìm kiếm và bộ lọc bất động sản đa tiêu chí (giá, diện tích, số phòng, tiện nghi, loại hình).
* Hoàn thiện trang cài đặt tài khoản và hồ sơ cá nhân cho người thuê (tenant) và chủ nhà (manager) ở giao diện Next.js.
* Tích hợp giao diện tìm kiếm và danh sách bất động sản ở frontend với backend NestJS API.
* Kiểm thử và tối ưu toàn bộ các chức năng đã triển khai trong tuần.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Phát triển backend module **property**: <br>&emsp; + Định nghĩa Prisma model Property, Location, PropertyImage và tạo DTO cho CRUD <br>&emsp; + Tích hợp AWS SDK v3 cho **Amazon S3**: xây dựng service upload nhiều ảnh bất động sản, lưu URL ảnh <br>&emsp; + Xây dựng API tạo tin đăng, cập nhật thông tin và xóa bất động sản | 13/07/2026 | <https://docs.aws.amazon.com/s3/> |
| 3 | - Xây dựng chức năng tìm kiếm và bộ lọc bất động sản: <br>&emsp; + Viết logic truy vấn Prisma linh hoạt hỗ trợ lọc theo khoảng giá, diện tích, số phòng ngủ, phòng tắm, loại hình <br>&emsp; + Xây dựng bộ lọc tiện nghi (amenities: wifi, điều hòa, chỗ để xe, nội thất) <br>&emsp; + Xây dựng phân trang (pagination) và sắp xếp (sorting) cho API danh sách | 14/07/2026 | <https://www.prisma.io/docs/> |
| 4 | - Hoàn thiện trang cài đặt ở frontend Next.js: <br>&emsp; + Xây dựng trang cài đặt tài khoản (Settings) và thông tin hồ sơ (Profile) cho tenant và manager <br>&emsp; + Xây dựng form chỉnh sửa thông tin cá nhân, cập nhật ảnh đại diện <br>&emsp; + Quản lý auth state ở client bằng Redux Toolkit | 15/07/2026 | <https://nextjs.org/docs> |
| 5 | - Tích hợp giao diện tìm kiếm và xem tin đăng: <br>&emsp; + Xây dựng trang danh sách bất động sản (Property List) với thanh tìm kiếm và sidebar bộ lọc <br>&emsp; + Xây dựng trang chi tiết bất động sản (Property Detail) hiển thị gallery ảnh S3, tiện nghi, mô tả <br>&emsp; + Kết nối API bằng RTK Query, đính kèm JWT token trong mọi request | 16/07/2026 | <https://redux-toolkit.js.org/> |
| 6 | - Kiểm thử và tối ưu các chức năng đã triển khai: <br>&emsp; + Kiểm thử luồng đăng tin bất động sản kèm upload ảnh lên Amazon S3 thành công <br>&emsp; + Test độ chính xác của bộ lọc khi tìm kiếm kết hợp nhiều điều kiện <br>&emsp; + Kiểm tra hiển thị responsive trên màn hình mobile và desktop, fix bug giao diện | 17/07/2026 | |

---

### Kết quả đạt được tuần 4:

* Phát triển hoàn thiện **Property module**: cung cấp đầy đủ API CRUD bất động sản, tự động quản lý danh mục hình ảnh.

* Tích hợp thành công **Amazon S3**: hỗ trợ upload nhiều hình ảnh bất động sản cùng lúc và hiển thị ảnh mượt mà qua S3 bucket URL.

* Xây dựng thành công chức năng tìm kiếm và bộ lọc đa tiêu chí: cho phép lọc chính xác theo giá thuê, diện tích, số phòng, tiện nghi và loại hình bất động sản.

* Hoàn thiện trang cài đặt **(Settings/Profile)** ở giao diện Next.js cho cả hai nhóm người dùng tenant và manager.

* Tích hợp hoàn tất giữa Next.js frontend và NestJS backend thông qua RTK Query, kiểm thử toàn bộ luồng đăng tin và tìm kiếm hoạt động ổn định.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu cách tích hợp AWS SDK v3 trong NestJS để quản lý file trên Amazon S3 (PutObjectCommand, DeleteObjectCommand).
* Nắm vững kỹ thuật xây dựng động câu truy vấn Prisma (dynamic query building) khi xử lý bộ lọc tìm kiếm có nhiều điều kiện không bắt buộc.
* Kinh nghiệm tổ chức giao diện Next.js App Router: tách biệt Server Components cho trang danh sách (SEO) và Client Components cho form bộ lọc tương tác.
* Nắm vững cách quản lý trạng thái đăng nhập và cache dữ liệu API ở frontend bằng Redux Toolkit và RTK Query.
