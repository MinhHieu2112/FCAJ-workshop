---
title: "Blog 3"
date: 2026-08-04
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# BẢN ĐỒ ĐỊNH VỊ VỚI AWS LOCATION SERVICE

Trong hệ thống quản lý cho thuê bất động sản, vị trí là một trong những yếu tố quan trọng ảnh hưởng đến quyết định của người thuê. Người dùng không chỉ cần xem thông tin về căn hộ mà còn muốn biết bất động sản nằm ở đâu, khu vực xung quanh như thế nào và có thuận tiện cho việc di chuyển hay không. Vì vậy, hệ thống cần hỗ trợ hiển thị vị trí bất động sản trên bản đồ và cho phép người quản lý lựa chọn địa điểm một cách trực quan khi đăng tin.

Để đáp ứng yêu cầu đó, em lựa chọn **AWS Location Service** kết hợp với **MapLibre GL JS** nhằm xây dựng chức năng bản đồ và tìm kiếm vị trí. Giải pháp này giúp tận dụng hạ tầng AWS, dễ dàng tích hợp với các dịch vụ khác trong hệ thống và đáp ứng tốt nhu cầu hiển thị dữ liệu không gian.

Trong quá trình triển khai, hệ thống được xây dựng theo các hướng sau:

- Sử dụng **AWS Location Service** để cung cấp dịch vụ bản đồ, tìm kiếm địa điểm và chuyển đổi địa chỉ thành tọa độ địa lý (Geocoding).
- Tích hợp **MapLibre GL JS** để hiển thị bản đồ và đánh dấu vị trí bất động sản trên giao diện Next.js.
- Cho phép người quản lý tìm kiếm địa chỉ và lựa chọn vị trí trực tiếp trên bản đồ khi tạo hoặc chỉnh sửa thông tin bất động sản.
- Lưu tọa độ địa lý vào PostgreSQL để phục vụ việc hiển thị và tìm kiếm theo vị trí.
- Quản lý quyền truy cập tài nguyên Location Service thông qua **AWS IAM**, đảm bảo chỉ các thành phần được cấp quyền mới có thể sử dụng dịch vụ.

Trong quá trình phát triển, em gặp một số khó khăn như cấu hình quyền truy cập AWS IAM, xử lý chữ ký xác thực **AWS Signature Version 4 (SigV4)** và tối ưu số lượng yêu cầu Geocoding khi người dùng nhập địa chỉ. Sau khi hoàn thiện các cấu hình và áp dụng kỹ thuật **Debounce** trong React, chức năng tìm kiếm và hiển thị vị trí hoạt động ổn định với trải nghiệm người dùng tốt hơn.

## Hình ảnh minh họa

![Overview](/images/3-BlogsPosted/Amazon_Location_Architecture.png)

## Tham khảo

- https://docs.aws.amazon.com/location/
- https://maplibre.org/maplibre-gl-js/docs/
- https://docs.aws.amazon.com/location/latest/developerguide/integrating-with-amplify.html