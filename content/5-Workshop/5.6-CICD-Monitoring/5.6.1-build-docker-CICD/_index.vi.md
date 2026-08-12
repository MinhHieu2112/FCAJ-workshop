---
title: "Thiết lập GitHub Actions workflow"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

#### Tổng quan quy trình GitHub Actions

Tự động hóa triển khai giúp mọi thay đổi code đẩy lên repository đều được biên dịch, đóng gói container và phát hành lên EC2 một cách nhất quán mà không cần thực hiện lệnh SSH thủ công.

#### Bước 1: Cấu hình repository secrets

Trong GitHub repository của bạn, truy cập **Settings** → **Secrets and variables** → **Actions** → **New repository secret** và thêm các biến:

- `DOCKERHUB_USERNAME`: Tên tài khoản Docker Hub của bạn.
- `DOCKERHUB_TOKEN`: Personal Access Token từ Docker Hub.
- `EC2_HOST`: Địa chỉ IP public hoặc Elastic IP của EC2 instance.
- `EC2_USERNAME`: `ec2-user` (hoặc `ubuntu`).
- `EC2_SSH_KEY`: Nội dung file khóa SSH private `.pem`.

#### Bước 2: Tạo file `.github/workflows/deploy.yml`

```yaml
name: Build Docker & Deploy to EC2

on:
  push:
    branches:
      - master

  workflow_dispatch:

jobs:
  build-and-push:
    name: Build Docker Image on GitHub & Push to Docker Hub
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v4

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./apps/server/Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest

  deploy-ec2:
    needs: build-and-push
    name: Pull Image & Restart Container on EC2
    runs-on: ubuntu-latest

    steps:
      - name: Executing remote SSH commands to deploy
        uses: appleboy/ssh-action@v1.2.0
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          port: ${{ secrets.EC2_PORT }}
          script_stop: true 
          script: |
            cd ~/Real-estate
            git checkout master
            git fetch origin master
            git reset --hard origin/master
            docker compose pull server
            docker compose up -d --force-recreate server
            docker image prune -f
```

#### Bước 3: Kiểm tra quy trình tự động

1. Thực hiện push commit lên nhánh `main`.
2. Theo dõi tiến trình build tại tab **Actions** trên GitHub.
3. Kiểm tra log ứng dụng trên máy chủ EC2: `docker logs -f nestjs-backend`.

#### Sơ đồ mô phỏng quy trình

![Architecture](/images/5-Workshop/5.7-Pipeline/architecture-pipeline.gif)

