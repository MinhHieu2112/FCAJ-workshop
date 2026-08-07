---
title: "Implement Presigned Uploads in NestJS"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Step 1. Install AWS S3 SDK Packages

Inside your NestJS backend directory (`apps/server`), install `@aws-sdk/client-s3` and `@aws-sdk/s3-request-presigner`:

```bash
pnpm add @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
```

#### Step 2. Create S3 Storage Service (`src/storage/storage.service.ts`)

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

    // Generate signed URL valid for 15 minutes (900 seconds)
    const uploadUrl = await getSignedUrl(this.s3Client, command, { expiresIn: 900 });
    const fileUrl = `https://${bucketName}.s3.${process.env.AWS_REGION}.amazonaws.com/${fileKey}`;

    return { uploadUrl, fileUrl, fileKey };
  }
}
```

#### Step 3. Expose Upload Endpoint in Controller (`src/property/property.controller.ts`)

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

#### Step 4. Uploading Binary File from Next.js Client

```typescript
// Next.js client upload handler
async function handleFileUpload(file: File) {
  // 1. Get Presigned URL from NestJS backend
  const res = await fetch('/api/properties/upload-url', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${accessToken}`,
    },
    body: JSON.stringify({ fileType: file.type }),
  });
  const { uploadUrl, fileUrl } = await res.json();

  // 2. Direct binary upload to Amazon S3
  await fetch(uploadUrl, {
    method: 'PUT',
    headers: { 'Content-Type': file.type },
    body: file,
  });

  console.log('Image uploaded successfully to S3:', fileUrl);
}
```
