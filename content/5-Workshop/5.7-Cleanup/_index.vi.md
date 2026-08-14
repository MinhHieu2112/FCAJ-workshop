---
title: "Dọn dẹp tài nguyên"
date: 2026-08-06
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Chúc mừng bạn đã hoàn thành bài workshop **Hệ thống quản lý bất động sản cho thuê trên AWS**!

Trong bài lab này, bạn đã triển khai thành công kiến trúc thực tế:
- **Frontend Vercel (HTTPS)** – Kết nối an toàn đến backend qua tên miền DuckDNS.
- **Mạng & tính toán**: Tạo VPC 2 tầng, subnets public/private, Elastic IP, Security Groups mở port 80/443 và máy chủ EC2 chạy Docker runtime cùng Caddy HTTPS Reverse Proxy.
- **Cơ sở dữ liệu & bảo mật**: Khởi tạo Amazon RDS PostgreSQL (PostGIS) trong private subnet và quản lý bí mật qua AWS Secrets Manager.
- **Dịch vụ tích hợp**: Tích hợp Amazon Cognito User Pool, Amazon S3 Presigned URL, Amazon Location Service và Amazon SES.
- **CI/CD & giám sát**: Thiết lập tự động hóa GitHub Actions và giám sát log/metrics với CloudWatch.

---

#### Dọn dẹp tài nguyên AWS

Để tránh phát sinh chi phí ngoài ý muốn trên tài khoản AWS của bạn, hãy xóa các tài nguyên đã tạo theo thứ tự sau:

#### 1. Hủy Elastic IP & Terminate EC2 Instance

1. Truy cập [bảng điều khiển Amazon EC2](https://console.aws.amazon.com/ec2/home).
2. Chọn **Instances** -> chọn máy chủ backend -> **Instance state** -> **Terminate instance**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.5-aws-ec2.png)
3. Chọn **Elastic IPs** -> chọn Elastic IP đã gán cho EC2 -> **Actions** -> **Disassociate Elastic IP address**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.6-aws-ip-elastic.png)
4. Tiếp tục chọn **Actions** -> **Release Elastic IP addresses** để trả IP về AWS pool.

---

#### 2. Xóa Amazon RDS instance & DB subnet group

1. Truy cập [bảng điều khiển Amazon RDS](https://console.aws.amazon.com/rds/home).
2. Chọn **Databases** -> chọn `real-estate-rental-db` -> **Actions** -> **Delete**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.7-aws-rds-1.png)
3. Bỏ chọn *Create final snapshot*, tích chọn ô xác nhận và nhập `delete me` để xác nhận.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.7-aws-rds-2.png)
4. Chọn **Subnet groups** -> chọn `real-estate-rds-subnet-group` -> **Delete**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.7-aws-subnet-group.png)
---

#### 3. Xóa AWS Secrets Manager secret
1. Truy cập [bảng điều khiển AWS Secrets Manager](https://console.aws.amazon.com/secretsmanager/).
2. Chọn `rentiful/production/env` -> **Actions** -> **Delete secret**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.8-aws-secret-manager.png)
3. Chọn số ngày chờ xóa hoặc xác nhận xóa ngay.
---

#### 4. Xóa Amazon S3 media bucket

1. Truy cập [bảng điều khiển Amazon S3](https://s3.console.aws.amazon.com/s3/home).
2. Chọn bucket `real-estate-rental-media-dev`.
3. Click **Empty** và xác nhận xóa toàn bộ file bên trong.
4. Nhấn **Delete** và nhập tên bucket để xóa vĩnh viễn.
![Delete S3 Bucket](/images/5-Workshop/5.6-Cleanup/5.6.1-bucket.png)

---

#### 5. Xóa Amazon Cognito User Pool

1. Truy cập [bảng điều khiển Amazon Cognito](https://console.aws.amazon.com/cognito/v2/home).
2. Chọn `real-estate-rental-user-pool`.
3. Click **Delete user pool** và nhập `delete` để xác nhận.

![Delete Cognito User Pool](/images/5-Workshop/5.6-Cleanup/5.6.2-aws-cognito.png)

---

#### 6. Xóa Amazon Location Service API Key

1. Truy cập [bảng điều khiển Amazon Location Service](https://console.aws.amazon.com/location/home).
2. Chọn **API keys** ở menu bên trái.
3. Chọn **Deactivate** để vô hiệu hóa API Key, sau đó chọn **Delete** để xóa vĩnh viễn.

![Delete Location Key 1](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location.png)
![Delete Location Key 2](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete.png)
![Delete Location Key 3](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete-1.png)

---

#### 7. Xóa VPC & Security Groups

1. Truy cập [bảng điều khiển Amazon VPC](https://console.aws.amazon.com/vpc/home).
2. Chọn **Your VPCs** -> chọn `my-app-vpc` -> **Actions** -> **Delete VPC**.
3. Xác nhận xóa để tự động xóa subnets, route tables, internet gateways và security groups (`sg-ec2-backend`, `sg-rds-private`).
![Delete VPC](/images/5-Workshop/5.6-Cleanup/5.6.9-aws-vpc-group.png)
---

#### 8. Xóa IAM Roles & Policies

1. Truy cập [bảng điều khiển IAM Console](https://console.aws.amazon.com/iam/home#/roles).
2. Chọn `RentifulEC2SecretManagerRole` -> **Delete**.

![Delete IAM Role](/images/5-Workshop/5.6-Cleanup/5.6.4-aws-iam.png)
