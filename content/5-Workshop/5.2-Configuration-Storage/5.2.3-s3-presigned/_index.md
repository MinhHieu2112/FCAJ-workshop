---
title: "Set up Amazon S3 bucket & CORS"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.2.3. </b> "
---

#### Step 1: Create an S3 bucket

1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home).
2. Click **Create bucket**.
3. Under **Bucket type**, select **General purpose** (suitable for application media and image assets).
4. Under **Bucket namespace**, retain the default **Global namespace**.
5. Under **Bucket name**, enter a unique bucket name (e.g., `real-estate-rental-media-dev`).
   - *Note:* Length must be between **3 and 63 characters**, containing only **lowercase letters, numbers, dots (.), and hyphens (-)**.
![Create S3 Bucket](/images/5-Workshop/5.4-S3-Storage/5.4.1-create-bucket.png)
![General Configuration](/images/5-Workshop/5.4-S3-Storage/5.4.2-general-configuration.png)

#### Step 2: Configure Block Public Access

1. Under **Object Ownership**, select **ACLs disabled (recommended)**.
2. Under **Block Public Access settings for this bucket**:
   - Uncheck **Block *all* public access** (to allow public read access for property image URLs).
   - Check the risk acknowledgment box.
![S3 Public Access Configuration](/images/5-Workshop/5.4-S3-Storage/5.4.3-object-ownership.png)

#### Step 3: Configure versioning and encryption

1. Under **Bucket Versioning**, select **Disable** (or **Enable** if object version management is required).
2. Under **Default encryption**:
   - **Encryption type:** Select **Server-side encryption with Amazon S3 managed keys (SSE-S3)**.
   - **Bucket Key:** Select **Enable** to optimize encryption request costs.
![Bucket created](/images/5-Workshop/5.4-S3-Storage/5.4.4-bucket-created.png)

3. Under **Object Lock**, select **Disable**.
4. Review all settings and click **Create bucket** at the bottom of the page.
![Object Lock](/images/5-Workshop/5.4-S3-Storage/5.4.5-object-lock.png)

---

#### Step 4: Configure CORS (Cross-Origin Resource Sharing) policy

To enable web browser clients (e.g., Next.js at `http://localhost:3000`) to upload binary files directly to Amazon S3 using JavaScript `fetch`:

1. Select your bucket (`real-estate-rental-media-dev`).
2. Switch to the **Permissions** tab and scroll to **Cross-origin resource sharing (CORS)**.
![S3 CORS Policy Config](/images/5-Workshop/5.4-S3-Storage/5.4.6-cors-config.png)

3. Click **Edit** and paste the following JSON policy:

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

4. Click **Save changes**.
![S3 Save CORS Changes](/images/5-Workshop/5.4-S3-Storage/5.4.7-save-changes.png)

---

#### Step 5: Install AWS S3 SDK and implement presigned upload logic

In your NestJS backend codebase (`apps/server`), install `@aws-sdk/client-s3` and `@aws-sdk/s3-request-presigner`:

```bash
pnpm add @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
```

Create the storage service (`src/storage/storage.service.ts`):

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

    // Generate presigned URL valid for 15 minutes (900 seconds)
    const uploadUrl = await getSignedUrl(this.s3Client, command, { expiresIn: 900 });
    const fileUrl = `https://${bucketName}.s3.${process.env.AWS_REGION}.amazonaws.com/${fileKey}`;

    return { uploadUrl, fileUrl, fileKey };
  }
}
```

Expose the upload endpoint in your controller (`src/property/property.controller.ts`):

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

Execute direct binary upload from the Next.js frontend:

```typescript
async function handleFileUpload(file: File) {
  // 1. Request Presigned URL from NestJS backend
  const res = await fetch('/api/properties/upload-url', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${accessToken}`,
    },
    body: JSON.stringify({ fileType: file.type }),
  });
  const { uploadUrl, fileUrl } = await res.json();

  // 2. Upload binary payload directly to Amazon S3
  await fetch(uploadUrl, {
    method: 'PUT',
    headers: { 'Content-Type': file.type },
    body: file,
  });

  console.log('Image uploaded successfully to S3:', fileUrl);
}
```
