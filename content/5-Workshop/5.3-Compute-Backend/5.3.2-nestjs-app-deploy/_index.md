---
title: "Deploying the NestJS backend application & configuring CORS"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

### 1. Overview

With the Docker engine operational on your EC2 host, this section covers containerizing the **NestJS** backend application, executing Prisma schema migrations against RDS PostgreSQL, and implementing robust **CORS (Cross-Origin Resource Sharing)** configuration for Vercel frontend integration.

---

### 2. Normalized CORS configuration in NestJS (`src/main.ts`)

To allow secure cross-origin API calls from the Next.js application deployed on Vercel (`https://real-estate-client-one-eta.vercel.app`):

1. **Parse origins from `CORS_ORIGIN`**: Supports comma-separated origins.
2. **Normalize origins**: Strip trailing slash `/` characters to avoid domain string mismatches (e.g. `https://real-estate-client-one-eta.vercel.app/` vs `https://real-estate-client-one-eta.vercel.app`).
3. **Allow missing `Origin` headers (`!origin`)**: Permits internal health check calls, `cURL` commands, and server-to-server requests without browser CORS errors.

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // Normalize and parse CORS origin configuration
  const rawOrigins = process.env.CORS_ORIGIN || 'https://real-estate-client-one-eta.vercel.app';
  const allowedOrigins = rawOrigins
    .split(',')
    .map((origin) => origin.trim().replace(/\/$/, '')) // Strip trailing slashes
    .filter(Boolean);

  app.enableCors({
    origin: (origin, callback) => {
      // Allow requests with no Origin header (cURL, Postman, internal healthchecks)
      if (!origin) {
        return callback(null, true);
      }

      const normalizedOrigin = origin.trim().replace(/\/$/, '');
      if (allowedOrigins.includes(normalizedOrigin)) {
        callback(null, true);
      } else {
        callback(new Error(`CORS Error: Origin ${origin} is not allowed.`));
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

### 3. Container deployment steps

#### Step 1: Multi-stage Dockerfile (`apps/server/Dockerfile`)

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

#### Step 2: Configure EC2 environment variables (`~/apps/server/.env`)

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

#### Step 3: Run Prisma migrations and start backend container

```bash
# 1. Execute database schema migrations
docker run --rm \
  --env-file ~/apps/server/.env \
  <your-dockerhub-username>/real-estate-server:latest \
  npx prisma migrate deploy

# 2. Launch backend service container listening on port 4000
docker run -d \
  --name nestjs-backend \
  --restart unless-stopped \
  -p 4000:4000 \
  --env-file ~/apps/server/.env \
  <your-dockerhub-username>/real-estate-server:latest
```

---

#### Step 4: Verify API health check

```bash
# Query health check (request without Origin header)
curl http://localhost:4000/api/health
```

Expected response:
```json
{"status":"ok","timestamp":"2026-08-14T10:00:00.000Z"}
```
