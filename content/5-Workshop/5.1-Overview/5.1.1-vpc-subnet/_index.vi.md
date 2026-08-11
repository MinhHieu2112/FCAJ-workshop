---
title: "Thiết lập VPC, subnet & security groups"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.1.1. </b> "
---

#### Tổng quan thiết kế VPC

Hệ thống sử dụng mô hình **VPC hai tầng** với public và private subnet trải rộng trên hai availability zone để đảm bảo tính sẵn sàng cao:

| Tài nguyên | CIDR / Chi tiết |
|---|---|
| VPC | `10.0.0.0/16` |
| Public subnet A | `10.0.1.0/24` (EC2, ALB) |
| Public subnet B | `10.0.2.0/24` (ALB phụ) |
| Private subnet A | `10.0.3.0/24` (RDS chính) |
| Private subnet B | `10.0.4.0/24` (RDS dự phòng) |

#### Bước 1: Tạo VPC

1. Truy cập [Bảng điều khiển Amazon VPC](https://console.aws.amazon.com/vpc/home).
2. Chọn **Your VPCs** → **Create VPC**.
3. Chọn **VPC and more** để tự động tạo subnet và route table.
4. Cấu hình:
   - **IPv4 CIDR**: `10.0.0.0/16`
   - **Number of availability zones**: `2`
   - **Number of public subnets**: `2`
   - **Number of private subnets**: `2`
   - **NAT gateways**: `None` (tiết kiệm chi phí cho môi trường phát triển)
5. Nhấn **Create VPC**.

#### Bước 2: Cấu hình security groups

Tạo hai security group trong VPC vừa tạo:

**Security group: `sg-ec2-backend`** (gán cho EC2 instance)

| Loại | Giao thức | Cổng | Nguồn |
|---|---|---|---|
| SSH | TCP | 22 | IP của bạn |
| Custom TCP | TCP | 4000 | `sg-alb-public` (ALB security group) |

**Security group: `sg-rds-private`** (gán cho RDS instance)

| Loại | Giao thức | Cổng | Nguồn |
|---|---|---|---|
| Custom TCP | TCP | 5432 | `sg-ec2-backend` |

{{% notice warning %}}
**Không** mở cổng 5432 (PostgreSQL) ra internet công khai. Luôn giới hạn quyền truy cập cơ sở dữ liệu chỉ từ security group của EC2.
{{% /notice %}}
