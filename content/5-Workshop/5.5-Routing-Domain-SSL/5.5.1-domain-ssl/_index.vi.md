---
title: "Cấu hình tên miền DuckDNS, Caddy HTTPS Reverse Proxy & CORS"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

### 1. Phân biệt Mixed Content & CORS

Khi triển khai ứng dụng thực tế trên môi trường sản xuất:
- **Frontend Next.js** được triển khai trên **Vercel** (`https://real-estate-client-one-eta.vercel.app`) chạy trên giao thức mã hóa an toàn **HTTPS**.
- **Backend NestJS** chạy trong container Docker trên **Amazon EC2** lắng nghe tại cổng `4000`.

#### Nguyên nhân gây lỗi Mixed Content
Nếu frontend HTTPS gửi API request trực tiếp đến địa chỉ IP unencrypted HTTP của EC2 (ví dụ: `http://54.210.xx.xx:4000`), trình duyệt sẽ **ngay lập tức ngăn chặn kết nối** và thông báo lỗi **Mixed Content** (Nội dung hỗn hợp). Trình duyệt chặn điều này để bảo vệ dữ liệu người dùng không bị nghe lén hoặc can thiệp trên đường truyền.

#### Phân biệt Mixed Content vs CORS
| Khái niệm | Bản chất | Cách khắc phục |
|---|---|---|
| **Mixed Content** | Quy tắc an ninh của trình duyệt cấm trang web **HTTPS** tải tài nguyên từ điểm cuối **HTTP** không mã hóa. | Thiết lập SSL/TLS (HTTPS) cho backend bằng tên miền DuckDNS và Caddy Web Server. |
| **CORS (Cross-Origin)** | Quy tắc chia sẻ tài nguyên giữa hai Origin khác nhau (ví dụ: Vercel frontend và DuckDNS backend). | Cấu hình `app.enableCors()` trong NestJS để chấp nhận Origin từ Vercel. |

---

### 2. Luồng request hoàn chỉnh trong thực tế

```
Frontend Vercel (HTTPS)
   └─► Request API: https://nestro.duckdns.org/api/...
         └─► Phân giải DNS DuckDNS ──► Elastic IP EC2
               └─► EC2 Security Group (Port 443 HTTPS)
                     └─► Caddy Web Server (Tự động cấp SSL)
                           └─► Reverse Proxy nội bộ ──► Docker Backend (:4000)
```

---

### 3. Các bước triển khai thực tế

#### Bước 1: Tạo và cấu hình tên miền DuckDNS

1. Truy cập [DuckDNS.org](https://www.duckdns.org/) và đăng nhập tài khoản.
2. Tại mục **sub-domain**, nhập `nestro` và nhấn **add domain**.
3. Tên miền miễn phí được khởi tạo: `nestro.duckdns.org`.

---

#### Bước 2: Trỏ DuckDNS về Elastic IP của EC2

1. Lấy địa chỉ **Elastic IP** của EC2 đã tạo ở Mô-đun 5.1 (ví dụ: `54.210.xx.xx`).
2. Nhập IP này vào ô **IP address** của tên miền `nestro` trên giao diện DuckDNS và nhấn **update ip**.
3. Kiểm tra phân giải tên miền từ máy tính của bạn:
```bash
dig nestro.duckdns.org +short
# Hoặc: nslookup nestro.duckdns.org
```

---

#### Bước 3: Cấu hình Security Group cho cổng 80 & 443

Đảm bảo Security Group `sg-ec2-backend` của máy chủ EC2 có các quy tắc Inbound sau:

| Type | Port | Source | Mục đích |
|---|---|---|---|
| **HTTP** | `80` | `0.0.0.0/0` | Phục vụ ACME HTTP-01 Challenge để Caddy tự động xin cấp chứng chỉ |
| **HTTPS** | `443` | `0.0.0.0/0` | Tiếp nhận kết nối HTTPS mã hóa từ Vercel frontend |

{{% notice warning %}}
Cổng **4000** của Docker container giữ an toàn nội bộ, **không mở công khai** trên Security Group.
{{% /notice %}}

---

#### Bước 4: Cài đặt và cấu hình Caddy Web Server làm Reverse Proxy

1. **Cài đặt Caddy trên máy chủ EC2 (Amazon Linux 2023 / RHEL):**

```bash
sudo dnf install -y 'dnf-command(copr)'
sudo dnf copr enable -y @caddy/caddy
sudo dnf install -y caddy
```

2. **Cấu hình Caddyfile (`/etc/caddy/Caddyfile`):**

Mở file cấu hình Caddy:
```bash
sudo nano /etc/caddy/Caddyfile
```

Thay thế toàn bộ nội dung bằng cấu hình sau:
```caddyfile
nestro.duckdns.org {
    reverse_proxy localhost:4000
}
```

3. **Khởi chạy và bật dịch vụ Caddy:**

```bash
sudo systemctl enable --now caddy
sudo systemctl reload caddy
```

**Cơ chế HTTPS tự động của Caddy:**
Caddy tự động phát hiện tên miền `nestro.duckdns.org`, tự gửi yêu cầu xin cấp chứng chỉ SSL/TLS thông qua giao thức ACME, cấu hình khóa mã hóa trên cổng 443 và tự động gia hạn chứng chỉ trước khi hết hạn mà **không cần can thiệp thủ công**.

---

#### Bước 5: Cấu hình CORS chuẩn hóa trong NestJS Backend

Trong mã nguồn backend (`apps/server/src/main.ts`), cấu hình CORS xử lý biến môi trường `CORS_ORIGIN`, loại bỏ trailing slash `/` và hỗ trợ request không có `Origin`:

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const rawOrigins = process.env.CORS_ORIGIN || 'https://real-estate-client-one-eta.vercel.app';
  const allowedOrigins = rawOrigins
    .split(',')
    .map((origin) => origin.trim().replace(/\/$/, '')) // Bỏ trailing slash /
    .filter(Boolean);

  app.enableCors({
    origin: (origin, callback) => {
      // Cho phép request không có Origin header (cURL, health check, internal call)
      if (!origin) {
        return callback(null, true);
      }

      const normalizedOrigin = origin.trim().replace(/\/$/, '');
      if (allowedOrigins.includes(normalizedOrigin)) {
        callback(null, true);
      } else {
        callback(new Error(`CORS Error: Origin ${origin} không được phép.`));
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

#### Bước 6: Cấu hình Frontend trên Vercel

1. Truy cập dự án Frontend trên [Bảng điều khiển Vercel](https://vercel.com/).
2. Chọn **Settings** -> **Environment Variables**.
3. Thêm biến môi trường trỏ đến điểm cuối Caddy HTTPS:

| Key | Value |
|---|---|
| `NEXT_PUBLIC_API_URL` | `https://nestro.duckdns.org` |

4. Thực hiện Redeploy lại dự án trên Vercel để áp dụng biến môi trường mới.

---

#### Bước 7: Kiểm tra và xác nhận kết quả HTTPS & CORS

1. **Kiểm tra kết nối HTTPS qua cURL:**
```bash
curl -i https://nestro.duckdns.org/api/health
```

Phản hồi thành công (HTTP/2 200 OK với chứng chỉ HTTPS hợp lệ):
```http
HTTP/2 200 
server: Caddy
content-type: application/json; charset=utf-8
content-length: 46

{"status":"ok","timestamp":"2026-08-14T10:00:00.000Z"}
```

2. **Kiểm tra CORS Header với Origin của Vercel:**
```bash
curl -i -X OPTIONS https://nestro.duckdns.org/api/health \
  -H "Origin: https://real-estate-client-one-eta.vercel.app" \
  -H "Access-Control-Request-Method: GET"
```

Phản hồi xác nhận CORS được chấp nhận:
```http
HTTP/2 204 
server: Caddy
access-control-allow-origin: https://real-estate-client-one-eta.vercel.app
access-control-allow-credentials: true
```

3. Mở ứng dụng trên Vercel (`https://real-estate-client-one-eta.vercel.app`) trên trình duyệt, mở Developer Tools (`F12`) -> Tab **Network** để xác nhận mọi API call thành công qua HTTPS mà không gặp lỗi Mixed Content hay CORS.
