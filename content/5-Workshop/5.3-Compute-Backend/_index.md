---
title: "Compute & backend deployment"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Overview

In this module, you will set up the virtual server host environment and deploy the containerized NestJS backend application:

- An **Amazon EC2** instance configured with **Docker** and **Docker Compose** runtimes inside the VPC.
- The **NestJS Backend** container service configured with environment variables, executing **Prisma** schema database migrations and exposing application API endpoints.

#### Module steps

1. [Launching an EC2 instance & installing Docker runtime](5.3.1-ec2-docker/)
2. [Deploying the NestJS backend application](5.3.2-nestjs-app-deploy/)
