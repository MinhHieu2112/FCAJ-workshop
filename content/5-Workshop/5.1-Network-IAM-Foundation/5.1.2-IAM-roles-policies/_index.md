---
title: "IAM role configuration for EC2"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.1.2. </b> "
---

### 1. Overview

In our system architecture, the **EC2 backend server** must interact securely with **AWS Secrets Manager** to fetch production environment variables dynamically upon container startup.

**Traditional anti-pattern (Hardcoded credentials):**
Embedding static `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` credentials in source code or `.env` files introduces severe security risks if repository access is leaked.

**Recommended approach (IAM instance profiles):**
Attach an **IAM role** directly to the EC2 instance. This provides two key benefits:
- AWS automatically issues short-lived temporary credentials and rotates them periodically behind the scenes.
- The AWS SDK running inside the application automatically retrieves these credentials from the EC2 instance metadata service (IMDSv2) without storing long-term credentials in environment variables.

---

### 2. Step-by-step implementation

#### Step 1: Create an IAM role for EC2

1. Open the [AWS IAM Console](https://console.aws.amazon.com/iam/).
2. In the left navigation pane, select **Roles** and click **Create role**.
3. **Select trusted entity:**
   - **Trusted entity type**: Select **AWS service**.
   - **Use case**: Select **EC2** and click **Next**.
![IAM entity selection](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-1.png)

4. **Add permissions:**
   - Search for and select: `SecretsManagerReadWrite` *(or a custom scoped policy restricted to your secret ARN)*.
   - Click **Next**.
![IAM permissions setup](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-2.png)

5. **Name, review, and create:**
   - **Role name**: `RentifulEC2SecretManagerRole`
   - **Description**: `IAM Role granting EC2 backend access to AWS Secrets Manager`
6. Click **Create role** to complete setup.
![IAM role created 1](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-3.png)
![IAM role created 2](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/IAM-setting-4.png)

---

#### Step 2: Attach the IAM role to your EC2 instance

1. Open the [Amazon EC2 Console](https://console.aws.amazon.com/ec2/).
2. Select **Instances** and select your **EC2 Backend** instance.
3. In the top-right menu, select **Actions** -> **Security** -> **Modify IAM role**.
4. Under **IAM role**, select the newly created role: `RentifulEC2SecretManagerRole`.
5. Click **Update IAM role**.
![Modify IAM role 1](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/Setting-EC2-1.png)
![Modify IAM role 2](/images/5-Workshop/5.1-Overview/5.1.2-IAM-Role-Policies/Setting-EC2-2.png)

---

#### Step 3: Application code configuration

Once the IAM role is attached to the EC2 instance, configure the AWS SDK by providing only the `region`, omitting access keys:

```typescript
import { SecretsManagerClient } from "@aws-sdk/client-secrets-manager";

// AWS SDK automatically retrieves temporary credentials from the EC2 instance profile
export const secretsClient = new SecretsManagerClient({ 
  region: process.env.AWS_REGION!
});
```

{{% notice tip %}}
Using instance profiles follows AWS Security Best Practices for **least privilege with automated credential rotation**. Credentials rotate automatically through the EC2 Instance Metadata Service (IMDSv2).
{{% /notice %}}
