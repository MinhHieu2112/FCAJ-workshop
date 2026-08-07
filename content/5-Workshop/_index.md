---
title: "Workshop"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building a Cloud-Native Real Estate Rental Management System on AWS

#### Overview

In this hands-on workshop, you will build and configure key AWS cloud services for a **Real Estate Rental Management System** built with **NestJS** and **Next.js**.

You will learn how to integrate core enterprise AWS services into a modern web application:
+ **Amazon Cognito** - User identity management, authentication, role-based access control (RBAC), and JWT token handling.
+ **Amazon S3** - Secure media storage for property images using short-lived Presigned URLs for direct client uploads.
+ **Amazon Location Service** - Address geocoding (converting address strings to coordinates) and interactive map rendering.
+ **Amazon RDS (PostgreSQL + PostGIS)** - Managed relational database for rental application workflows, lease contracts, and spatial queries.

{{% notice tip %}}
This workshop is designed to mirror real-world production setups, utilizing AWS SDK v3 with TypeScript and NestJS modular backend architecture.
{{% /notice %}}

#### Content

1. [System Architecture & Overview](5.1-Overview/)
2. [Prerequisites & Environment Setup](5.2-Prerequisites/)
3. [Authentication with Amazon Cognito](5.3-Cognito-Auth/)
4. [Media Storage with Amazon S3](5.4-S3-Storage/)
5. [Address Geocoding with Amazon Location Service](5.5-Location-Service/)
6. [Resource Cleanup](5.6-Cleanup/)
