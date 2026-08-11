---
title: "Resource cleanup"
date: 2026-08-06
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Congratulations on completing the **Real Estate Rental Management System AWS Workshop**!

In this workshop, you successfully built and deployed a modern cloud architecture on AWS:
- **Networking & compute**: Created a two-tier VPC, public/private subnets, security groups, and an EC2 instance with Docker runtime.
- **Database & routing**: Deployed Amazon RDS PostgreSQL with PostGIS and configured Application Load Balancer (ALB), AWS WAF, and DuckDNS with SSL/TLS.
- **Authentication & services**: Integrated Amazon Cognito User Pool, Amazon S3 Presigned URLs, Amazon Location Service, and Amazon SES.
- **CI/CD & observability**: Configured GitHub Actions automation and CloudWatch logging & metric alarms.

---

#### Clean up AWS resources

To avoid incurring unexpected recurring charges on your AWS account, remove provisioned resources in the following order:

#### 1. Terminate EC2 instance & delete target groups / ALB

1. Open the [EC2 Console](https://console.aws.amazon.com/ec2/home).
2. Select **Instances** → select `real-estate-backend-server` → **Instance state** → **Terminate instance**.
3. Select **Load Balancers** → select `real-estate-alb` → **Actions** → **Delete load balancer**.
4. Select **Target Groups** → select `real-estate-backend-tg` → **Actions** → **Delete**.

---

#### 2. Delete AWS WAF Web ACL

1. Open the [AWS WAF Console](https://console.aws.amazon.com/wafv2/homev2).
2. Select **Web ACLs** → select `real-estate-waf` → click **Delete**.

---

#### 3. Delete Amazon RDS instance & DB subnet group

1. Open the [Amazon RDS Console](https://console.aws.amazon.com/rds/home).
2. Select **Databases** → select `real-estate-rental-db` → **Actions** → **Delete**.
3. Uncheck *Create final snapshot*, check acknowledgement, and type `delete me` to confirm.
4. Once deleted, select **Subnet groups** → select `real-estate-rds-subnet-group` → **Delete**.

---

#### 4. Delete Amazon S3 media bucket

1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home).
2. Select `real-estate-rental-media-dev`.
3. Click **Empty** and confirm deletion of all objects inside the bucket.
4. Once empty, click **Delete** and type the bucket name to permanently delete it.

![Delete S3 Bucket](/images/5-Workshop/5.6-Cleanup/5.6.1-bucket.png)

---

#### 5. Delete Amazon Cognito User Pool

1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/v2/home).
2. Select `real-estate-rental-user-pool`.
3. Click **Delete user pool** and type `delete` to confirm deletion.

![Delete Cognito User Pool](/images/5-Workshop/5.6-Cleanup/5.6.2-aws-cognito.png)

---

#### 6. Delete Amazon Location Service API Key

1. Open the [Amazon Location Service Console](https://console.aws.amazon.com/location/home).
2. Navigate to **API keys** on the left menu.
3. Click **Deactivate** to disable the API Key, then click **Delete** to remove it permanently.

![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location.png)
![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete.png)
![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete-1.png)

---

#### 7. Delete VPC & security groups

1. Open the [Amazon VPC Console](https://console.aws.amazon.com/vpc/home).
2. Select **Your VPCs** → select `real-estate-vpc` → **Actions** → **Delete VPC**.
3. Confirm deletion to auto-delete associated subnets, route tables, internet gateways, and security groups.

---

#### 8. Delete IAM roles & policies

1. Open the [IAM Console](https://console.aws.amazon.com/iam/home#/roles).
2. Select `RealEstateEC2Role` → **Delete**.

![Delete IAM User](/images/5-Workshop/5.6-Cleanup/5.6.4-aws-iam.png)
