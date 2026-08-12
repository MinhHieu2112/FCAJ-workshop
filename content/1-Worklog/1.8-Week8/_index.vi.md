---
title: "Nhật ký tuần 8"
date: 2026-08-10
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

* Kiểm thử tổng thể ứng dụng (end-to-end testing) trên toàn bộ luồng nghiệp vụ của người thuê (tenant) và chủ nhà (manager).
* Rà soát và sửa dứt điểm tất cả các lỗi giao diện và API response còn tồn đọng.
* Hoàn thiện đầy đủ tài liệu kỹ thuật (README.md, Swagger API documentation, sơ đồ kiến trúc hệ thống).
* Chuẩn hóa và cập nhật tài liệu **Solution Proposal** cũng như báo cáo thực tập (Worklog 8 tuần).
* Chuẩn bị kịch bản demo sản phẩm, tổng kết quá trình thực tập và bàn giao hệ thống.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Kiểm thử tổng thể hệ thống (End-to-End Testing): <br>&emsp; + Kiểm thử luồng Tenant: đăng ký/đăng nhập Cognito → tìm kiếm bất động sản trên bản đồ Amazon Location Service → nộp đơn xin thuê → nhắn tin thời gian thực qua WebSocket <br>&emsp; + Kiểm thử luồng Manager: đăng tin → upload ảnh S3 → nhận email SES → xét duyệt đơn thuê → tự động khởi tạo hợp đồng trên Amazon RDS | 10/08/2026 | |
| 3 | - Rà soát và sửa lỗi tồn đọng (Bug Fixing): <br>&emsp; + Sửa các lỗi vỡ khung giao diện responsive trên màn hình điện thoại di động và máy tính bảng <br>&emsp; + Bổ sung exception filter xử lý các lỗi truy vấn CSDL và trả về response chuẩn định dạng <br>&emsp; + Rà soát kiểm tra tính toàn vẹn dữ liệu khi hủy đơn thuê hoặc kết thúc hợp đồng | 11/08/2026 | |
| 4 | - Hoàn thiện tài liệu kỹ thuật hệ thống: <br>&emsp; + Viết **README.md** chi tiết: cấu hình môi trường (.env), hướng dẫn cài đặt monorepo (`pnpm install`), chạy development và build production <br>&emsp; + Xuất bản và chuẩn hóa tài liệu **Swagger API documentation** cho hơn 40 API endpoints <br>&emsp; + Cập nhật sơ đồ kiến trúc hệ thống tổng thể (system architecture diagram) | 12/08/2026 | |
| 5 | - Cập nhật Solution Proposal và báo cáo thực tập: <br>&emsp; + Rà soát và chỉnh sửa tài liệu **Solution Proposal** (`content/2-Proposal/`): đảm bảo chuẩn 8 mục, phân tích sâu về kiến trúc và các dịch vụ AWS <br>&emsp; + Đồng bộ nội dung **Worklog 8 tuần** (`content/1-Worklog/`): kiểm tra tính nhất quán giữa các tuần, loại bỏ lỗi viết hoa tùy tiện và văn phong AI <br>&emsp; + Chuẩn hóa quy chuẩn chính tả tiếng Việt báo cáo doanh nghiệp | 13/08/2026 | |
| 6-7 | - Chuẩn bị demo và bàn giao hệ thống: <br>&emsp; + Chuẩn bị kịch bản demo và slide tổng kết quá trình thực tập 8 tuần <br>&emsp; + Demo sản phẩm hoàn chỉnh trước mentor và hội đồng đánh giá thực tập <br>&emsp; + Lắng nghe nhận xét, ghi nhận đánh giá kết quả thực tập từ doanh nghiệp <br>&emsp; + Bàn giao source code, tài liệu kỹ thuật và tài khoản quản trị hệ thống | 14/08/2026 - 15/08/2026 | |

---

### Kết quả đạt được tuần 8:

* Hoàn thành **kiểm thử tổng thể (E2E testing)**: tất cả các luồng nghiệp vụ chính giữa tenant và manager đều vận hành ổn định, không còn lỗi nghiêm trọng.

* Khắc phục 100% các **lỗi giao diện và xử lý ngoại lệ API còn tồn đọng**, đảm bảo ứng dụng hiển thị mượt mà trên mọi thiết bị.

* Hoàn thành bộ **tài liệu kỹ thuật đầy đủ**: README.md hướng dẫn triển khai, tài liệu Swagger API documentation và sơ đồ kiến trúc hệ thống chuẩn hóa.

* Hoàn thiện tài liệu **Solution Proposal** và **Worklog 8 tuần** với văn phong báo cáo doanh nghiệp chuyên nghiệp, chính xác thuật ngữ và thống nhất trên toàn bộ hệ thống.

* **Báo cáo và demo thành công** sản phẩm Real Estate Rental Management System với mentor, nhận phản hồi tích cực về kiến trúc, tính hoàn thiện và khả năng ứng dụng thực tế.

* **Bàn giao thành công** source code và tài liệu dự án cho đơn vị thực tập First Cloud AI Journey.

---

### Kiến thức / Kinh nghiệm học được:

* **Tổng kết kỹ thuật**: Qua 8 tuần thực tập, đã làm chủ quy trình xây dựng một hệ thống web full-stack hoàn chỉnh từ khâu phân tích nghiệp vụ, thiết kế kiến trúc, chọn tech stack (NestJS, Next.js, PostgreSQL/PostGIS, Prisma) đến việc tích hợp các dịch vụ AWS cốt lõi (Cognito, S3, SES, RDS, Location Service).
* **Kỹ năng thực tế tích lũy**:
  * Tư duy thiết kế kiến trúc đám mây và tối ưu chi phí hạ tầng.
  * Kỹ năng xử lý tối ưu CSDL (giải quyết N+1 Query, tạo index, spatial query PostGIS).
  * Kỹ năng xử lý đồng thời (Race Condition, Transaction, Pessimistic Locking).
  * Tư duy bảo mật ứng dụng web (RBAC Guards, Refresh Token Rotation, Rate Limiting).
  * Kỹ năng viết tài liệu kỹ thuật và báo cáo doanh nghiệp chuyên nghiệp.
* **Định hướng phát triển**: Tiếp tục mở rộng kiến thức về Cloud-Native Architecture, Serverless, Containerization (Docker, Amazon ECS/EKS) và Infrastructure as Code (Terraform / AWS CDK).
