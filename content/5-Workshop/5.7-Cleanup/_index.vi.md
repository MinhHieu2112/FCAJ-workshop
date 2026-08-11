---
title: "Dọn dẹp tài nguyên"
date: 2026-08-06
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Xin chúc mừng bạn đã hoàn thành bài thực hành **Triển khai hệ thống quản lý bất động sản trên AWS**!

Qua bài lab này, bạn đã xây dựng và triển khai thành công một hệ thống đám mây hoàn chỉnh trên AWS:
- **Mạng & tính toán**: Thiết lập VPC hai tầng, public/private subnets, security groups và máy chủ EC2 chạy Docker runtime.
- **Cơ sở dữ liệu & định tuyến**: Triển khai Amazon RDS PostgreSQL hỗ trợ PostGIS, cấu hình Application Load Balancer (ALB), AWS WAF và DuckDNS kèm SSL/TLS.
- **Xác thực & dịch vụ**: Tích hợp Amazon Cognito User Pool, Amazon S3 Presigned URLs, Amazon Location Service và Amazon SES.
- **CI/CD & giám sát**: Cấu hình tự động hóa GitHub Actions và hệ thống log, cảnh báo chỉ số CloudWatch.

---

#### Dọn dẹp tài nguyên AWS

Để tránh phát sinh chi phí ngoài ý muốn trên tài khoản AWS, hãy thực hiện xóa các tài nguyên đã tạo theo thứ tự sau:

#### 1. Xóa máy chủ EC2, target groups & ALB

1. Truy cập [Bảng điều khiển EC2](https://console.aws.amazon.com/ec2/home).
2. Chọn **Instances** → chọn `real-estate-backend-server` → **Instance state** → **Terminate instance**.
3. Chọn **Load Balancers** → chọn `real-estate-alb` → **Actions** → **Delete load balancer**.
4. Chọn **Target Groups** → chọn `real-estate-backend-tg` → **Actions** → **Delete**.

---

#### 2. Xóa AWS WAF Web ACL

1. Truy cập [Bảng điều khiển AWS WAF](https://console.aws.amazon.com/wafv2/homev2).
2. Chọn **Web ACLs** → chọn `real-estate-waf` → nhấn **Delete**.

---

#### 3. Xóa Amazon RDS instance & DB subnet group

1. Truy cập [Bảng điều khiển Amazon RDS](https://console.aws.amazon.com/rds/home).
2. Chọn **Databases** → chọn `real-estate-rental-db` → **Actions** → **Delete**.
3. Bỏ tích chọn *Create final snapshot*, tích chọn xác nhận và nhập từ khóa `delete me` để xác nhận xóa.
4. Sau khi xóa xong, chọn **Subnet groups** → chọn `real-estate-rds-subnet-group` → **Delete**.

---

#### 4. Xóa Amazon S3 media bucket

1. Truy cập [Bảng điều khiển Amazon S3](https://s3.console.aws.amazon.com/s3/home).
2. Chọn bucket `real-estate-rental-media-dev`.
3. Nhấn **Empty** và xác nhận xóa toàn bộ ảnh bên trong bucket.
4. Sau khi bucket rỗng, nhấn **Delete** và nhập lại tên bucket để xóa vĩnh viễn.

![Delete S3 Bucket](/images/5-Workshop/5.6-Cleanup/5.6.1-bucket.png)

---

#### 5. Xóa Amazon Cognito User Pool

1. Truy cập [Bảng điều khiển Amazon Cognito](https://console.aws.amazon.com/cognito/v2/home).
2. Chọn `real-estate-rental-user-pool`.
3. Nhấn **Delete user pool** và nhập từ khóa `delete` để xác nhận xóa.

![Delete Cognito User Pool](/images/5-Workshop/5.6-Cleanup/5.6.2-aws-cognito.png)

---

#### 6. Xóa Amazon Location Service API Key

1. Truy cập [Bảng điều khiển Amazon Location Service](https://console.aws.amazon.com/location/home).
2. Điều hướng và click vào mục **API keys** bên tab trái.
3. Nhấn **Deactivate** để vô hiệu hóa API Key, sau đó nhấn **Delete** để xóa vĩnh viễn.

![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location.png)
![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete.png)
![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete-1.png)

---

#### 7. Xóa VPC & security groups

1. Truy cập [Bảng điều khiển Amazon VPC](https://console.aws.amazon.com/vpc/home).
2. Chọn **Your VPCs** → chọn `real-estate-vpc` → **Actions** → **Delete VPC**.
3. Xác nhận xóa để tự động xóa các subnets, route tables, internet gateways và security groups liên quan.

---

#### 8. Xóa IAM roles & policies

1. Truy cập [Bảng điều khiển IAM](https://console.aws.amazon.com/iam/home#/roles).
2. Chọn `RealEstateEC2Role` → **Delete**.

![Delete IAM User](/images/5-Workshop/5.6-Cleanup/5.6.4-aws-iam.png)
