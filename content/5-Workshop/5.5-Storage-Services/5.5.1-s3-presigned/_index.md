---
title: "Set up Amazon S3 Bucket & CORS"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### Step 1: Create bucket

1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home).
2. Click **Create bucket**.
3. Under **Bucket type**, select **General purpose** (common bucket type suitable for most application image/data storage use cases).
4. Under **Bucket namespace**, keep default **Global namespace** (ensuring the bucket name is unique across Amazon S3 globally).
5. Under **Bucket name**, enter your unique bucket name (e.g., `real-estate-saas-media-bucket`).
   * *Note:* Length between **3 and 63 characters**, contains only **lowercase letters, numbers, dots (.), and hyphens (-)**.

![Create S3 Bucket](/images/5-Workshop/5.4-S3-Storage/5.4.1-create-bucket.png)
![Create S3 Bucket](/images/5-Workshop/5.4-S3-Storage/5.4.2-general-configuration.png)

#### Step 2: Configure Block Public Access

1. Under **Object Ownership**, select **ACLs disabled (recommended)**.
2. Under **Block Public Access settings for this bucket**:
   - Uncheck **Block *all* public access** (to allow public read access for property images).
   - Check the risk acknowledgement box.

![S3 Public Access Configuration](/images/5-Workshop/5.4-S3-Storage/5.4.3-object-ownership.png)

#### Step 3: Configure Versioning
1. Under **Bucket Versioning**, select **Disable** (or select **Enable** if you want to manage file versioning and restore older object versions).
2. *(Optional)* Under **Tags - optional**, click **Add new tag** to add Key-Value tags for classification, cost tracking, or permissions.
3. Under **Default encryption**:
    * **Encryption type:** Select **Server-side encryption with Amazon S3 managed keys (SSE-S3)** to use S3's default free server-side encryption.
    * **Bucket Key:** Keep default **Enable** to optimize and reduce KMS encryption call costs when applicable.

![Create S3 Bucket](/images/5-Workshop/5.4-S3-Storage/5.4.4-bucket-created.png)

4. Under **Object Lock**, select **Disable** (keep default unless Write-Once-Read-Many / WORM compliance is required).
   * *Note:* Only select **Enable** if you want to strictly prevent deletion or overwriting of files for a fixed retention period (requires *Bucket Versioning* enabled and cannot be disabled after activation).
5. Review all selected settings and click **Create bucket** at the bottom of the page to complete initialization.

![Create S3 Bucket](/images/5-Workshop/5.4-S3-Storage/5.4.5-object-lock.png)

---

#### Step 4: Configure CORS Policy (Cross-Origin Resource Sharing)

To allow Next.js web clients (`http://localhost:3000`) to upload image files directly to S3 via browser JavaScript `fetch`:

1. Select your created bucket (`real-estate-rental-media-dev`).
2. Switch to the **Permissions** tab and scroll down to **Cross-origin resource sharing (CORS)**.

![S3 CORS Policy Config](/images/5-Workshop/5.4-S3-Storage/5.4.6-cors-config.png)

3. Click **Edit** and paste the following JSON policy:

```json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

4. Click **Save changes**.

![S3 CORS Policy Config](/images/5-Workshop/5.4-S3-Storage/5.4.7-save-changes.png)

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
