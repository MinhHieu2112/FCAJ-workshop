---
title: "Database & backend server deployment"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Overview

In this module, you will provision the core compute and data layer of the system:

- An **Amazon RDS PostgreSQL** instance with the **PostGIS** extension inside a private subnet.
- An **Amazon EC2** instance with Docker runtime in a public subnet to host the NestJS backend container.

#### Module steps

1. [Initializing Amazon RDS PostgreSQL (PostGIS) in a private subnet](5.2.1-rds-postgresql/)
2. [Launching an EC2 instance & installing Docker runtime](5.2.2-ec2-docker/)
