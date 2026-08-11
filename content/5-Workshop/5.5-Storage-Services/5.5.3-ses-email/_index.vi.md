---
title: "Gửi email thông báo với Amazon SES"
date: 2026-08-06
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

#### Tổng quan tích hợp Amazon SES

**Amazon Simple Email Service (SES)** gửi email giao dịch tự động khi các sự kiện quan trọng xảy ra:
- Người thuê nộp đơn xin thuê.
- Chủ nhà chấp thuận hoặc từ chối đơn.
- Hợp đồng thuê được tạo.

#### Bước 1: Xác thực địa chỉ email gửi trong SES

1. Truy cập [Bảng điều khiển Amazon SES](https://console.aws.amazon.com/ses/home).
2. Trong menu trái, chọn **Verified identities** → **Create identity**.
3. Chọn **Email address** và nhập địa chỉ email gửi (ví dụ: `noreply@yourdomain.com`).
4. Nhấn **Create identity** và kiểm tra hộp thư đến để xác thực.
5. Nhấn vào link xác thực trong email nhận được.

{{% notice warning %}}
Trong **chế độ SES sandbox** (mặc định cho tài khoản mới), bạn chỉ có thể gửi đến các địa chỉ email đã xác thực. Để gửi đến bất kỳ địa chỉ nào, yêu cầu quyền production qua **Account dashboard** → **Request production access**.
{{% /notice %}}

#### Bước 2: Cài đặt SES SDK trong NestJS

```bash
pnpm add @aws-sdk/client-ses
```

#### Bước 3: Xây dựng notification service

Tạo `src/notification/ses.service.ts` trong NestJS backend:

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
      ? 'Đơn xin thuê của bạn đã được chấp thuận'
      : 'Cập nhật về đơn xin thuê của bạn';

    const body = `Xin chào ${params.tenantName},\n\n` +
      `Đơn xin thuê bất động sản "${params.propertyTitle}" của bạn đã được ${params.status === 'APPROVED' ? 'chấp thuận' : 'từ chối'}.\n\n` +
      `Vui lòng đăng nhập vào nền tảng để biết thêm chi tiết.\n\nHệ thống quản lý bất động sản cho thuê`;

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
      this.logger.log(`Email đã gửi đến ${params.to}`);
    } catch (err) {
      this.logger.error(`Gửi email thất bại đến ${params.to}`, err);
    }
  }
}
```

#### Bước 4: Thêm biến môi trường SES

```env
SES_SENDER_EMAIL="noreply@yourdomain.com"
AWS_REGION="us-east-1"
```
