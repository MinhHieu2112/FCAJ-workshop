---
title: "Gửi email thông báo tự động với SMTP (Nodemailer)"
date: 2026-08-06
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Tổng quan dịch vụ gửi email SMTP

Hệ thống gửi **email thông báo tự động (transactional emails)** qua giao thức **SMTP** (ví dụ: Gmail SMTP, Brevo, SendGrid SMTP hoặc hệ thống mail server riêng) khi diễn ra các sự kiện quan trọng trong hệ thống:
- Người thuê nộp đơn xin thuê bất động sản.
- Chủ nhà hoặc quản lý hệ thống phê duyệt/từ chối đơn xin thuê.
- Hợp đồng thuê nhà kỹ thuật số được khởi tạo và gửi tới người dùng.

#### Bước 1: Cấu hình thông tin tài khoản SMTP

Để kết nối và gửi mail thông qua dịch vụ SMTP (ví dụ sử dụng Gmail SMTP):
1. Đăng nhập vào tài khoản Gmail và kích hoạt **Xác thực 2 bước (2-Step Verification)**.
2. Truy cập phần **Mật khẩu ứng dụng (App Passwords)** trong phần cài đặt bảo mật tài khoản Google.
3. Tạo mật khẩu ứng dụng mới với tên thiết bị/ứng dụng, ví dụ: `NestJS-Rental-System`.
4. Lưu lại chuỗi **Mật khẩu ứng dụng 16 ký tự** vừa tạo để sử dụng trong biến môi trường.

{{% notice tip %}}
Nếu sử dụng các nhà cung cấp dịch vụ SMTP khác (như Brevo, Amazon SES SMTP Interface, SendGrid), bạn cần lấy các thông tin tương tự gồm **SMTP Host**, **SMTP Port** (thường là 465 cho SSL hoặc 587 cho TLS), **Username** và **API Key / Mật khẩu SMTP**.
{{% /notice %}}

#### Bước 2: Cài đặt thư viện Nodemailer trong NestJS

Tại thư mục dự án NestJS backend (`apps/server`), tiến hành cài đặt gói `nodemailer` cùng khai báo type cho TypeScript:

```bash
pnpm add nodemailer
pnpm add -D @types/nodemailer
```

#### Bước 3: Xây dựng Email Notification Service

Tạo file `src/notification/email.service.ts` trong dự án NestJS backend:

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
      ? 'Đơn xin thuê của bạn đã được chấp thuận'
      : 'Cập nhật về đơn xin thuê của bạn';

    const body = `Xin chào ${params.tenantName},\n\n` +
      `Đơn xin thuê bất động sản "${params.propertyTitle}" của bạn đã được ${params.status === 'APPROVED' ? 'chấp thuận' : 'từ chối'}.\n\n` +
      `Vui lòng đăng nhập vào hệ thống để kiểm tra thông tin chi tiết.\n\nTrân trọng,\nHệ thống quản lý bất động sản cho thuê`;

    try {
      await this.transporter.sendMail({
        from: process.env.SMTP_FROM || `"Real Estate System" <${process.env.SMTP_USER}>`,
        to: params.to,
        subject: subject,
        text: body,
      });
      this.logger.log(`Email đã gửi thành công tới địa chỉ ${params.to}`);
    } catch (err) {
      this.logger.error(`Gửi email thất bại tới ${params.to}`, err);
    }
  }
}
```

#### Bước 4: Thêm biến môi trường SMTP

Cập nhật thông số cấu hình trong file `apps/server/.env`:

```env
SMTP_HOST="smtp.gmail.com"
SMTP_PORT=587
SMTP_SECURE=false
SMTP_USER="your-email@gmail.com"
SMTP_PASS="your-16-character-app-password"
SMTP_FROM='"Real Estate System" <your-email@gmail.com>'
```
