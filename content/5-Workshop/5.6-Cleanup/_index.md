---
title: "Resource Cleanup"
date: 2026-08-06
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Congratulations on completing the **Real Estate Rental Management System AWS Workshop**!

In this workshop, you successfully integrated enterprise AWS cloud services with a modern NestJS and Next.js monorepo:
- **Amazon Cognito User Pool**: Built user authentication with JWT tokens and role authorization (`TENANT` / `MANAGER`).
- **Amazon S3**: Implemented secure direct binary file uploads via Presigned URLs.
- **Amazon Location Service**: Integrated address geocoding and PostGIS spatial storage.

---

#### Clean Up AWS Resources

To avoid incurring unexpected charges on your AWS account, clean up provisioned resources in the following order:

#### 1. Delete Amazon S3 Media Bucket

1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home).
2. Select `real-estate-rental-media-dev`.
3. Click **Empty** and confirm deletion of all objects inside the bucket.
4. Once empty, click **Delete** and type the bucket name to permanently delete it.

![Delete S3 Bucket](/images/5-Workshop/5.6-Cleanup/5.6.1-bucket.png)

---

#### 2. Delete Amazon Cognito User Pool

1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/v2/home).
2. Select `real-estate-rental-user-pool`.
3. Click **Delete user pool** and type `delete` to confirm deletion.

![Delete Cognito User Pool](/images/5-Workshop/5.6-Cleanup/5.6.2-aws-cognito.png)

---

#### 3. Delete Amazon Location Service

1. Open the [Amazon Location Service Console](https://console.aws.amazon.com/location/home).
2. Navigate and click **API key** on the left tab.

![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location.png)

3. Click **Deactivate** to disable the API Key, then click **Delete** to permanently delete it.

![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete.png)
![Delete Place Index](/images/5-Workshop/5.6-Cleanup/5.6.3-aws-location-delete(1).png)

---

#### 4. Delete IAM User

1. Open the [IAM Console](https://console.aws.amazon.com/iam/home#/users).
2. Click **IAM users** on the left tab.
3. Select the user to delete.
4. Click **Delete** and type `delete` to confirm deletion.

![Delete IAM User](/images/5-Workshop/5.6-Cleanup/5.6.4-aws-iam.png)
