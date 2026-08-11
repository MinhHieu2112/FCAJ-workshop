---
title: "Cấu hình tên miền (DuckDNS) & chứng chỉ SSL/TLS"
date: 2026-08-06
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Bước 1: Đăng ký tên miền DuckDNS miễn phí

1. Truy cập [https://www.duckdns.org](https://www.duckdns.org) và đăng nhập bằng tài khoản GitHub hoặc Google.
2. Tại mục **Add domain**, nhập tên subdomain (ví dụ: `real-estate-api`).
3. Nhấn **Add domain**. Bạn sẽ nhận được tên miền miễn phí: `real-estate-api.duckdns.org`.
4. Sao chép **ALB DNS name** từ EC2 Console → **Load Balancers** → chọn ALB → **DNS name**.
5. Trong DuckDNS, cập nhật trường IP:
   - Nếu ALB cung cấp địa chỉ IP, nhập trực tiếp.
   - Nếu cung cấp hostname (ví dụ: `real-estate-alb-xxx.us-east-1.elb.amazonaws.com`), cần dùng cách CNAME. Vì DuckDNS chỉ hỗ trợ A record, hãy phân giải hostname ALB sang IP và nhập vào. (Trong môi trường production, nên dùng Route 53 với alias record thay thế.)

#### Bước 2: Yêu cầu chứng chỉ SSL/TLS với ACM

1. Truy cập [Bảng điều khiển AWS Certificate Manager](https://console.aws.amazon.com/acm/home).
2. Nhấn **Request a certificate** → **Request a public certificate**.
3. Nhập tên miền đầy đủ (FQDN), ví dụ:
   - `real-estate-api.duckdns.org`
4. Chọn **DNS validation** làm phương thức xác thực.
5. Nhấn **Request**.
6. Sao chép **CNAME name** và **CNAME value** hiển thị trong chi tiết chứng chỉ.
7. Trong cài đặt tên miền DuckDNS, thêm CNAME record với các giá trị được cung cấp để hoàn tất xác thực.

#### Bước 3: Gắn chứng chỉ vào HTTPS listener của ALB

1. Mở EC2 Console → **Load Balancers** → chọn ALB.
2. Chọn tab **Listeners** → nhấn vào listener HTTPS:443 → **Edit**.
3. Tại **Default SSL/TLS certificate**, nhấn **Add certificate** và chọn chứng chỉ ACM vừa tạo.
4. Nhấn **Save changes**.

{{% notice tip %}}
Chứng chỉ ACM gắn vào ALB được **tự động gia hạn** trước khi hết hạn. Không cần gia hạn thủ công.
{{% /notice %}}
