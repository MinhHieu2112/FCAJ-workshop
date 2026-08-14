---
title: "Tự động hóa CI/CD & giám sát"
date: 2026-08-06
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Tổng quan

Trong mô-đun này, bạn sẽ xây dựng quy trình tự động hóa CI/CD và giám sát hệ thống chuẩn production:

- **GitHub Actions Workflow** – Tự động hóa quá trình kiểm thử, build Docker image của ứng dụng backend NestJS, đẩy lên Docker Hub khi push vào nhánh `main` và kích hoạt deploy lên EC2 qua kết nối SSH.
- **Amazon CloudWatch** – Thu thập log tập trung từ container ứng dụng và thiết lập các cảnh báo chỉ số (metric alarms) cho sức khỏe máy chủ.

#### Các bước thực hiện

1. [Cấu hình GitHub Actions CI/CD workflow](5.6.1-build-docker-CICD/)
2. [Cấu hình Amazon CloudWatch logs & metrics](5.6.2-cloudwatch/)
