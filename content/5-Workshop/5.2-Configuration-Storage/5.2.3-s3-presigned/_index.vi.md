---
title: "Khởi tạo Amazon S3 Bucket & cấu hình CORS"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.2.3. </b> "
---

#### Bước 1: Tạo S3 bucket

1. Truy cập [bảng điều khiển Amazon S3](https://s3.console.aws.amazon.com/s3/home).
2. Nhấn nút **Create bucket**.
3. Tại mục **Bucket type**, chọn **General purpose** (loại bucket phổ biến phù hợp với hầu hết các trường hợp sử dụng và lưu trữ hình ảnh/dữ liệu ứng dụng).
4. Tại mục **Bucket namespace**, giữ mặc định **Global namespace** (đảm bảo tên bucket duy nhất trên toàn hệ thống Amazon S3).
5. Tại mục **Bucket name**, nhập tên bucket duy nhất của bạn (ví dụ: `real-estate-rental-media-dev`).
   - *Lưu ý:* Độ dài từ **3 đến 63 ký tự**, chỉ chứa **chữ cái viết thường, chữ số, dấu chấm (.) và dấu gạch ngang (-)**.
![Create S3 Bucket](/images/5-Workshop/5.4-S3-Storage/5.4.1-create-bucket.png)
![General Configuration](/images/5-Workshop/5.4-S3-Storage/5.4.2-general-configuration.png)

#### Bước 2: Cấu hình Block Public Access

1. Tại mục **Object Ownership**, chọn **ACLs disabled (recommended)**.
2. Tại mục **Block Public Access settings for this bucket**:
   - Bỏ chọn **Block *all* public access** (để cho phép đọc công khai hình ảnh bất động sản).
   - Tích chọn ô xác nhận nhận thức rủi ro.
![S3 Public Access Configuration](/images/5-Workshop/5.4-S3-Storage/5.4.3-object-ownership.png)

#### Bước 3: Cấu hình Versioning và mã hóa

1. Tại mục **Bucket Versioning**, chọn **Disable** (hoặc **Enable** nếu muốn quản lý phiên bản lưu trữ cũ).
2. Tại mục **Default encryption**:
   - **Encryption type:** Chọn **Server-side encryption with Amazon S3 managed keys (SSE-S3)**.
   - **Bucket Key:** Chọn **Enable** để tối ưu chi phí mã hóa.
![Bucket created](/images/5-Workshop/5.4-S3-Storage/5.4.4-bucket-created.png)

3. Tại mục **Object Lock**, chọn **Disable**.
4. Kiểm tra lại cấu hình và nhấn nút **Create bucket** ở cuối trang để hoàn tất.
![Object Lock](/images/5-Workshop/5.4-S3-Storage/5.4.5-object-lock.png)

---

#### Bước 4: Cấu hình chính sách CORS (Cross-Origin Resource Sharing)

Để trình duyệt client Next.js (`http://localhost:3000`) có thể gửi file ảnh trực tiếp lên S3 qua lệnh `fetch` của JavaScript:

1. Chọn bucket vừa tạo (`real-estate-rental-media-dev`).
2. Chuyển sang tab **Permissions**, cuộn xuống mục **Cross-origin resource sharing (CORS)**.
![S3 CORS Policy Config](/images/5-Workshop/5.4-S3-Storage/5.4.6-cors-config.png)

3. Nhấn **Edit** và dán đoạn cấu hình JSON sau:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "HEAD", "PUT", "POST"],
    "AllowedOrigins": ["*"],
    "ExposeHeaders": []
  }
]
```

4. Nhấn **Save changes**.
![S3 Save CORS Changes](/images/5-Workshop/5.4-S3-Storage/5.4.7-save-changes.png)

---

#### Bước 5: Cài đặt và tích hợp S3 SDK trong NestJS

Tại thư mục backend NestJS (`apps/server`), cài đặt gói `@aws-sdk/client-s3` và `@aws-sdk/s3-request-presigner`:

```bash
pnpm add @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
```

Tạo service storage (`src/storage/storage.service.ts`):

```typescript
import { Injectable } from '@nestjs/common';
import { S3Client, PutObjectCommand } from '@aws-sdk/client-s3';
import { getSignedUrl } from '@aws-sdk/s3-request-presigner';
import { v4 as uuidv4 } from 'uuid';

@Injectable()
export class StorageService {
  private s3Client = new S3Client({
    region: process.env.AWS_REGION || 'us-east-1',
  });

  async generatePresignedUploadUrl(fileType: string): Promise<{ uploadUrl: string; fileUrl: string; fileKey: string }> {
    const fileExtension = fileType.split('/')[1] || 'jpg';
    const fileKey = `properties/${uuidv4()}.${fileExtension}`;
    const bucketName = process.env.S3_BUCKET_NAME!;

    const command = new PutObjectCommand({
      Bucket: bucketName,
      Key: fileKey,
      ContentType: fileType,
    });

    // Tạo URL ký sẵn có hiệu lực trong 15 phút (900 giây)
    const uploadUrl = await getSignedUrl(this.s3Client, command, { expiresIn: 900 });
    const fileUrl = `https://${bucketName}.s3.${process.env.AWS_REGION}.amazonaws.com/${fileKey}`;

    return { uploadUrl, fileUrl, fileKey };
  }
}
```

Khai báo endpoint trong controller (`src/property/property.controller.ts`):

```typescript
@Controller('properties')
export class PropertyController {
  constructor(private readonly storageService: StorageService) {}

  @Post('upload-url')
  @UseGuards(AuthGuard, RolesGuard)
  @Roles('MANAGER')
  async getUploadUrl(@Body() body: { fileType: string }) {
    return this.storageService.generatePresignedUploadUrl(body.fileType);
  }
}
```

Thực hiện upload trực tiếp từ frontend Next.js:

```typescript
async function handleFileUpload(file: File) {
  // 1. Lấy Presigned URL từ backend NestJS
  const res = await fetch('/api/properties/upload-url', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${accessToken}`,
    },
    body: JSON.stringify({ fileType: file.type }),
  });
  const { uploadUrl, fileUrl } = await res.json();

  // 2. Upload file nhị phân trực tiếp lên Amazon S3
  await fetch(uploadUrl, {
    method: 'PUT',
    headers: { 'Content-Type': file.type },
    body: file,
  });

  console.log('Tải ảnh lên S3 thành công:', fileUrl);
}
```
