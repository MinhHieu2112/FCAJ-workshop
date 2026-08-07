---
title: "Media Storage with Amazon S3"
date: 2026-08-06
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Amazon S3 Presigned URL Upload Architecture

Property listings require uploading multiple high-resolution photos. Passing raw binary image files through the NestJS backend consumes server CPU and RAM bandwidth unnecessarily.

Instead, the **Real Estate Rental Management System** implements **Amazon S3 Presigned URLs**:

```
┌──────────────┐         1. Request Presigned URL (POST /properties/upload-url)       ┌──────────────┐
│              ├──────────────────────────────────────────────────────────────────────►│              │
│   Next.js    │         2. Returns short-lived Presigned PUT URL                     │   NestJS     │
│   Client     │◄──────────────────────────────────────────────────────────────────────┤   Backend    │
│              │                                                                      └──────────────┘
│              │         3. Direct Binary PUT Upload (image/jpeg)
│              ├──────────────────────────────────────────────────────────────────────┐
│              │                                                                      ▼
│              │         4. Returns 200 OK                                    ┌──────────────┐
│              │◄─────────────────────────────────────────────────────────────┤  Amazon S3   │
│              │                                                              │  Bucket      │
└──────────────┘                                                              └──────────────┘
```

#### Benefits of Presigned URLs
+ **Zero Backend Bottlenecks**: Transfers execute directly between browser client and S3.
+ **Security**: Upload URLs expire automatically in 15 minutes.
+ **Public Read Access**: Uploaded property images are served publicly via standard HTTPS S3 URLs.

#### Module Steps

1. [Set up Amazon S3 Bucket & CORS Configuration](5.4.1-s3-bucket-setup/)
2. [Implement Presigned Uploads in NestJS](5.4.2-presigned-urls/)
