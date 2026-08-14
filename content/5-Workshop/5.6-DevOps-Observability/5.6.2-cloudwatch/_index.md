---
title: "Configuring Amazon CloudWatch logs & metrics"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

#### 1. System observability overview

**Amazon CloudWatch** provides real-time monitoring solutions for application and infrastructure health:
- **CloudWatch Logs:** Centralizes NestJS application logs and EC2 container logs.
- **CloudWatch Metrics & Alarms:** Monitors CPU utilization, RAM, Disk I/O, and triggers automated alerts during errors or system overload.

---

#### 2. Install CloudWatch Agent on EC2

SSH into your backend EC2 server and install the Amazon CloudWatch Agent:

```bash
# Install Amazon CloudWatch Agent
sudo dnf install -y amazon-cloudwatch-agent

# Create CloudWatch Agent configuration file
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

Paste the following JSON configuration:

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

Start the CloudWatch Agent:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json \
  -s
```

---

#### 3. Create a CloudWatch Alarm for high CPU utilization

1. Access the [Amazon CloudWatch console](https://console.aws.amazon.com/cloudwatch/home).
2. In the left navigation menu, select **Alarms** -> **All alarms** -> **Create alarm**.
![Cloud-watch-1](/images/5-Workshop/5.8-Cloud/Cloud-watch-1.png)
3. Select metric -> **EC2** -> **Per-Instance Metrics** -> select `CPU Usage` for `real-estate-backend-server`.
![Cloud-watch-2](/images/5-Workshop/5.8-Cloud/Cloud-watch-2.png)
4. Configure condition settings:
   - **Threshold type**: Static
   - **Whenever CPUUtilization is...**: Greater than `80%`
   - **Period**: 5 minutes
![Cloud-watch-3](/images/5-Workshop/5.8-Cloud/Cloud-watch-3.png)
5. Configure actions:
   - Select **In alarm**
   - Select **Select an existing SNS topic**
   - Enter `CPU EC2 Alert`
![Cloud-watch-4](/images/5-Workshop/5.8-Cloud/Cloud-watch-4.png)
![Cloud-watch-5](/images/5-Workshop/5.8-Cloud/Cloud-watch-5.png)
6. Name & description:
   - Set alarm name: `EC2-High-CPU-Utilization-Alert`.
   - Enter description: `Notification when CPUUtilization on EC2 > 80% for 5 minutes`.
   - Click **Next**.
![Cloud-watch-6](/images/5-Workshop/5.8-Cloud/Cloud-watch-6.png)
7. Preview and create:
   - Click **Create alarm**.
![Cloud-watch-7](/images/5-Workshop/5.8-Cloud/Cloud-watch-7.png)
