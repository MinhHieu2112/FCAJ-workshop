---
title: "Chuẩn bị môi trường & quyền IAM"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Các công cụ phát triển cần thiết

Trước khi bắt đầu bài thực hành, hãy đảm bảo các công cụ sau đã được cài đặt trên máy tính của bạn:

- **Node.js**: Phiên bản `v18.x` hoặc `v20.x` (LTS)
- **pnpm**: Phiên bản `v8.x` trở lên (`npm install -g pnpm`)
- **AWS CLI**: Phiên bản `v2.x` đã được cấu hình tài khoản qua lệnh `aws configure`
- **PostgreSQL Client**: `psql` hoặc DBeaver (tùy chọn để kiểm tra CSDL)

#### Chính sách phân quyền IAM (IAM Policy)

Để tạo và quản lý Amazon Cognito, S3 Bucket và Amazon Location Service trong bài lab này, hãy đính kèm policy JSON sau vào tài khoản IAM / IAM Role của bạn:

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

#### Kiểm tra cấu hình AWS CLI

Kiểm tra thông tin tài khoản AWS hiện tại trên terminal:

```bash
aws sts get-caller-identity
```

#### Cấu hình biến môi trường (Environment Variables)

Khởi tạo các file `.env` cho dự án server NestJS (`apps/server/.env`) và client Next.js (`apps/client/.env`):

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
