---
title: "Dọn dẹp tài nguyên"
date: 2026-08-06
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Xin chúc mừng bạn đã hoàn thành bài thực hành **Xây dựng Hệ thống quản lý bất động sản cho thuê trên AWS**!

Qua bài lab này, bạn đã tích hợp thành công các dịch vụ đám mây AWS tiêu chuẩn doanh nghiệp vào ứng dụng monorepo NestJS và Next.js:
- **Amazon Cognito User Pool**: Xây dựng luồng xác thực đăng nhập JWT và phân quyền vai trò (`TENANT` / `MANAGER`).
- **Amazon S3**: Tải ảnh trực tiếp an toàn thông qua Presigned URL.
- **Amazon Location Service**: Chuyển đổi địa chỉ văn bản thành tọa độ địa lý và lưu trữ PostGIS.

---

#### Dọn dẹp tài nguyên AWS

Để tránh phát sinh chi phí ngoài ý muốn trên tài khoản AWS, hãy thực hiện xóa các tài nguyên đã tạo theo thứ tự sau:

#### 1. Xóa Amazon S3 Media Bucket

1. Truy cập [Bảng điều khiển Amazon S3](https://s3.console.aws.amazon.com/s3/home).
2. Chọn bucket `real-estate-rental-media-dev`.
3. Nhấn **Empty** và xác nhận xóa toàn bộ ảnh bên trong bucket.
4. Sau khi bucket rỗng, nhấn **Delete** và nhập lại tên bucket để xóa vĩnh viễn.

![Delete S3 Bucket](/images/5-Workshop/5.6-Cleanup/5.6.1-bucket.png)

---

#### 2. Xóa Amazon Cognito User Pool

1. Truy cập [Bảng điều khiển Amazon Cognito](https://console.aws.amazon.com/cognito/v2/home).
2. Chọn `real-estate-rental-user-pool`.
3. Nhấn **Delete user pool** và nhập từ khóa `delete` để xác nhận xóa.

![Delete Cognito User Pool](/images/5-Workshop/5.6-Cleanup/5.6.2-aws-cognito.png)

---

#### 3. Xóa Amazon Location Service

1. Truy cập [Bảng điều khiển Amazon Location Service](https://console.aws.amazon.com/location/home).
2. Điều hướng và click vào mục **API key** bên tab trái.

![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location.png)

3. Nhấn **Deactivate** để vô hiệu hóa API Key, sau đó nhấn **Delete** để xóa vĩnh viễn.

![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete.png)
![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete(1).png)
---

#### 4. Xóa IAM User

1. Truy cập [Bảng điều khiển IAM](https://console.aws.amazon.com/iam/home#/users).
2. Nhấn **IAM users** ở tab trái
3. Chọn user cần xóa 
4. Nhấn **Delete** và nhập từ khóa `delete` để xác nhận xóa.

![Delete IAM User](/images/5-Workshop/5.6-Cleanup/5.6.4-aws-iam.png)
