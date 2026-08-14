---
title: "Khởi tạo Amazon RDS PostgreSQL (PostGIS)"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2.2. </b> "
---

#### Tại sao đặt RDS trong private subnet?

Việc đặt cơ sở dữ liệu trong **private subnet** đảm bảo nó không bao giờ có thể truy cập trực tiếp từ internet. Chỉ các EC2 instance trong cùng VPC (thông qua quy tắc security group) mới có thể thiết lập kết nối.

#### Bước 1: Tạo DB subnet group

1. Truy cập [bảng điều khiển Amazon RDS](https://console.aws.amazon.com/rds/home).
2. Chọn **Subnet groups** -> **Create DB subnet group**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Create-db-subnet-group.png)
3. Nhập:
   - **Name**: `real-estate-rds-subnet-group`
   - **Description**: `DB subnet group for real-estate-rental-db`
   - **VPC**: chọn VPC đã tạo ở bước 5.1.1.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Information.png)
4. Tại phần Add subnets:
   - **Availability Zones**: Chọn các AZ tương ứng với các Private Subnet của bạn (ví dụ: chọn us-east-1a và us-east-1b).
   - **Subnets**: Tại ô Select subnets, nhấn chọn đúng 2 ID của Private Subnet A và Private Subnet B đã tạo trước đó.
(Lưu ý: Sau khi chọn, danh sách subnet được chọn sẽ xuất hiện ở bảng Subnets selected phía dưới).
5. Nhấn **Create**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Add-subnet.png)

#### Bước 2: Khởi tạo RDS PostgreSQL instance

1. Trong RDS Console, chọn **Databases** -> **Create database**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-1.png)
2. Chọn **PostgreSQL** và chọn **Full Configuration**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-2.png)
3. Chọn **Dev/test** và chọn **Single AZ**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-3.png)
4. Cấu hình thông số
- Chọn version của database (**Engine version**)
- Đặt tên cho DB (**DB instance identifier**)
- Chọn **Self managed** để thiết lập tài khoản, mật khẩu database của riêng bạn
- Nhập credentials settings: **master username**, **master password**
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-4.png)
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-5.png)
5. Cấu hình thông số
- Chọn **Burstable classes** gói cơ bản để tiết kiệm chi phí
- Chọn DB instance class **db.t3.micro**
- Dung lượng lưu trữ chọn loại **SSD gp3** và thiết lập **20 GiB**
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-6.png)
6. Tắt **Autoscaling** và chọn **Create database**.
![RDS subnet group](/images/5-Workshop/5.2-Prerequisite/5.2.2-RDS/Rds-7.png)

#### Bước 3: Kích hoạt phần mở rộng PostGIS

Sau khi cơ sở dữ liệu chạy xong, kết nối qua EC2 instance và chạy:

```sql
-- Kết nối vào rental_db
\c rental_db

-- Kích hoạt phần mở rộng không gian PostGIS
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology;

-- Xác nhận cài đặt thành công
SELECT PostGIS_Version();
```

#### Bước 4: Thêm database URL vào biến môi trường

Cập nhật `apps/server/.env` với endpoint RDS:

```env
DATABASE_URL="postgresql://postgres:<password>@<rds-endpoint>:5432/rental_db?schema=public"
```
