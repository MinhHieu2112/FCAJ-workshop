---
title: "Blog 2"
date: 2026-08-03
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# IMAGE MANAGEMENT WITH AWS S3

In a real estate rental management system, each property may contain multiple high-resolution images (`photoUrls`) to provide detailed information for potential tenants. Storing these files directly on the application server (NestJS) can quickly consume storage resources and increase I/O overhead when handling concurrent requests. To address these challenges, the project uses **Amazon S3** as the primary image storage service.

To improve both performance and security, the image upload workflow was designed as follows:

- Use **Amazon S3 Presigned URLs** to allow the Frontend (Next.js) to upload images directly to Amazon S3. The Backend generates a short-lived signed URL, eliminating the need for image data to pass through the NestJS server and significantly reducing CPU and memory usage.
- Manage S3 access permissions through **AWS IAM**, granting the Backend only the minimum permissions required to generate signed upload URLs (`PutObject`).
- Store the image URL in the PostgreSQL database (via Prisma) only after the upload has been successfully completed.
- Integrate the **Next.js Image (`<Image />`)** component with the `remotePatterns` configuration in `next.config.js` to enable automatic image optimization, lazy loading, and modern image formats such as WebP and AVIF.
- Provide placeholder images to ensure a consistent user experience when an image is unavailable or cannot be loaded.

During implementation, the team encountered **403 Forbidden** errors caused by incorrect S3 Bucket Policy and CORS configuration. After updating the bucket's `AllowedOrigins` and `AllowedHeaders` settings and reviewing the IAM policies, the system was able to upload and display images reliably with improved performance.

## Architecture Overview

![Overview](/images/3-BlogsPosted/S3_Image_Architecture.png)

## References

- https://docs.aws.amazon.com/s3/
- https://docs.aws.amazon.com/IAM/
- https://nextjs.org/docs/app/api-reference/components/image