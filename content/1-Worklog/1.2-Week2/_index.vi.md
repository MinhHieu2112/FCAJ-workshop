---
title: "Worklog Tuần 2"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Tìm hiểu chuyên sâu các dịch vụ AWS về Compute và Storage.
* Tìm hiểu Database, Networking và IAM trên AWS.
* Thực hành sử dụng các dịch vụ AWS cơ bản trên Console và CLI.
* Hoàn thành các nhiệm vụ AWS Academy / AWS Skill Builder.
* Tổng hợp kiến thức và trao đổi với mentor cuối tuần.

---

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày | Nguồn tài liệu |
|-----|-----------|------|----------------|
| 2 | - Tìm hiểu AWS Compute: <br>&emsp; + **EC2**: Instance types (General Purpose, Compute Optimized, Memory Optimized), AMI, User Data, Placement Groups <br>&emsp; + **Auto Scaling Group (ASG)**: scaling policy, health check <br>&emsp; + **Elastic Load Balancer (ELB)**: ALB vs NLB <br>&emsp; + **AWS Lambda**: serverless function, event triggers, execution environment | 29/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Tìm hiểu AWS Storage và Database: <br>&emsp; + **S3**: bucket policy, versioning, lifecycle rule, presigned URL <br>&emsp; + **EBS**: volume types (gp3, io2), snapshot <br>&emsp; + **RDS**: Multi-AZ, Read Replica, backup <br>&emsp; + **DynamoDB**: partition key, sort key, GSI <br> - Tìm hiểu AWS Networking: <br>&emsp; + **VPC**: subnet (public/private), Internet Gateway, NAT Gateway <br>&emsp; + **Security Group vs NACL** <br>&emsp; + **Route Table** | 30/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tìm hiểu AWS IAM chuyên sâu: <br>&emsp; + Users, Groups, Roles, Policies <br>&emsp; + Policy evaluation logic (Allow/Deny) <br>&emsp; + IAM Best Practices: least privilege, MFA, access key rotation <br> - Thực hành tạo IAM User, gán policy, tạo IAM Role cho EC2 <br> - Thực hành tạo VPC với public/private subnet, cấu hình Security Group | 01/07/2026 | <https://docs.aws.amazon.com/IAM/> |
| 5 | - Thực hành các dịch vụ AWS cơ bản: <br>&emsp; + Launch EC2 instance, kết nối SSH, cài đặt Nginx <br>&emsp; + Tạo S3 bucket, upload file, cấu hình public access <br>&emsp; + Tạo RDS instance (MySQL), kết nối từ EC2 <br> - Hoàn thành các bài lab trong AWS Academy / Skill Builder | 02/07/2026 | AWS Academy / Skill Builder |
| 6 | - Tổng hợp kiến thức tuần 2 vào notes cá nhân <br> - Trao đổi với mentor về tiến độ học và định hướng tuần tiếp theo <br> - Đọc thêm tài liệu về kiến trúc Well-Architected Framework của AWS | 03/07/2026 | <https://aws.amazon.com/architecture/well-architected/> |

---

### Kết quả đạt được tuần 2:

* Nắm vững các dịch vụ **Compute** của AWS:
  * EC2 với các loại instance, AMI, User Data và cách kết nối SSH
  * Auto Scaling Group với chính sách scale-out/scale-in
  * Elastic Load Balancer (ALB) và cơ chế phân tải traffic
  * AWS Lambda và mô hình serverless event-driven

* Hiểu và thực hành được các dịch vụ **Storage & Database**:
  * S3 với bucket policy, versioning, lifecycle rules
  * EBS snapshot và volume type selection
  * RDS với cơ chế Multi-AZ failover và Read Replica
  * DynamoDB với mô hình key-value và Global Secondary Index

* Hiểu kiến trúc **Networking AWS**:
  * Thiết kế VPC với public/private subnet
  * Cấu hình Security Group (stateful) và NACL (stateless)
  * Phân biệt Internet Gateway và NAT Gateway

* Nắm vững **IAM**: tạo user, group, role, policy theo nguyên tắc least privilege.

* Hoàn thành các bài lab AWS Academy và tích lũy thêm credits học tập.

---

### Kiến thức / Kinh nghiệm học được:

* Hiểu sự khác biệt quan trọng giữa Security Group (stateful, áp dụng cho instance) và NACL (stateless, áp dụng cho subnet).
* Nắm được nguyên tắc least privilege trong IAM — chỉ cấp quyền tối thiểu cần thiết cho từng entity.
* Học được cách đọc và áp dụng AWS Well-Architected Framework như một bộ guideline thiết kế hệ thống.
* Thực hành thực tế giúp củng cố kiến thức lý thuyết nhanh hơn nhiều so với chỉ đọc tài liệu.
