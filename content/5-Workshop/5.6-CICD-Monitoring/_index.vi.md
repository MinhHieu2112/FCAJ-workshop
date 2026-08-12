---
title: "Tự động hóa CI/CD & giám sát"
date: 2026-08-06
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Tổng quan

Phần này hướng dẫn thiết lập quy trình tự động hóa CI/CD và giám sát hệ thống chuẩn sản xuất:

- **GitHub Actions Workflow** – Tự động hóa đóng gói Docker image cho NestJS server, đẩy lên Docker Hub khi có commit mới ở nhánh `main`, sau đó kích hoạt triển khai cập nhật trên EC2 qua SSH.
- **Amazon CloudWatch** – Cấu hình tập trung log ứng dụng và cảnh báo chỉ số giám sát hiệu năng hệ thống.

#### Các bước thực hiện

1. [Configuring GitHub Actions CI/CD workflow](5.6.1-build-docker-CICD/)
2. [Configuring Amazon CloudWatch logs & metrics](5.6.2-cloudwatch/)
