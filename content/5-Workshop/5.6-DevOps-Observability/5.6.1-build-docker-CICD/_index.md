---
title: "Configuring GitHub Actions CI/CD workflow"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

#### Automated CI/CD pipeline overview

![CI/CD Pipeline Architecture](/images/5-Workshop/5.7-Pipeline/architecture-pipeline.gif)

Whenever code changes are pushed to the `main` branch of your GitHub repository, **GitHub Actions** automatically triggers the following pipeline:
1. Authenticate with Docker Hub.
2. Build the production Docker image for the NestJS backend application.
3. Push the newly tagged Docker image to Docker Hub.
4. Establish an SSH connection to the EC2 server, pull the latest image, execute Prisma database migrations, and restart the backend container without downtime.

---

#### Step 1: Configure GitHub Repository Secrets

1. Open your project repository on GitHub.
2. Navigate to **Settings** -> **Secrets and variables** -> **Actions** -> **New repository secret**.
3. Configure the following repository secrets:

| Secret Name | Description |
|---|---|
| `DOCKERHUB_USERNAME` | Your Docker Hub account username |
| `DOCKERHUB_TOKEN` | Personal Access Token generated from Docker Hub Account Settings |
| `EC2_HOST` | Public IP address of your EC2 backend server |
| `EC2_USERNAME` | SSH login username (`ec2-user` or `ubuntu`) |
| `EC2_SSH_KEY` | Entire content of your private SSH key file (`.pem`) |

---

#### Step 2: Create GitHub Actions Workflow file

Create `.github/workflows/deploy.yml` in your monorepo repository:

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
            # Pull latest backend image
            docker pull ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest
            
            # Execute database schema migrations
            docker run --rm \
              --env-file ~/apps/server/.env \
              ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest \
              npx prisma migrate deploy
            
            # Stop and remove legacy container
            docker stop nestjs-backend || true
            docker rm nestjs-backend || true
            
            # Launch updated backend container instance
            docker run -d \
              --name nestjs-backend \
              --restart unless-stopped \
              -p 4000:4000 \
              --env-file ~/apps/server/.env \
              ${{ secrets.DOCKERHUB_USERNAME }}/real-estate-server:latest
            
            # Prune dangling Docker images
            docker image prune -f
```

---

#### Step 3: Verify pipeline execution

1. Commit and push the workflow file to your `main` branch:
```bash
git add .github/workflows/deploy.yml
git commit -m "ci: add EC2 deployment workflow"
git push origin main
```
2. Navigate to the **Actions** tab on GitHub to monitor pipeline execution and verify successful container deployment on EC2.