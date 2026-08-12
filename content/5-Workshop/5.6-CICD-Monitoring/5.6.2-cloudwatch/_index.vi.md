---
title: "Cấu hình CloudWatch logs & metrics"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.6.3. </b> "
---

#### Tổng quan khả năng quan sát với CloudWatch

Giám sát tình trạng ứng dụng và ghi lại log thời gian thực là thành phần không thể thiếu đối với môi trường production. Trong phần này, bạn sẽ cài đặt **CloudWatch Logs Agent** trên EC2 và thiết lập các cảnh báo dựa trên chỉ số hiệu năng.

#### Bước 1: Cài đặt CloudWatch Agent trên EC2

SSH vào EC2 instance và cài đặt CloudWatch Agent:

```bash
# Tải và cài đặt gói CloudWatch Agent
sudo dnf install -y amazon-cloudwatch-agent

# Tạo file cấu hình CloudWatch Agent
sudo tee /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json > /dev/null << 'EOF'
{
  "agent": {
    "metrics_collection_interval": 60,
    "run_as_user": "root"
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/lib/docker/containers/*/*-json.log",
            "log_group_name": "/aws/ec2/real-estate-backend",
            "log_stream_name": "{instance_id}",
            "timestamp_format": "%Y-%m-%dT%H:%M:%S.%fZ"
          }
        ]
      }
    }
  },
  "metrics": {
    "metrics_collected": {
      "mem": {
        "measurement": ["mem_used_percent"]
      },
      "disk": {
        "measurement": ["used_percent"],
        "resources": ["/"]
      }
    }
  }
}
EOF

# Khởi động và kích hoạt dịch vụ CloudWatch Agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json \
  -s
```

#### Bước 2: Xem log trên CloudWatch Console

1. Truy cập [Bảng điều khiển Amazon CloudWatch](https://console.aws.amazon.com/cloudwatch/home).
2. Tại menu bên trái, chọn **Logs** → **Log groups**.
3. Chọn `/aws/ec2/real-estate-backend` để xem các luồng log thời gian thực được đẩy từ container NestJS.

#### Bước 3: Tạo cảnh báo vượt ngưỡng CPU utilization

1. Trong CloudWatch Console, chọn **Alarms** → **All alarms** → **Create alarm**.
2. Nhấn **Select metric** → **EC2** → **Per-Instance Metrics**.
3. Chọn chỉ số **CPUUtilization** tương ứng với EC2 instance của bạn.
4. Cấu hình điều kiện ngưỡng:
   - **Threshold type**: Static
   - **Whenever CPUUtilization is**: Greater/Equal
   - **Than**: `80`
   - **Period**: 5 minutes
5. Thêm hành động gửi email thông báo qua **Amazon SNS**.
6. Đặt tên cảnh báo `High-CPU-Utilization-RealEstate-EC2` và nhấn **Create alarm**.
