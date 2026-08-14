---
title: "CI/CD automation & monitoring"
date: 2026-08-06
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Overview

In this module, you will establish production-grade CI/CD pipeline automation and system observability:

- **GitHub Actions Workflow** – Automatically test, build, and push NestJS server Docker images to Docker Hub on every push to `main`, triggering automated deployment updates on EC2 via SSH.
- **Amazon CloudWatch** – Configure centralized container application logging and metric alarm alerts for instance resource utilization and API health.

#### Module steps

1. [Configuring GitHub Actions CI/CD workflow](5.6.1-build-docker-CICD/)
2. [Configuring Amazon CloudWatch logs & metrics](5.6.2-cloudwatch/)
