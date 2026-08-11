---
title: "Launching an EC2 instance & installing Docker runtime"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2.2. </b> "
---

#### Step 1: Launch an EC2 instance

1. Open the [Amazon EC2 Console](https://console.aws.amazon.com/ec2/home).
2. Select **Instances** → **Launch instances**.
3. Configure:

| Setting | Value |
|---|---|
| Name | `real-estate-backend-server` |
| AMI | Amazon Linux 2023 (or Ubuntu 22.04 LTS) |
| Instance type | `t3.small` or `t3.medium` |
| Key pair | Create or select an existing key pair (.pem) |
| VPC | Select your VPC |
| Subnet | **Public subnet A** |
| Auto-assign public IP | Enable |
| Security group | `sg-ec2-backend` |
| IAM instance profile | `RealEstateEC2Role` |

4. Click **Launch instance**.

#### Step 2: Connect and install Docker

SSH into the instance and install Docker:

```bash
ssh -i your-key.pem ec2-user@<public-ip>

# Update packages
sudo dnf update -y

# Install Docker
sudo dnf install -y docker

# Start and enable Docker service
sudo systemctl start docker
sudo systemctl enable docker

# Add ec2-user to the docker group (avoid using sudo for every command)
sudo usermod -aG docker ec2-user

# Apply group changes (or log out and reconnect)
newgrp docker

# Verify installation
docker --version
```

#### Step 3: Install Docker Compose

```bash
# Install Docker Compose plugin
sudo dnf install -y docker-compose-plugin

# Verify
docker compose version
```

#### Step 4: Pull and run the backend container

```bash
# Pull the latest backend image from Docker Hub
docker pull <your-dockerhub-username>/real-estate-server:latest

# Run the container
docker run -d \
  --name nestjs-backend \
  --restart unless-stopped \
  -p 4000:4000 \
  --env-file /home/ec2-user/apps/server/.env \
  <your-dockerhub-username>/real-estate-server:latest
```

{{% notice tip %}}
In section 5.6.1, GitHub Actions will automate the build, push, and `docker pull` steps on every commit to the main branch.
{{% /notice %}}
