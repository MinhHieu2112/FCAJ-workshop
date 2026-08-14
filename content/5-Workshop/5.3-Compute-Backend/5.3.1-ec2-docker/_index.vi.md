---
title: "Khởi tạo EC2 instance & cài đặt Docker runtime"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Bước 1: Khởi tạo EC2 instance

1. Truy cập [bảng điều khiển Amazon EC2](https://console.aws.amazon.com/ec2/home).
2. Chọn **Instances** -> **Launch instances**.
3. Cấu hình:

| Cài đặt | Giá trị |
|---|---|
| Name | `real-estate-backend-server` |
| AMI | Amazon Linux 2023 (hoặc Ubuntu 22.04 LTS) |
| Instance type | `t3.small` hoặc `t3.medium` |
| Key pair | Tạo mới hoặc chọn key pair có sẵn (.pem) |
| VPC | Chọn VPC của bạn |
| Subnet | **Public subnet A** |
| Auto-assign public IP | Bật |
| Security group | `sg-ec2-backend` |
| IAM instance profile | `EC2-Backend-Services-Role` |

4. Nhấn **Launch instance**.

#### Bước 2: Kết nối và cài đặt Docker

SSH vào instance và cài đặt Docker:

```bash
ssh -i your-key.pem ec2-user@<public-ip>

# Cập nhật các gói phần mềm
sudo dnf update -y

# Cài đặt Docker
sudo dnf install -y docker

# Khởi động và bật dịch vụ Docker khi khởi động
sudo systemctl start docker
sudo systemctl enable docker

# Thêm ec2-user vào nhóm docker (tránh phải dùng sudo cho mọi lệnh)
sudo usermod -aG docker ec2-user

# Áp dụng thay đổi nhóm (hoặc đăng xuất và kết nối lại)
newgrp docker

# Kiểm tra cài đặt
docker --version
```

#### Bước 3: Cài đặt Docker Compose

```bash
# Cài đặt Docker Compose plugin
sudo dnf install -y docker-compose-plugin

# Kiểm tra phiên bản
docker compose version
```

{{% notice tip %}}
Ở phần 5.6.1, GitHub Actions sẽ tự động hóa các bước build, push và deploy container mới trên mỗi commit vào nhánh main.
{{% /notice %}}
