---
title: "Phân quyền IAM role cho EC2 & services"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.1.2. </b> "
---

#### Thiết kế IAM role

EC2 instance chạy NestJS backend cần quyền gọi các dịch vụ AWS (S3, SES, Amazon Location Service, CloudWatch) mà không cần nhúng thông tin xác thực dài hạn vào biến môi trường.

Tạo một **EC2 instance profile** được hỗ trợ bởi IAM role sau:

**Tên role:** `RealEstateEC2Role`  
**Trusted entity:** `ec2.amazonaws.com`

#### Chính sách IAM cần thiết

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

#### Các bước tạo và gắn role

1. Truy cập [Bảng điều khiển IAM](https://console.aws.amazon.com/iam/home) → **Roles** → **Create role**.
2. Chọn **AWS service** → **EC2** làm trusted entity.
3. Đính kèm chính sách JSON ở trên dưới dạng **inline policy** với tên `RealEstateEC2Policy`.
4. Đặt tên role là `RealEstateEC2Role` và nhấn **Create role**.
5. Khi khởi chạy hoặc chỉnh sửa EC2 instance, tại mục **Advanced details** → **IAM instance profile**, chọn `RealEstateEC2Role`.

{{% notice tip %}}
Sử dụng instance profile thay vì AWS key tĩnh tuân thủ nguyên tắc bảo mật **least privilege with temporary credentials** của AWS. Thông tin xác thực được tự động luân chuyển qua dịch vụ EC2 metadata.
{{% /notice %}}
