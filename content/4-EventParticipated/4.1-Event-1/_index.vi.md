---
title: "Event 1"
date: 2026-06-27
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# FCAJ Community Day - June 2026

## Tổng Quan Sự Kiện
* **Tên sự kiện:** FCAJ Community Day - June 2026
* **Đơn vị tổ chức:** AWS Study Group / FCAJ Community
* **Chủ đề chính:** Xu hướng nghề nghiệp Cloud & AI Engineering, ứng dụng FinOps & Security bằng AI và kiến trúc triển khai Amazon Q Business / MCP Server thực tế.

---

## 1. Xu Hướng Nghề Nghiệp: Kỷ Nguyên Agentic AI & Lập Trình Viên

### Thực trạng & thách thức
* **Tốc độ tự động hóa:** Các AI agent và công cụ AI coding hiện nay có tốc độ triển khai và viết code nhanh chóng, dẫn đến việc doanh nghiệp có xu hướng nâng cao tiêu chuẩn tuyển dụng hoặc ưu tiên các vị trí Senior am hiểu sâu về công cụ AI.
* **Độ phức tạp hạ tầng:** Khi doanh nghiệp mở rộng quy mô hệ thống Cloud, độ phức tạp của hạ tầng tăng cao. AI đơn thuần chưa thể hiểu trọn vẹn toàn bộ bối cảnh (context) từ Source Code, Cloud Infrastructure đến Business Logic phức tạp của từng doanh nghiệp.

### Bài học thực tiễn & định hướng phát triển
* **AI không thay thế con người hoàn toàn:** Các vị trí như **Cloud Engineer, DevOps, Solution Architect, Reliability Engineering** vẫn cực kỳ quan trọng và không thể thay thế hoàn toàn. Nguyên nhân là các hệ thống Cloud đòi hỏi khả năng phản ứng incident nhanh chóng, ra quyết định chính xác và xử lý các bài toán bối cảnh doanh nghiệp.
* **Tư duy cốt lõi:** Thay vì lo lắng bị thay thế, kỹ sư cần học cách phối hợp với AIAgent để tự động hóa các công việc lặp đi lặp lại và tập trung vào thiết kế kiến trúc, tối ưu vận hành.

---

## 2. Giải Pháp AI Trong FinOps Và Cloud Security

### Tự động hóa FinOps (Cost Optimization)
* **Thực trạng:** Người làm tài chính/kế toán truyền thống thường thiếu kiến thức kỹ thuật sâu về dịch vụ Cloud, trong khi kỹ sư hệ thống lại không chuyên về mô hình chi phí tài chính.
* **Giải pháp:** Sử dụng AI am hiểu cấu trúc AWS và quy tắc tài chính để phân tích chi phí, phát hiện bất thường và đề xuất phương án tối ưu hóa FinOps tự động.

### Nâng cao an toàn thông tin (Cloud Security)
* **Thực trạng:** An toàn thông tin thường bị xem nhẹ hoặc phát hiện chậm trễ, dẫn đến lỗ hổng bảo mật kéo dài qua nhiều tháng/năm.
* **Giải pháp:** Triển khai các AI Agent chuyên biệt có khả năng:
  * Đánh giá bảo mật tự động (Security Assessment).
  * Kiểm tra lỗ hổng cấu trúc hạ tầng dưới dạng code (IaC Assessment).
  * Hỗ trợ Pen-testing tự động và phân tích Log hệ thống liên tục.

---

## 3. Bài toán hóa đơn & chi phí triển khai mạng riêng (Amazon Q / MCP)

### Mô hình kiến trúc & bảng chi phí ước tính

Khi triển khai các giải pháp AI như Amazon Q Business hay Model Context Protocol (MCP) Server trong mạng nội bộ (Private VPC) để đảm bảo an toàn dữ liệu, doanh nghiệp cần lưu ý các khoản chi phí cố định hạ tầng:

| Thành phần hạ tầng | Chi phí ước tính hàng tháng (USD) | Ghi chú / Mục đích |
| :--- | :--- | :--- |
| **Route 53 Resolver** | `~$180` | Xử lý DNS Query cho Private Endpoints |
| **Application Load Balancer (ALB)** | `~$32` | Điều hướng truy cập vào dịch vụ nội bộ |
| **EC2 Instance / Compute** | Tùy thuộc kích thước (`t3/m5`) | Hosting MCP Server hoặc các dịch vụ AI |
| **AWS Secrets Manager** | Chi phí theo số lượng Secret | Lưu trữ an toàn API Keys / Credentials (ví dụ: Zalo API, DB credentials) |
| **Data Transfer In/Out** | Chi phí biến đổi theo GB | Thường chiếm tỷ trọng nhỏ so với chi phí cố định của hạ tầng |

> **Tổng chi phí hạ tầng cố định ban đầu:** Ước tính từ **$250 – $350 USD/tháng** (chưa tính chi phí truy xuất dữ liệu thực tế).

### Bài học thực tiễn khi ước tính chi phí
* **Cạm bẫy chi phí ẩn:** Nhiều dự án AI Private chỉ tính tiền chạy Model/LLM mà quên mất chi phí hạ tầng mạng riêng (VPC Endpoints, Route 53 Resolver, ALB, Secrets Manager).
* **Phương pháp tính toán:** Cần căn cứ vào lượng người dùng thực tế (ví dụ: 500 nhân viên) và dung lượng dữ liệu truy xuất trung bình mỗi tháng (GB/tháng) để đưa ra bài toán kinh tế chính xác trước khi bấm nút triển khai trên AWS.

## 4. Hình ảnh tham gia sự kiện
![Overview](/images/4-EventParticipated/Event_1/Meeting.jpeg)
***Hình 1: Hình ảnh tại sự kiện FCAJ Community Day - June 2026.***
