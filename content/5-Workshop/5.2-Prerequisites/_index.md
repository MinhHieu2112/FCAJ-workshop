---
title: "Prerequisites & Environment Setup"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Local Development Tools Required

Before starting the workshop, ensure the following software tools are installed on your workstation:

- **Node.js**: `v18.x` or `v20.x` (LTS)
- **pnpm**: `v8.x` or higher (`npm install -g pnpm`)
- **AWS CLI**: `v2.x` configured with `aws configure`
- **PostgreSQL Client**: `psql` or DBeaver (optional for database inspection)

#### IAM Permissions Policy

To configure Amazon Cognito, S3 Buckets, and Amazon Location Service during this lab, attach the following custom IAM Policy to your AWS User / IAM Role:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CognitoManagement",
            "Effect": "Allow",
            "Action": [
                "cognito-idp:CreateUserPool",
                "cognito-idp:DeleteUserPool",
                "cognito-idp:UpdateUserPool",
                "cognito-idp:DescribeUserPool",
                "cognito-idp:CreateUserPoolClient",
                "cognito-idp:DeleteUserPoolClient",
                "cognito-idp:AdminCreateUser",
                "cognito-idp:AdminDeleteUser",
                "cognito-idp:AdminSetUserPassword"
            ],
            "Resource": "*"
        },
        {
            "Sid": "S3BucketManagement",
            "Effect": "Allow",
            "Action": [
                "s3:CreateBucket",
                "s3:DeleteBucket",
                "s3:PutBucketCors",
                "s3:GetBucketCors",
                "s3:PutBucketPolicy",
                "s3:GetBucketPolicy",
                "s3:PutObject",
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::real-estate-rental-*"
        },
        {
            "Sid": "LocationServiceManagement",
            "Effect": "Allow",
            "Action": [
                "geo:CreatePlaceIndex",
                "geo:DeletePlaceIndex",
                "geo:DescribePlaceIndex",
                "geo:SearchPlaceIndexForText",
                "geo:SearchPlaceIndexForPosition"
            ],
            "Resource": "*"
        }
    ]
}
```

#### AWS CLI Verification

Verify your AWS CLI configuration and active AWS credentials:

```bash
aws sts get-caller-identity
```

#### Project Monorepo Environment Variables

Create `.env` files in both the NestJS server (`apps/server/.env`) and Next.js client (`apps/client/.env`):

```env
# Server (.env)
PORT=4000
DATABASE_URL="postgresql://postgres:password@localhost:5432/rental_db?schema=public"

AWS_REGION="us-east-1"
COGNITO_USER_POOL_ID="us-east-1_XXXXXXXXX"
COGNITO_CLIENT_ID="XXXXXXXXXXXXXXXX──────────"

S3_BUCKET_NAME="real-estate-rental-media-dev"
LOCATION_PLACE_INDEX_NAME="RentalPlaceIndex"
```
