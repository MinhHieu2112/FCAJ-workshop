---
title: "Cấu hình GitHub Actions CI/CD workflow"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

#### Quy trình tự động hóa CI/CD

![CI/CD Pipeline Architecture](/images/5-Workshop/5.7-Pipeline/architecture-pipeline.gif)

Mỗi khi mã nguồn được push lên nhánh `main` của repository GitHub, **GitHub Actions** tự động thực hiện:
1. Đăng nhập vào Docker Hub.
2. Build Docker Image cho ứng dụng NestJS backend.
3. Push Docker Image mới lên Docker Hub.
4. Kết nối SSH vào EC2 instance, kéo image mới về và khởi chạy lại container không làm gián đoạn dịch vụ.

---

#### Bước 1: Khai báo Secrets trên GitHub Repository

1. Truy cập repository GitHub của dự án.
2. Chọn **Settings** -> **Secrets and variables** -> **Actions** -> **New repository secret**.
3. Khai báo các secret sau:

| Tên Secret | Mô tả |
|---|---|
| `DOCKERHUB_USERNAME` | Tên đăng nhập Docker Hub của bạn |
| `DOCKERHUB_TOKEN` | Access Token khởi tạo từ Docker Hub Account Settings |
| `EC2_HOST` | Địa chỉ IP công khai (Public IP) của máy chủ EC2 |
| `EC2_USERNAME` | Tên người dùng SSH (thường là `ec2-user` hoặc `ubuntu`) |
| `EC2_SSH_KEY` | Nội dung đầy đủ của file khóa riêng SSH (`.pem`) |

---

#### Bước 2: Tạo GitHub Actions Workflow File

Tạo file `.github/workflows/deploy.yml` trong mã nguồn monorepo:

```yaml
name: Build & Deploy Backend to EC2

on:
  push:
    branches:
      - main
    paths:
      - 'apps/server/**'
      - 'packages/shared/**'
      - '.github/workflows/deploy.yml'

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./apps/server/Dockerfile
          push: true
          tags: |
            ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest
            ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:${{ github.sha }}

      - name: Deploy to EC2 via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            # Pull latest image
            docker pull ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest
            
            # Run Prisma database migrations
            docker run --rm \
              --env-file ~/apps/server/.env \
              ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest \
              npx prisma migrate deploy
            
            # Stop and remove old container
            docker stop nestjs-backend || true
            docker rm nestjs-backend || true
            
            # Start new backend container
            docker run -d \
              --name nestjs-backend \
              --restart unless-stopped \
              -p 4000:4000 \
              --env-file ~/apps/server/.env \
              ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest
            
            # Dọn dẹp Docker images cũ
            docker image prune -f
```

---

#### Bước 3: Kiểm tra kết quả thực thi

1. Commit và push thay đổi lên nhánh `main`:
```bash
git add .github/workflows/deploy.yml
git commit -m "ci: add EC2 deployment workflow"
git push origin main
```
2. Truy cập tab **Actions** trên GitHub để theo dõi tiến trình chạy workflow và xác nhận kết quả deploy thành công trên EC2.
