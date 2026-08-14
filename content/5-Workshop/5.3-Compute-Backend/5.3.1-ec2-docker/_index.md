---
title: "Launching an EC2 instance & installing Docker runtime"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Step 1: Launch an EC2 instance

1. Open the [Amazon EC2 Console](https://console.aws.amazon.com/ec2/home).
2. Select **Instances** -> **Launch instances**.
3. Configure instance options:

| Setting | Value |
|---|---|
| Name | `real-estate-backend-server` |
| AMI | Amazon Linux 2023 (or Ubuntu 22.04 LTS) |
| Instance type | `t3.small` or `t3.medium` |
| Key pair | Create a new key pair or select an existing `.pem` key |
| VPC | Select your VPC |
| Subnet | **Public subnet A** |
| Auto-assign public IP | Enable |
| Security group | `sg-ec2-backend` |
| IAM instance profile | `EC2-Backend-Services-Role` |

4. Click **Launch instance**.

#### Step 2: Connect and install Docker engine

SSH into your EC2 instance and install Docker:

```bash
ssh -i your-key.pem ec2-user@<public-ip>

# Update system package repositories
sudo dnf update -y

# Install Docker engine
sudo dnf install -y docker

# Start and enable Docker service to run on system boot
sudo systemctl start docker
sudo systemctl enable docker

# Add ec2-user to the docker user group to run docker commands without sudo
sudo usermod -aG docker ec2-user

# Apply updated group membership
newgrp docker

# Verify Docker installation
docker --version
```

#### Step 3: Install Docker Compose plugin

```bash
# Install Docker Compose CLI plugin
sudo dnf install -y docker-compose-plugin

# Verify installation
docker compose version
```

{{% notice tip %}}
In section 5.6.1, GitHub Actions will automate container building, image pushing, and SSH deployment updates on every commit pushed to the main branch.
{{% /notice %}}
