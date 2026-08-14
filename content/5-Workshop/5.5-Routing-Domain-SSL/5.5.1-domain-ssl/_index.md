---
title: "Configuring DuckDNS domain, Caddy HTTPS reverse proxy & CORS"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

### 1. Understanding Mixed Content vs. CORS

In production deployments:
- **Next.js Frontend** is deployed on **Vercel** (`https://real-estate-client-one-eta.vercel.app`) operating over encrypted **HTTPS**.
- **NestJS Backend** runs inside a Docker container on **Amazon EC2** listening internally on port `4000`.

#### Root cause of Mixed Content errors
When an HTTPS web application makes API requests to an unencrypted HTTP backend endpoint (e.g., `http://54.210.xx.xx:4000`), modern web browsers **immediately block the request** with a **Mixed Content** security error to prevent eavesdropping and data tampering.

#### Mixed Content vs. CORS comparison
| Concept | Nature | Solution |
|---|---|---|
| **Mixed Content** | Browser security rule forbidding **HTTPS** pages from fetching resources from unencrypted **HTTP** endpoints. | Provision SSL/TLS (HTTPS) for the backend using a DuckDNS domain and Caddy Web Server. |
| **CORS (Cross-Origin)** | Browser policy controlling cross-origin resource sharing between distinct origins (Vercel frontend origin vs DuckDNS backend origin). | Configure `app.enableCors()` in NestJS to allow requests from the Vercel origin. |

---

### 2. Complete production request workflow

```
Frontend Vercel (HTTPS)
   └─► API Request: https://nestro.duckdns.org/api/...
         └─► DuckDNS A Record Resolution ──► EC2 Elastic IP
               └─► EC2 Security Group (Port 443 HTTPS)
                     └─► Caddy Web Server (Automated SSL)
                           └─► Internal Reverse Proxy ──► Docker Backend (:4000)
```

---

### 3. Step-by-step implementation

#### Step 1: Create and configure DuckDNS domain

1. Visit [DuckDNS.org](https://www.duckdns.org/) and log in.
2. Under **sub-domain**, enter `nestro` and click **add domain**.
3. Your free domain string is created: `nestro.duckdns.org`.

---

#### Step 2: Point DuckDNS to EC2 Elastic IP

1. Retrieve the **Elastic IP** address allocated to your EC2 instance in Module 5.1 (e.g., `54.210.xx.xx`).
2. Enter this IP address into the **IP address** field for domain `nestro` on DuckDNS and click **update ip**.
3. Verify DNS resolution from your workstation:
```bash
dig nestro.duckdns.org +short
# Or: nslookup nestro.duckdns.org
```

---

#### Step 3: Configure EC2 security group rules (Ports 80 & 443)

Ensure your EC2 security group (`sg-ec2-backend`) contains the following inbound rules:

| Type | Port | Source | Purpose |
|---|---|---|---|
| **HTTP** | `80` | `0.0.0.0/0` | Satisfies ACME HTTP-01 challenges for automated Caddy certificate issuance |
| **HTTPS** | `443` | `0.0.0.0/0` | Receives incoming encrypted HTTPS requests from the Vercel frontend |

{{% notice warning %}}
Port **4000** must remain private and **not exposed** in the Security Group.
{{% /notice %}}

---

#### Step 4: Install and configure Caddy Web Server as reverse proxy

1. **Install Caddy on EC2 (Amazon Linux 2023 / RHEL):**

```bash
sudo dnf install -y 'dnf-command(copr)'
sudo dnf copr enable -y @caddy/caddy
sudo dnf install -y caddy
```

2. **Configure Caddyfile (`/etc/caddy/Caddyfile`):**

Open the Caddy configuration file:
```bash
sudo nano /etc/caddy/Caddyfile
```

Replace its contents with:
```caddyfile
nestro.duckdns.org {
    reverse_proxy localhost:4000
}
```

3. **Start and enable the Caddy service:**

```bash
sudo systemctl enable --now caddy
sudo systemctl reload caddy
```

**Automated Caddy HTTPS mechanism:**
Caddy automatically detects the domain `nestro.duckdns.org`, requests an SSL/TLS certificate via ACME protocol over ports 80/443, enables HTTPS encryption, and handles automatic certificate renewals without manual intervention.

---

#### Step 5: Configure normalized CORS in NestJS backend

In your NestJS backend codebase (`apps/server/src/main.ts`), configure CORS to parse `CORS_ORIGIN`, strip trailing slashes `/`, and handle missing `Origin` headers:

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const rawOrigins = process.env.CORS_ORIGIN || 'https://real-estate-client-one-eta.vercel.app';
  const allowedOrigins = rawOrigins
    .split(',')
    .map((origin) => origin.trim().replace(/\/$/, '')) // Strip trailing slash /
    .filter(Boolean);

  app.enableCors({
    origin: (origin, callback) => {
      // Allow requests with no Origin header (cURL, health check, internal server call)
      if (!origin) {
        return callback(null, true);
      }

      const normalizedOrigin = origin.trim().replace(/\/$/, '');
      if (allowedOrigins.includes(normalizedOrigin)) {
        callback(null, true);
      } else {
        callback(new Error(`CORS Error: Origin ${origin} is not permitted.`));
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

#### Step 6: Configure frontend environment variable on Vercel

1. Open your project on the [Vercel Dashboard](https://vercel.com/).
2. Navigate to **Settings** -> **Environment Variables**.
3. Add the API endpoint variable:

| Key | Value |
|---|---|
| `NEXT_PUBLIC_API_URL` | `https://nestro.duckdns.org` |

4. Trigger a new deployment on Vercel to apply the updated environment variable.

---

#### Step 7: Verify HTTPS connectivity and CORS headers

1. **Verify HTTPS connection via cURL:**
```bash
curl -i https://nestro.duckdns.org/api/health
```

Expected HTTP/2 200 response with valid SSL certificate:
```http
HTTP/2 200 
server: Caddy
content-type: application/json; charset=utf-8
content-length: 46

{"status":"ok","timestamp":"2026-08-14T10:00:00.000Z"}
```

2. **Verify CORS preflight headers:**
```bash
curl -i -X OPTIONS https://nestro.duckdns.org/api/health \
  -H "Origin: https://real-estate-client-one-eta.vercel.app" \
  -H "Access-Control-Request-Method: GET"
```

Expected CORS headers:
```http
HTTP/2 204 
server: Caddy
access-control-allow-origin: https://real-estate-client-one-eta.vercel.app
access-control-allow-credentials: true
```

3. Launch your Vercel frontend (`https://real-estate-client-one-eta.vercel.app`) in your browser. Open Developer Tools (`F12`) -> **Network** tab to confirm all API calls execute over HTTPS without Mixed Content or CORS errors.
