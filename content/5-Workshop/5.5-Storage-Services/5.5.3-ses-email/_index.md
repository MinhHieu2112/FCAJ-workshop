---
title: "Email notifications with Amazon SES"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

#### Amazon SES integration overview

**Amazon Simple Email Service (SES)** sends automated transactional emails when key events occur:
- A tenant submits a rental application.
- A manager approves or rejects an application.
- A lease contract is created.

#### Step 1: Verify sender email in SES

1. Open the [Amazon SES Console](https://console.aws.amazon.com/ses/home).
2. In the left menu, select **Verified identities** → **Create identity**.
3. Choose **Email address** and enter the sender email (e.g., `noreply@yourdomain.com`).
4. Click **Create identity** and check your inbox for the verification email.
5. Click the verification link in the email.

{{% notice warning %}}
In **SES sandbox mode** (default for new accounts), you can only send to verified email addresses. To send to any address, request production access via **Account dashboard** → **Request production access**.
{{% /notice %}}

#### Step 2: Install the SES SDK in NestJS

```bash
pnpm add @aws-sdk/client-ses
```

#### Step 3: Implement the notification service

Create `src/notification/ses.service.ts` in the NestJS backend:

```typescript
import { Injectable, Logger } from '@nestjs/common';
import { SESClient, SendEmailCommand } from '@aws-sdk/client-ses';

@Injectable()
export class SesService {
  private readonly logger = new Logger(SesService.name);
  private client = new SESClient({ region: process.env.AWS_REGION || 'us-east-1' });

  async sendApplicationStatusEmail(params: {
    to: string;
    tenantName: string;
    propertyTitle: string;
    status: 'APPROVED' | 'REJECTED';
  }): Promise<void> {
    const subject = params.status === 'APPROVED'
      ? 'Your rental application has been approved'
      : 'Update on your rental application';

    const body = `Hello ${params.tenantName},\n\n` +
      `Your application for "${params.propertyTitle}" has been ${params.status.toLowerCase()}.\n\n` +
      `Please log in to the platform for more details.\n\nReal Estate Rental Management System`;

    const command = new SendEmailCommand({
      Source: process.env.SES_SENDER_EMAIL,
      Destination: { ToAddresses: [params.to] },
      Message: {
        Subject: { Data: subject },
        Body: { Text: { Data: body } },
      },
    });

    try {
      await this.client.send(command);
      this.logger.log(`Email sent to ${params.to}`);
    } catch (err) {
      this.logger.error(`Failed to send email to ${params.to}`, err);
    }
  }
}
```

#### Step 4: Add SES environment variables

```env
SES_SENDER_EMAIL="noreply@yourdomain.com"
AWS_REGION="us-east-1"
```
