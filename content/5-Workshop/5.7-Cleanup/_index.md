---
title: "Resource cleanup"
date: 2026-08-06
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Congratulations on completing the **Real Estate Rental Management System AWS Workshop**!

In this workshop, you successfully deployed an authentic production web architecture:
- **Frontend Vercel (HTTPS)** – Securely connected to the backend API via your DuckDNS domain.
- **Networking & compute**: Provisioned a two-tier VPC, public/private subnets, static Elastic IP, Security Groups opening ports 80/443, and an EC2 instance running Docker runtime with Caddy HTTPS Reverse Proxy.
- **Database & security**: Deployed Amazon RDS PostgreSQL (PostGIS) in a private subnet and managed secrets via AWS Secrets Manager.
- **Integrated services**: Integrated Amazon Cognito User Pool, Amazon S3 Presigned URLs, Amazon Location Service, and SMTP email service.
- **CI/CD & observability**: Configured GitHub Actions workflow automation and CloudWatch log & metric alarms.

---

#### Clean up AWS resources

To prevent unexpected charges on your AWS account, clean up provisioned resources in the following order:

#### 1. Release Elastic IP & terminate EC2 instance

1. Access the [Amazon EC2 console](https://console.aws.amazon.com/ec2/home).
2. Select **Instances** -> select your backend server -> **Instance state** -> **Terminate instance**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.5-aws-ec2.png)
3. Select **Elastic IPs** -> select the Elastic IP associated with EC2 -> **Actions** -> **Disassociate Elastic IP address**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.6-aws-ip-elastic.png)
4. Click **Actions** -> **Release Elastic IP addresses** to return the IP to the AWS pool.

---

#### 2. Delete Amazon RDS instance & DB subnet group

1. Access the [Amazon RDS console](https://console.aws.amazon.com/rds/home).
2. Select **Databases** -> select `real-estate-rental-db` -> **Actions** -> **Delete**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.7-aws-rds-1.png)
3. Uncheck *Create final snapshot*, check the acknowledgment box, and enter `delete me` to confirm.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.7-aws-rds-2.png)
4. Select **Subnet groups** -> select `real-estate-rds-subnet-group` -> **Delete**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.7-aws-subnet-group.png)

---

#### 3. Delete AWS Secrets Manager secret

1. Access the [AWS Secrets Manager console](https://console.aws.amazon.com/secretsmanager/).
2. Select `rentiful/production/env` -> **Actions** -> **Delete secret**.
![Clean-up-EC2](/images/5-Workshop/5.6-Cleanup/5.6.8-aws-secret-manager.png)
3. Select the waiting period or confirm immediate deletion.

---

#### 4. Delete Amazon S3 media bucket

1. Access the [Amazon S3 console](https://s3.console.aws.amazon.com/s3/home).
2. Select bucket `real-estate-rental-media-dev`.
3. Click **Empty** and confirm deletion of all files inside.
4. Click **Delete** and enter the bucket name to permanently delete it.

![Delete S3 Bucket](/images/5-Workshop/5.6-Cleanup/5.6.1-bucket.png)

---

#### 5. Delete Amazon Cognito User Pool

1. Access the [Amazon Cognito console](https://console.aws.amazon.com/cognito/v2/home).
2. Select `real-estate-rental-user-pool`.
3. Click **Delete user pool** and enter `delete` to confirm.

![Delete Cognito User Pool](/images/5-Workshop/5.6-Cleanup/5.6.2-aws-cognito.png)

---

#### 6. Delete Amazon Location Service API key

1. Access the [Amazon Location Service console](https://console.aws.amazon.com/location/home).
2. Select **API keys** in the left menu.
3. Click **Deactivate** to disable the API key, then select **Delete** to permanently remove it.

![Delete Location Key 1](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location.png)
![Delete Location Key 2](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete.png)
![Delete Location Key 3](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete-1.png)

---

#### 7. Delete VPC & security groups

1. Access the [Amazon VPC console](https://console.aws.amazon.com/vpc/home).
2. Select **Your VPCs** -> select `my-app-vpc` -> **Actions** -> **Delete VPC**.
3. Confirm deletion to automatically remove subnets, route tables, internet gateways, and security groups (`sg-ec2-backend`, `sg-rds-private`).
![Delete VPC](/images/5-Workshop/5.6-Cleanup/5.6.9-aws-vpc-group.png)
---

#### 8. Delete IAM roles & policies

1. Access the [IAM console](https://console.aws.amazon.com/iam/home#/roles).
2. Select `RentifulEC2SecretManagerRole` -> **Delete**.

![Delete IAM Role](/images/5-Workshop/5.6-Cleanup/5.6.4-aws-iam.png)
