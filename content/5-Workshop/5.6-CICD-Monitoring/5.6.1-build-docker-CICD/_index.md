---
title: "GitHub Actions CI/CD workflow"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

#### GitHub Actions workflow overview

Automating deployment ensures that every change pushed to the repository is compiled, containerized, and deployed to your EC2 instance smoothly without manual SSH commands.

#### Step 1: Configure repository secrets

In your GitHub repository, navigate to **Settings** → **Secrets and variables** → **Actions** → **New repository secret** and add:

- `DOCKERHUB_USERNAME`: Your Docker Hub username.
- `DOCKERHUB_TOKEN`: Docker Hub Personal Access Token.
- `EC2_HOST`: Public IP address or Elastic IP of your EC2 instance.
- `EC2_USERNAME`: `ec2-user` (or `ubuntu`).
- `EC2_SSH_KEY`: Content of your `.pem` private SSH key file.

#### Step 2: Create `.github/workflows/deploy.yml`

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

#### Step 3: Verify execution

1. Push a commit to the `main` branch.
2. Monitor progress under the **Actions** tab in GitHub.
3. Check application logs on EC2: `docker logs -f nestjs-backend`.

#### Architecture diagram

![Architecture](/images/5-Workshop/5.7-Pipeline/architecture-pipeline.gif)