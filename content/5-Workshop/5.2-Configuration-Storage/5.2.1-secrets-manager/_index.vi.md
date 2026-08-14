---
title: "Quản lý khóa bí mật với Secrets Manager"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.2.1. </b> "
---

### 1. Tổng quan

Đối với các ứng dụng triển khai theo quy chuẩn sản xuất, việc lưu trữ mật khẩu cơ sở dữ liệu, JWT Secret, API keys hoặc cấu hình nhạy cảm dưới dạng văn bản thuần (plain-text) trong mã nguồn hoặc file `.env` chứa rủi ro an ninh rất lớn.

**AWS Secrets Manager** là dịch vụ lưu trữ và quản lý bảo mật các thông tin nhạy cảm (secrets). Dịch vụ giúp ứng dụng truy cập bí mật một cách tự động thông qua API, hỗ trợ tự động mã hóa bằng AWS KMS và hỗ trợ xoay vòng (rotation) mật khẩu định kỳ.

---

### 2. Các bước triển khai thực tế

#### Bước 1: Khởi tạo Secret trên AWS Console

1. Truy cập [bảng điều khiển AWS Secrets Manager](https://console.aws.amazon.com/secretsmanager/).
2. Nhấn nút **Store a new secret**.
![Store keys](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/store-keys.png)

3. Tại mục **Secret type**, chọn **Other type of secret**.
4. Tại phần **Key/value pairs**, nhập các biến cấu hình cần thiết cho hệ thống backend NestJS:
![Secret type](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/secret-type.png)

5. Nhấn **Next**.
6. Tại mục **Secret name**, nhập: `rentiful/production/env`.
![Configure secret 1](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-secret-1.png)
![Configure secret 2](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-secret-2.png)

7. Giữ các tùy chọn mặc định cho KMS Key mã hóa và nhấn **Next** -> **Store**.
![Configure rotation 1](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-rotation-1.png)
![Configure rotation 2](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-rotation-2.png)
![Store secret finish](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/create-store-secrets.png)

---

#### Bước 2: Tích hợp đọc Secret trong NestJS backend

Tại ứng dụng NestJS (`apps/server`), sử dụng gói `@aws-sdk/client-secrets-manager` để đọc cấu hình khi máy chủ khởi động:

```bash
pnpm add @aws-sdk/client-secrets-manager
```

Tạo helper đọc secret (`src/config/secrets.helper.ts`):

```typescript
import { SecretsManagerClient, GetSecretValueCommand } from '@aws-sdk/client-secrets-manager';

export async function loadProductionSecrets(): Promise<Record<string, string>> {
  const secretName = process.env.SECRET_NAME || 'rentiful/production/env';
  const client = new SecretsManagerClient({
    region: process.env.AWS_REGION || 'us-east-1',
  });

  try {
    const response = await client.send(
      new GetSecretValueCommand({ SecretId: secretName })
    );

    if (response.SecretString) {
      const secrets = JSON.parse(response.SecretString);
      // Nạp các bí mật vào biến môi trường process.env
      Object.assign(process.env, secrets);
      return secrets;
    }
  } catch (error) {
    console.warn('Không thể nạp secrets từ AWS Secrets Manager, sử dụng biến môi trường cục bộ:', error);
  }
  return {};
}
```

Nạp secrets trước khi ứng dụng NestJS khởi chạy (`src/main.ts`):

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { loadProductionSecrets } from './config/secrets.helper';

async function bootstrap() {
  if (process.env.NODE_ENV === 'production') {
    await loadProductionSecrets();
  }

  const app = await NestFactory.create(AppModule);
  await app.listen(process.env.PORT || 4000);
}
bootstrap();
```

{{% notice tip %}}
Khi máy chủ EC2 đã được gắn `RentifulEC2SecretManagerRole`, SDK sẽ tự động lấy quyền đọc `secretsmanager:GetSecretValue` mà không cần nhập thêm IAM User credential.
{{% /notice %}}
