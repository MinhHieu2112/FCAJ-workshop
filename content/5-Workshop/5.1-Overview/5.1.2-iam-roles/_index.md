---
title: "IAM role configuration for EC2 & services"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.1.2. </b> "
---

#### IAM role design

The EC2 instance running the NestJS backend needs permissions to call AWS services (S3, SES, Amazon Location Service, CloudWatch) without embedding long-term credentials in environment variables.

Create an **EC2 instance profile** backed by the following IAM role:

**Role name:** `RealEstateEC2Role`  
**Trusted entity:** `ec2.amazonaws.com`

#### Required IAM permissions policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "S3MediaAccess",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:GeneratePresignedUrl"
      ],
      "Resource": "arn:aws:s3:::real-estate-rental-*/*"
    },
    {
      "Sid": "SESEmailSend",
      "Effect": "Allow",
      "Action": ["ses:SendEmail", "ses:SendRawEmail"],
      "Resource": "*"
    },
    {
      "Sid": "LocationServiceAccess",
      "Effect": "Allow",
      "Action": [
        "geo:SearchPlaceIndexForText",
        "geo:SearchPlaceIndexForPosition"
      ],
      "Resource": "*"
    },
    {
      "Sid": "CloudWatchLogsAccess",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "cloudwatch:PutMetricData"
      ],
      "Resource": "*"
    }
  ]
}
```

#### Steps to create and attach

1. Open the [IAM Console](https://console.aws.amazon.com/iam/home) → **Roles** → **Create role**.
2. Select **AWS service** → **EC2** as the trusted entity.
3. Attach the JSON policy above as an **inline policy** named `RealEstateEC2Policy`.
4. Name the role `RealEstateEC2Role` and click **Create role**.
5. When launching or editing your EC2 instance, under **Advanced details** → **IAM instance profile**, select `RealEstateEC2Role`.

{{% notice tip %}}
Using an instance profile instead of hardcoded AWS keys follows the AWS security best practice of **least privilege with temporary credentials**. Credentials rotate automatically via the EC2 metadata service.
{{% /notice %}}
