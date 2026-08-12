---
title: "Blog 2"
date: 2026-08-03
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---
# QUẢN LÝ HÌNH ẢNH VỚI AMAZON S3 

Trong hệ thống quản lý cho thuê bất động sản, mỗi tài sản có thể bao gồm hàng loạt hình ảnh chất lượng cao (`photoUrls`) để mô tả chi tiết. Nếu lưu trữ tệp trực tiếp trên máy chủ ứng dụng (NestJS), dung lượng bộ nhớ sẽ tăng nhanh và dễ gây nghẽn I/O khi có nhiều request đồng thời. Vì vậy, em lựa chọn **Amazon S3** làm dịch vụ lưu trữ hình ảnh chuyên dụng.

Để tối ưu hiệu năng và bảo mật, quy trình xử lý hình ảnh được thiết kế theo hướng:

* Sử dụng cơ chế **S3 Presigned URL**: Frontend (Next.js) xin cấp URL có chữ ký thời hạn ngắn từ Backend, sau đó **tải ảnh trực tiếp lên Amazon S3** mà không cần truyền qua server trung gian, giúp giải phóng hoàn toàn tải CPU/RAM cho Backend NestJS.
* Quản lý quyền truy cập S3 nghiêm ngặt thông qua **AWS IAM**, chỉ cấp quyền tạo chữ ký (`PutObject`) cho Backend.
* Sau khi tải lên S3 thành công, URL của hình ảnh mới được lưu vào cơ sở dữ liệu (PostgreSQL/Prisma) để phục vụ việc truy vấn.
* Tích hợp **Next.js Image (`<Image />`)** kết hợp cấu hình `remotePatterns` trong `next.config.js` để tự động nén, lazy-loading và tối ưu định dạng ảnh (WebP/AVIF) phía Client.
* Xây dựng bộ ảnh mặc định (Placeholder Image) để xử lý mượt mà các trường hợp liên kết ảnh hỏng hoặc không tồn tại.

Trong quá trình triển khai, em đã giải quyết vấn đề lỗi **403 Forbidden** do Bucket Policy và CORS trên S3 chưa cho phép domain của Next.js gọi request. Sau khi cấu hình chính xác `AllowedHeaders`, `AllowedOrigins` trên S3 Bucket và rà soát IAM Policy, hệ thống đã vận hành upload và hiển thị hình ảnh ổn định, tốc độ cao.

## Hình ảnh minh họa

![Overview](/images/3-BlogsPosted/S3_Image_Architecture.png)

## Tham khảo

- https://docs.aws.amazon.com/s3/
- https://docs.aws.amazon.com/IAM/
- https://nextjs.org/docs/app/api-reference/components/image