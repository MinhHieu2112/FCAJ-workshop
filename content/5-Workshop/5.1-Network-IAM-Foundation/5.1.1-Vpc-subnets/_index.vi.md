---
title: "Cấu hình VPC, Security Groups & Elastic IP"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1.1. </b> "
---

### 1. Tổng quan

Mô hình mạng VPC được thiết kế nhằm đảm bảo khả năng kết nối an toàn cho hệ thống:

- **Public Subnet**: Nơi đặt máy chủ **EC2 Backend** chạy Caddy Reverse Proxy và container NestJS Docker.
- **Private Subnet**: Nơi đặt cơ sở dữ liệu **Amazon RDS PostgreSQL**, hoàn toàn không có Public IP, chỉ nhận kết nối nội bộ từ Security Group của EC2 trên port 5432.
- **Elastic IP (EIP)**: Gán địa chỉ IP công khai cố định cho EC2 để trỏ tên miền **DuckDNS** (`nestro.duckdns.org`) ổn định.

---

### 2. Các bước triển khai thực tế

#### Bước 1: Khởi tạo VPC và subnets

1. Truy cập vào [bảng điều khiển Amazon VPC](https://console.aws.amazon.com/vpc/home).
2. Chọn **Your VPCs** -> Click nút **Create VPC**.
![Create VPC](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-1.png)

3. Chọn **VPC and more** để tự động tạo VPC cùng các Subnets (Public và Private), Route Tables và Internet Gateway.
4. Nhập thông số cấu hình:
   - **Name tag auto-generation**: `my-app-vpc`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - **Number of Availability Zones (AZs)**: `2`
   - **Number of public subnets**: `2`
   - **Number of private subnets**: `2`
![VPC configuration 1](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-2.png)
![VPC configuration 2](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-3.png)

5. Xác nhận và chọn **Create VPC**.
![VPC preview](/images/5-Workshop/5.1-Overview/5.1.1-VPC-Subnets/Create-vpc-4.png)

---

#### Bước 2: Cấu hình Security Groups cho EC2 và RDS

1. Chọn **Security Groups** -> **Create security group**.
2. **Tạo `sg-ec2-backend` cho máy chủ EC2:**
   - **Name**: `sg-ec2-backend`
   - **Inbound rules**:

| Type | Port | Source | Mục đích |
|---|---|---|---|
| **HTTP** | `80` | `0.0.0.0/0` | Cho phép Caddy nhận Let's Encrypt HTTP challenge và tự động redirect sang HTTPS |
| **HTTPS** | `443` | `0.0.0.0/0` | Cho phép Vercel frontend gọi API HTTPS đến `https://nestro.duckdns.org` |
| **SSH** | `22` | `My IP` hoặc `0.0.0.0/0` | Quản trị máy chủ và GitHub Actions SSH deploy |

{{% notice warning %}}
**Lưu ý quan trọng**: Cổng **4000** của backend NestJS **KHÔNG được mở** trên Security Group. Container Docker chỉ giao tiếp nội bộ với Caddy qua `localhost:4000`. Mọi lưu lượng bên ngoài phải đi qua cổng 443 HTTPS của Caddy.
{{% /notice %}}

3. **Tạo `sg-rds-private` cho Amazon RDS:**
   - **Name**: `sg-rds-private`
   - **Inbound rules**: Chọn PostgreSQL (`5432`), chọn Source là Security Group `sg-ec2-backend`.

---

#### Bước 3: Đăng ký và gán Elastic IP (EIP) cho EC2

1. Trong VPC Console, tại menu bên trái chọn **Elastic IPs**.
2. Nhấn **Allocate Elastic IP address** -> chọn **Allocate**.
3. Chọn Elastic IP vừa tạo -> chọn **Actions** -> **Associate Elastic IP address**.
4. Chọn **Resource type**: `Instance`, chọn máy chủ EC2 của bạn và nhấn **Associate**.
5. Ghi lại địa chỉ IP này (ví dụ: `54.210.xx.xx`) để trỏ bản ghi A trên DuckDNS ở Mô-đun 5.5.