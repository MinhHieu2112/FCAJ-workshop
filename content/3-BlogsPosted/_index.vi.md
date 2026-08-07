---
title: "Các bài blog đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

### [Blog 1 - Hệ thống xác thực với Amazon Cognito](3.1-Blog1/)

Blog giới thiệu quá trình tích hợp **Amazon Cognito** vào hệ thống quản lý cho thuê bất động sản, từ cơ chế xác thực bằng JWT đến phân quyền theo vai trò (**Manager** và **Tenant**) với `JwtAuthGuard` và `RolesGuard`. Bài viết cũng chia sẻ những vấn đề gặp phải khi làm việc với Access Token, ID Token và cách tối ưu quy trình xác thực trong ứng dụng Next.js và NestJS.

### [Blog 2 - Quản lý hình ảnh với Amazon S3](3.2-Blog2/)

Blog trình bày cách sử dụng **Amazon S3** để lưu trữ hình ảnh bất động sản, tối ưu quá trình tải lên và hiển thị ảnh trong ứng dụng. Nội dung bao gồm cơ chế tải ảnh bằng Presigned URL, quản lý quyền truy cập thông qua AWS IAM, lưu trữ đường dẫn ảnh trong PostgreSQL và tối ưu hiển thị bằng thành phần `Image` của Next.js.

### [Blog 3 - Bản đồ và định vị với Amazon Location Service](3.3-Blog3/)

Blog giới thiệu việc tích hợp **Amazon Location Service** và **MapLibre GL JS** để xây dựng chức năng bản đồ trong hệ thống. Bài viết trình bày quy trình chuyển đổi địa chỉ thành tọa độ, hiển thị vị trí bất động sản trên bản đồ, quản lý quyền truy cập bằng AWS IAM và những kinh nghiệm khi xử lý SigV4 cũng như tối ưu Geocoding trong quá trình phát triển.