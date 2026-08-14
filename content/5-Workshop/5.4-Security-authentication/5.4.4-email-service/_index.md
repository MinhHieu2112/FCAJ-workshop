---
title: "Sending email notifications with SMTP (Nodemailer)"
date: 2026-08-06
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### SMTP email service overview

The application dispatches **automated transactional emails** via the **SMTP protocol** (e.g., Gmail SMTP, Brevo, SendGrid SMTP, or a custom mail server) when key system events occur:
- A tenant submits a rental application for a property listing.
- A property manager approves or rejects a rental application.
- A digital lease agreement contract is generated and sent to users.

#### Step 1: Configure SMTP account credentials

To connect and dispatch emails through SMTP (for example, using Gmail SMTP):
1. Sign in to your Gmail account and enable **2-Step Verification**.
2. Navigate to **App Passwords** in your Google Account security settings.
3. Generate a new app password named `NestJS-Rental-System`.
4. Copy the generated **16-character app password** to use in your environment variables.

{{% notice tip %}}
If using other SMTP service providers (such as Brevo, Amazon SES SMTP Interface, or SendGrid), retrieve your **SMTP Host**, **SMTP Port** (typically 465 for SSL or 587 for TLS), **Username**, and **API Key / SMTP Password**.
{{% /notice %}}

#### Step 2: Install Nodemailer in NestJS

In your NestJS backend directory (`apps/server`), install the `nodemailer` package and TypeScript definitions:

```bash
pnpm add nodemailer
pnpm add -D @types/nodemailer
```

#### Step 3: Implement email notification service

Create `src/notification/email.service.ts` in your NestJS backend:

```typescript
import { Injectable, Logger } from '@nestjs/common';
import * as nodemailer from 'nodemailer';

@Injectable()
export class EmailService {
  private readonly logger = new Logger(EmailService.name);
  private transporter: nodemailer.Transporter;

  constructor() {
    this.transporter = nodemailer.createTransport({
      host: process.env.SMTP_HOST || 'smtp.gmail.com',
      port: Number(process.env.SMTP_PORT) || 587,
      secure: process.env.SMTP_SECURE === 'true',
      auth: {
        user: process.env.SMTP_USER,
        pass: process.env.SMTP_PASS,
      },
    });
  }

  async sendApplicationStatusEmail(params: {
    to: string;
    tenantName: string;
    propertyTitle: string;
    status: 'APPROVED' | 'REJECTED';
  }): Promise<void> {
    const subject = params.status === 'APPROVED'
      ? 'Your rental application has been approved'
      : 'Update regarding your rental application';

    const body = `Hello ${params.tenantName},\n\n` +
      `Your application for property listing "${params.propertyTitle}" has been ${params.status === 'APPROVED' ? 'approved' : 'rejected'}.\n\n` +
      `Please log in to the system to view further details.\n\nBest regards,\nReal Estate Management System`;

    try {
      await this.transporter.sendMail({
        from: process.env.SMTP_FROM || `"Real Estate System" <${process.env.SMTP_USER}>`,
        to: params.to,
        subject: subject,
        text: body,
      });
      this.logger.log(`Email successfully dispatched to ${params.to}`);
    } catch (err) {
      this.logger.error(`Failed to send email to ${params.to}`, err);
    }
  }
}
```

#### Step 4: Add SMTP environment variables

Add the SMTP configuration to `apps/server/.env`:

```env
SMTP_HOST="smtp.gmail.com"
SMTP_PORT=587
SMTP_SECURE=false
SMTP_USER="your-email@gmail.com"
SMTP_PASS="your-16-character-app-password"
SMTP_FROM='"Real Estate System" <your-email@gmail.com>'
```
