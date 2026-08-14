---
title: "Cấu hình Amazon CloudWatch logs & metrics"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

#### 1. Tổng quan về giám sát hệ thống

**Amazon CloudWatch** cung cấp giải pháp giám sát ứng dụng và hạ tầng theo thời gian thực:
- **CloudWatch Logs:** Tập trung hóa log ứng dụng NestJS và log container từ EC2.
- **CloudWatch Metrics & Alarms:** Giám sát mức sử dụng CPU, RAM, Disk I/O và phát cảnh báo tự động khi phát sinh lỗi hoặc quá tải.

---

#### 2. Cài đặt CloudWatch Agent trên EC2

SSH vào máy chủ EC2 backend và cài đặt Amazon CloudWatch Agent:

```bash
# Cài đặt Amazon CloudWatch Agent
sudo dnf install -y amazon-cloudwatch-agent

# Tạo file cấu hình CloudWatch Agent
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

Dán cấu hình JSON sau:

```json
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
            "log_stream_name": "{instance_id}-docker-logs",
            "timezone": "UTC"
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
```

Khởi động CloudWatch Agent:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json \
  -s
```

---

#### 3. Tạo CloudWatch Alarm cảnh báo CPU sử dụng cao

1. Truy cập [bảng điều khiển Amazon CloudWatch](https://console.aws.amazon.com/cloudwatch/home).
2. Tại menu trái, chọn **Alarms** -> **All alarms** -> **Create alarm**.
![Cloud-watch-1](/images/5-Workshop/5.8-Cloud/Cloud-watch-1.png)
3. Select metric -> **EC2** -> **Per-Instance Metrics** -> chọn `CPU Usage` của `real-estate-backend-server`.
![Cloud-watch-2](/images/5-Workshop/5.8-Cloud/Cloud-watch-2.png)
4. Cấu hình điều kiện:
   - **Threshold type**: Static
   - **Whenever CPUUtilization is...**: Greater than `80%`
   - **Period**: 5 minutes
![Cloud-watch-3](/images/5-Workshop/5.8-Cloud/Cloud-watch-3.png)
5. Configure actions:
   - Chọn **alarm**
   - Chọn **Select an existing SNS topic**
   - Điền `CPU EC2 Alert`
![Cloud-watch-4](/images/5-Workshop/5.8-Cloud/Cloud-watch-4.png)
![Cloud-watch-5](/images/5-Workshop/5.8-Cloud/Cloud-watch-5.png)
6. Name & description:
   - Đặt tên Alarm: `EC2-High-CPU-Utilization-Alert`.
   - Điền description: `Thông báo khi CPUUtilization trên EC2 > 80% trong 5 phút`.
   - Nhấn **Following**.
![Cloud-watch-6](/images/5-Workshop/5.8-Cloud/Cloud-watch-6.png)
7. Preview and create:
   - Nhấn **Create alarm**.
![Cloud-watch-7](/images/5-Workshop/5.8-Cloud/Cloud-watch-7.png)
