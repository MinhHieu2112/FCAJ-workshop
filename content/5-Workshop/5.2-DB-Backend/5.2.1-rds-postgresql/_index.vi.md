---
title: "Khởi tạo Amazon RDS PostgreSQL (PostGIS) trong private subnet"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.2.1. </b> "
---

#### Tại sao đặt RDS trong private subnet?

Việc đặt cơ sở dữ liệu trong **private subnet** đảm bảo nó không bao giờ có thể truy cập trực tiếp từ internet. Chỉ các EC2 instance trong cùng VPC (thông qua quy tắc security group) mới có thể thiết lập kết nối.

#### Bước 1: Tạo DB subnet group

1. Truy cập [Bảng điều khiển Amazon RDS](https://console.aws.amazon.com/rds/home).
2. Chọn **Subnet groups** → **Create DB subnet group**.
3. Nhập:
   - **Name**: `real-estate-rds-subnet-group`
   - **VPC**: chọn VPC đã tạo ở bước 5.1.1.
   - **Subnets**: chọn cả hai **private subnet** (private subnet A và private subnet B).
4. Nhấn **Create**.

#### Bước 2: Khởi tạo RDS PostgreSQL instance

1. Trong RDS Console, chọn **Databases** → **Create database**.
2. Chọn **Standard create** và chọn **PostgreSQL**.
3. Cấu hình:

| Cài đặt | Giá trị |
|---|---|
| DB instance identifier | `real-estate-rental-db` |
| Master username | `postgres` |
| Master password | (đặt mật khẩu mạnh, lưu vào Secrets Manager) |
| DB instance class | `db.t3.micro` (đủ điều kiện Free Tier) |
| Storage | 20 GB SSD (gp2) |
| Multi-AZ deployment | Không (môi trường phát triển) |
| VPC | Chọn VPC của bạn |
| DB subnet group | `real-estate-rds-subnet-group` |
| Public access | **Không** |
| VPC security group | `sg-rds-private` |
| Initial database name | `rental_db` |

4. Nhấn **Create database**.

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

{{% notice tip %}}
Tìm endpoint RDS tại **Databases** → chọn DB instance → **Connectivity & security** → **Endpoint**.
{{% /notice %}}
