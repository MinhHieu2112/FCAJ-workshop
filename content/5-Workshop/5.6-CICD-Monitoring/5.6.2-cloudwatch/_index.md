---
title: "Amazon CloudWatch logs & metrics"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.6.3. </b> "
---

#### CloudWatch observability overview

Monitoring application health and capturing runtime logs in real time is essential for production deployments. In this module, you will set up **CloudWatch Logs Agent** on EC2 and configure metric alarms for system alerts.

#### Step 1: Install AWS CloudWatch Logs Agent on EC2

SSH into your EC2 instance and install the CloudWatch Agent:

```bash
# Download and install CloudWatch Agent package
sudo dnf install -y amazon-cloudwatch-agent

# Create CloudWatch Agent configuration
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

# Start and enable CloudWatch Agent service
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json \
  -s
```

#### Step 2: View logs in CloudWatch Console

1. Open the [Amazon CloudWatch Console](https://console.aws.amazon.com/cloudwatch/home).
2. On the left navigation pane, select **Logs** → **Log groups**.
3. Select `/aws/ec2/real-estate-backend` to view real-time log streams emitted by the NestJS container.

#### Step 3: Create CPU utilization metric alarm

1. In CloudWatch Console, select **Alarms** → **All alarms** → **Create alarm**.
2. Click **Select metric** → **EC2** → **Per-Instance Metrics**.
3. Select **CPUUtilization** for your EC2 instance.
4. Set threshold conditions:
   - **Threshold type**: Static
   - **Whenever CPUUtilization is**: Greater/Equal
   - **Than**: `80`
   - **Period**: 5 minutes
5. Configure notification action to send an email via **Amazon SNS**.
6. Name the alarm `High-CPU-Utilization-RealEstate-EC2` and click **Create alarm**.
