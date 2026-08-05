---
title: "Week 2 Worklog"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Study AWS Compute and Storage services in depth.
* Study AWS Database, Networking, and IAM.
* Practice using core AWS services via Console and CLI.
* Complete AWS Academy / AWS Skill Builder assignments.
* Consolidate knowledge and check in with mentor at end of week.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Study AWS Compute: <br>&emsp; + **EC2**: Instance types (General Purpose, Compute Optimized, Memory Optimized), AMI, User Data, Placement Groups <br>&emsp; + **Auto Scaling Group (ASG)**: scaling policies, health checks <br>&emsp; + **Elastic Load Balancer (ELB)**: ALB vs NLB <br>&emsp; + **AWS Lambda**: serverless functions, event triggers, execution environment | 29/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| Tue | - Study AWS Storage and Database: <br>&emsp; + **S3**: bucket policies, versioning, lifecycle rules, presigned URLs <br>&emsp; + **EBS**: volume types (gp3, io2), snapshots <br>&emsp; + **RDS**: Multi-AZ, Read Replicas, backups <br>&emsp; + **DynamoDB**: partition key, sort key, GSI <br> - Study AWS Networking: <br>&emsp; + **VPC**: public/private subnets, Internet Gateway, NAT Gateway <br>&emsp; + **Security Group vs NACL** <br>&emsp; + **Route Tables** | 30/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| Wed | - Deep dive into AWS IAM: <br>&emsp; + Users, Groups, Roles, Policies <br>&emsp; + Policy evaluation logic (Allow/Deny) <br>&emsp; + IAM best practices: least privilege, MFA, access key rotation <br> - Practice: create IAM Users, assign policies, create IAM Roles for EC2 <br> - Practice: create VPC with public/private subnets, configure Security Groups | 01/07/2026 | <https://docs.aws.amazon.com/IAM/> |
| Thu | - Practice core AWS services: <br>&emsp; + Launch EC2 instance, SSH connection, install Nginx <br>&emsp; + Create S3 bucket, upload files, configure public access <br>&emsp; + Create RDS instance (MySQL), connect from EC2 <br> - Complete lab assignments in AWS Academy / Skill Builder | 02/07/2026 | AWS Academy / Skill Builder |
| Fri | - Consolidate Week 2 knowledge into personal notes <br> - Check in with mentor on learning progress and plan for next week <br> - Read additional documentation on AWS Well-Architected Framework | 03/07/2026 | <https://aws.amazon.com/architecture/well-architected/> |

---

### Week 2 Achievements:

* Mastered AWS **Compute** services:
  * EC2 with various instance types, AMIs, User Data, and SSH access
  * Auto Scaling Group with scale-out/scale-in policies
  * Elastic Load Balancer (ALB) and traffic distribution mechanisms
  * AWS Lambda and the serverless event-driven model

* Understood and practiced **Storage & Database** services:
  * S3 with bucket policies, versioning, and lifecycle rules
  * EBS snapshots and volume type selection
  * RDS with Multi-AZ failover and Read Replica configurations
  * DynamoDB with key-value model and Global Secondary Index

* Understood **AWS Networking** architecture:
  * Designed VPCs with public/private subnets
  * Configured Security Groups (stateful) and NACLs (stateless)
  * Distinguished between Internet Gateway and NAT Gateway

* Mastered **IAM**: created users, groups, roles, and policies following the principle of least privilege.

* Completed AWS Academy lab assignments and accumulated additional learning credits.

---

### Knowledge / Experience Gained:

* Understood the critical difference between Security Groups (stateful, instance-level) and NACLs (stateless, subnet-level).
* Internalized the principle of least privilege in IAM — only granting the minimum permissions necessary for each entity.
* Learned how to read and apply the AWS Well-Architected Framework as a system design guideline.
* Hands-on practice reinforced theoretical knowledge far more effectively than reading documentation alone.
