---
title: "CI/CD automation & monitoring"
date: 2026-08-06
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Overview

In this module, you will set up production-ready CI/CD automation and system observability:

- **GitHub Actions Workflow** – Automatically build, test, and push NestJS server Docker images to Docker Hub on push to `main`, then trigger deployment to EC2 via SSH.
- **Amazon CloudWatch** – Configure centralized logging and metric alarms for system health and API request monitoring.

#### Module steps

1. [Configuring GitHub Actions CI/CD workflow](5.6.1-github-actions/)
2. [Configuring Amazon CloudWatch logs & metrics](5.6.2-cloudwatch/)
