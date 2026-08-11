---
title: "Quản lý lưu trữ & dịch vụ mở rộng"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Tổng quan

Phần này bao gồm ba dịch vụ AWS có quản lý giúp mở rộng chức năng cốt lõi của ứng dụng:

- **Amazon S3** – Lưu trữ hình ảnh bất động sản an toàn qua presigned URL để tải trực tiếp từ trình duyệt lên S3.
- **Amazon Location Service** – Chuyển đổi địa chỉ thành tọa độ và hiển thị bản đồ tương tác.
- **Amazon SES** – Gửi email thông báo tự động khi trạng thái đơn thuê thay đổi.

#### Các bước thực hiện

1. [Cấu hình Amazon S3 bucket & presigned upload URL](5.5.1-s3-presigned/)
2. [Định vị & bản đồ với Amazon Location Service](5.5.2-location-service/)
3. [Gửi email thông báo với Amazon SES](5.5.3-ses-email/)
