---
title: "Tích hợp Presigned Upload URL trong NestJS"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Bước 1. Cài đặt thư viện AWS S3 SDK

Tại thư mục backend NestJS (`apps/server`), cài đặt gói `@aws-sdk/client-s3` và `@aws-sdk/s3-request-presigner`:

```bash
pnpm add @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
```

#### Bước 2. Xây dựng service storage (`src/storage/storage.service.ts`)

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

#### Bước 3. Khai báo endpoint trong controller (`src/property/property.controller.ts`)

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

#### Bước 4. Thực hiện upload nhị phân từ frontend Next.js

```typescript
// Xử lý upload ở client Next.js
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
