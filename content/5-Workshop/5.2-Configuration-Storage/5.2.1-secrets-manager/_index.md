---
title: "Managing secrets with AWS Secrets Manager"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.2.1. </b> "
---

### 1. Overview

In production web applications, storing database connection credentials, JWT secrets, API keys, or sensitive configuration parameters in plain-text source files or version-controlled `.env` files poses a severe security risk.

**AWS Secrets Manager** helps you protect access to your applications, services, and IT resources. You can retrieve secrets dynamically using AWS SDK APIs, encrypted at rest using AWS Key Management Service (KMS), and configure automated secret rotation.

---

### 2. Step-by-step implementation

#### Step 1: Create a secret in AWS Secrets Manager

1. Open the [AWS Secrets Manager Console](https://console.aws.amazon.com/secretsmanager/).
2. Click **Store a new secret**.
![Store keys](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/store-keys.png)

3. Under **Secret type**, select **Other type of secret**.
4. In the **Key/value pairs** section, add the required backend environment variables.
![Secret type](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/secret-type.png)

5. Click **Next**.
6. Under **Secret name**, enter: `rentiful/production/env`.
![Configure secret 1](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-secret-1.png)
![Configure secret 2](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-secret-2.png)

7. Keep default KMS encryption settings and click **Next** -> **Store**.
![Configure rotation 1](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-rotation-1.png)
![Configure rotation 2](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/configure-rotation-2.png)
![Store secret finish](/images/5-Workshop/5.2-Prerequisite/5.2.1-Secret-manager/create-store-secrets.png)

---

#### Step 2: Integrate AWS Secrets Manager in NestJS

In your NestJS backend codebase (`apps/server`), install `@aws-sdk/client-secrets-manager`:

```bash
pnpm add @aws-sdk/client-secrets-manager
```

Create a secret loader utility (`src/config/secrets.helper.ts`):

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
      // Inject retrieved secrets directly into process.env
      Object.assign(process.env, secrets);
      return secrets;
    }
  } catch (error) {
    console.warn('Unable to load secrets from AWS Secrets Manager, falling back to local env:', error);
  }
  return {};
}
```

Bootstrap application secrets during server startup (`src/main.ts`):

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
When your EC2 instance is assigned the `RentifulEC2SecretManagerRole`, the SDK seamlessly authorizes `secretsmanager:GetSecretValue` without embedding AWS Access Keys in local configuration files.
{{% /notice %}}
