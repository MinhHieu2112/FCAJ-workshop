---
title: "Cấu hình IAM Role cho EC2"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.1.2. </b> "
---

### 1. Tổng quan

Trong sơ đồ kiến trúc hệ thống, máy chủ **EC2 Backend** cần tương tác với nhiều dịch vụ AWS khác như **Amazon S3** (lưu trữ file), **Amazon SES** (gửi email), hay **Amazon CloudWatch** (đẩy logs).

**Phương pháp truyền thống (Không khuyến nghị):**
Hardcode chuỗi `AWS_ACCESS_KEY_ID` và `AWS_SECRET_ACCESS_KEY` trực tiếp vào mã nguồn hoặc file `.env`. Cách làm này nguy hiểm vì dễ làm rò rỉ credential nếu dự án bị lộ mã nguồn.

**Giải pháp tối ưu (Sử dụng IAM Role):**
Gán một **IAM Role** trực tiếp cho EC2 Instance. Khi đó:
- AWS tự động cấp phát credential tạm thời và xoay vòng (rotate) định kỳ.
- AWS SDK trong ứng dụng sẽ tự động phát hiện và sử dụng credential này mà **không cần lưu Access Key/Secret Key** trong file môi trường.

---

### 2. Các bước triển khai thực tế

#### Bước 1: Khởi tạo IAM Role cho EC2

1. Truy cập [bảng điều khiển AWS IAM Console](https://console.aws.amazon.com/iam/).
2. Tại thanh menu bên trái, chọn **Roles** và click nút **Create role**.
3. **Select trusted entity (Chọn thực thể tin cậy):**
   - **Trusted entity type**: Chọn **AWS service**.
   - **Use case**: Chọn **EC2** và nhấn **Next**.
![IAM entity selection](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-1.png)

4. **Add permissions (Gán chính sách quyền):**
   - Tìm kiếm và tích chọn các Policy cần thiết cho ứng dụng: `SecretManagerReadWrite`
   - Nhấn **Next**.
![IAM permissions setup](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-2.png)

5. **Name, review, and create:**
   - **Role name**: `RentifulEC2SecretManagerRole`
   - **Description**: `IAM Role cấp quyền cho EC2 Backend truy cập Secret Manager`
6. Nhấn **Create role** để hoàn tất.
![IAM role created 1](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-3.png)
![IAM role created 2](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-4.png)

---

#### Bước 2: Gán IAM Role vào EC2 Instance

1. Truy cập [bảng điều khiển Amazon EC2 Console](https://console.aws.amazon.com/ec2/).
2. Chọn **Instances** và tích chọn máy chủ **EC2 Backend**.
3. Tại menu hành động (góc trên bên phải), chọn **Actions** -> **Security** -> **Modify IAM role**.
4. Tại mục **IAM role**, chọn Role vừa tạo: `EC2-Backend-Services-Role`.
5. Nhấn **Update IAM role**.
![Modify IAM role 1](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/Setting-EC2-1.png)
![Modify IAM role 2](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/Setting-EC2-2.png)

---

#### Bước 3: Cấu hình mã nguồn

Khi EC2 đã được gán IAM Role, bạn chỉ cần cấu hình AWS SDK khởi tạo với `region` mà không truyền Access Key/Secret Key.

**Ví dụ cấu hình AWS SDK v3 (Node.js/TypeScript):**

```typescript
import { S3Client } from "@aws-sdk/client-s3";
import { SESClient } from "@aws-sdk/client-ses";

// AWS SDK sẽ TỰ ĐỘNG lấy credential từ IAM Role của EC2
export const s3Client = new S3Client({ 
  region: process.env.AWS_REGION!
});

export const sesClient = new SESClient({ 
  region: process.env.AWS_REGION!
});
```