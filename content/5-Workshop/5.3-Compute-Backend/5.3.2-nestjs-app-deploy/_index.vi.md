---
title: "Triển khai ứng dụng NestJS backend & cấu hình CORS"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

### 1. Tổng quan

Sau khi cài đặt Docker runtime trên EC2, bước này thực hiện đóng gói container ứng dụng backend **NestJS**, thực thi Prisma database migration và cấu hình chính sách **CORS (Cross-Origin Resource Sharing)** chuẩn để tương tác an toàn với Vercel frontend.

---

### 2. Cấu hình CORS chuẩn hóa trong NestJS (`src/main.ts`)

Để cho phép ứng dụng Next.js trên Vercel (`https://real-estate-client-one-eta.vercel.app`) gọi API an toàn:

1. **Đọc danh sách Origin từ `CORS_ORIGIN`**: Hỗ trợ truyền nhiều domain (phân cách bằng dấu phẩy `,`).
2. **Chuẩn hóa Origin**: Loại bỏ ký tự slash `/` ở cuối domain để tránh lỗi so sánh địa chỉ (ví dụ: `https://real-estate-client-one-eta.vercel.app/` vs `https://real-estate-client-one-eta.vercel.app`).
3. **Cho phép request không có Origin (`!origin`)**: Cho phép các lệnh `cURL`, health check nội bộ và các cuộc gọi server-to-server đi qua mà không bị trình duyệt hoặc NestJS chặn.

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // Chuẩn hóa và xử lý danh sách CORS Origins
  const rawOrigins = process.env.CORS_ORIGIN || 'https://real-estate-client-one-eta.vercel.app';
  const allowedOrigins = rawOrigins
    .split(',')
    .map((origin) => origin.trim().replace(/\/$/, '')) // Loại bỏ trailing slash /
    .filter(Boolean);

  app.enableCors({
    origin: (origin, callback) => {
      // Cho phép request không có Origin (cURL, Postman, internal healthchecks)
      if (!origin) {
        return callback(null, true);
      }

      const normalizedOrigin = origin.trim().replace(/\/$/, '');
      if (allowedOrigins.includes(normalizedOrigin)) {
        callback(null, true);
      } else {
        callback(new Error(`CORS Error: Origin ${origin} không được phép truy cập.`));
      }
    },
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
    allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With'],
  });

  await app.listen(process.env.PORT || 4000);
}
bootstrap();
```

---

### 3. Các bước triển khai container

#### Bước 1: Multi-stage Dockerfile (`apps/server/Dockerfile`)

```dockerfile
# Stage 1: Build phase
FROM node:20-alpine AS builder
WORKDIR /app
RUN corepack enable && corepack prepare pnpm@latest --activate

COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY apps/server/package.json ./apps/server/
COPY packages/shared/package.json ./packages/shared/

RUN pnpm install --frozen-lockfile

COPY apps/server ./apps/server
COPY packages/shared ./packages/shared

RUN pnpm --filter @shared/types build
RUN pnpm --filter server build

# Stage 2: Production runtime phase
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

RUN corepack enable && corepack prepare pnpm@latest --activate

COPY --from=builder /app/package.json /app/pnpm-lock.yaml /app/pnpm-workspace.yaml ./
COPY --from=builder /app/apps/server/package.json ./apps/server/
COPY --from=builder /app/apps/server/dist ./apps/server/dist
COPY --from=builder /app/apps/server/prisma ./apps/server/prisma
COPY --from=builder /app/packages ./packages

RUN pnpm install --prod --frozen-lockfile

EXPOSE 4000
CMD ["node", "apps/server/dist/src/main.js"]
```

---

#### Bước 2: Chuẩn bị file biến môi trường trên EC2 (`~/apps/server/.env`)

```env
PORT=4000
NODE_ENV=production
AWS_REGION=us-east-1
CORS_ORIGIN="https://real-estate-client-one-eta.vercel.app"
DATABASE_URL="postgresql://postgres:<password>@<rds-endpoint>:5432/rental_db?schema=public"
COGNITO_USER_POOL_ID="us-east-1_xxxxxxxxx"
COGNITO_CLIENT_ID="xxxxxxxxxxxxxxxxxxxxxxxxxx"
S3_BUCKET_NAME="real-estate-rental-media-dev"
SES_SENDER_EMAIL="noreply@yourdomain.com"
```

---

#### Bước 3: Chạy Prisma migration và khởi tạo container

```bash
# 1. Chạy Prisma migration
docker run --rm \
  --env-file ~/apps/server/.env \
  <your-dockerhub-username>/real-estate-server:latest \
  npx prisma migrate deploy

# 2. Khởi chạy container backend listening port 4000
docker run -d \
  --name nestjs-backend \
  --restart unless-stopped \
  -p 4000:4000 \
  --env-file ~/apps/server/.env \
  <your-dockerhub-username>/real-estate-server:latest
```

---

#### Bước 4: Kiểm tra phản hồi API

```bash
# Kiểm tra healthcheck nội bộ (request không có Origin)
curl http://localhost:4000/api/health
```

Kết quả phản hồi:
```json
{"status":"ok","timestamp":"2026-08-14T10:00:00.000Z"}
```
