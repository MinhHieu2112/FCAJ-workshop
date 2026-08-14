---
title: "Cấu hình tên miền DuckDNS, Caddy HTTPS & CORS"
date: 2026-08-06
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Tổng quan

Trong mô-đun này, bạn sẽ thiết lập tên miền và giao thức bảo mật **HTTPS** cho backend API, giúp frontend Vercel giao tiếp an toàn với máy chủ EC2:

- **Giải quyết lỗi Mixed Content**: Khắc phục triệt để rào cản trình duyệt khi Vercel (HTTPS) gọi backend HTTP.
- **Tên miền DuckDNS**: Đăng ký tên miền miễn phí `https://nestro.duckdns.org` trỏ về Elastic IP của EC2.
- **Caddy Web Server**: Reverse Proxy tự động cấp phát và gia hạn chứng chỉ SSL/TLS, nhận lưu lượng HTTPS trên cổng 443 và chuyển tiếp tới container NestJS trên cổng 4000.
- **Cấu hình CORS trong NestJS**: Cho phép domain Vercel truy cập tài nguyên API an toàn.

#### Các bước thực hiện

1. [Cấu hình DuckDNS, Caddy HTTPS Reverse Proxy & CORS](5.3.1-domain-ssl/)
