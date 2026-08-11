---
title: "Khởi tạo Amazon Cognito User Pool"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Tạo User Pool trên Bảng điều khiển AWS

### Bước 1: Truy cập và khởi tạo
1. Truy cập [Bảng điều khiển Amazon Cognito](https://console.aws.amazon.com/cognito/v2/home).
2. Tại menu điều hướng bên trái, chọn **User pools**.
3. Nhấn nút **Create user pool** ở góc trên bên phải để bắt đầu quá trình cấu hình.

![Cognito Sign-in Options](/images/5-Workshop/5.3-Cognito-Auth/5.3.1-signin-options.png)

### Bước 2: Đặt tên ứng dụng
1. Nhập tên ứng dụng đại diện của bạn vào mục **Name your application**.
2. Ví dụ tên đề xuất: `real-estate-saas-web-app` hoặc `My SPA app - real-estate`
3. Quy tắc: tối đa **128 ký tự**, chấp nhận **chữ cái, chữ số, khoảng trắng** và các ký tự đặc biệt `+ = , . @ -`.

![Overview](/images/5-Workshop/5.3-Cognito-Auth/5.3.2-set-up-name.png)

### Bước 3: Cấu hình
1. Tại mục **Options for sign-in identifiers**, tích chọn checkbox **Email** để cho phép người dùng đăng nhập bằng địa chỉ email.
2. Tại mục **Self-registration**, tích chọn **Enable self-registration** để cho phép người dùng tự đăng ký tài khoản từ giao diện ứng dụng.
3. Tại mục **Required attributes for sign-up**, nhấn vào ô dropdown **Select attributes** và chọn các thuộc tính bắt buộc người dùng phải cung cấp khi đăng ký (ví dụ: `email`, `name`).
4. *(Tùy chọn)* Tại mục **Add a return URL - optional**, nhập đường dẫn URL mà Cognito sẽ chuyển hướng (redirect) người dùng quay trở lại ứng dụng sau khi đăng nhập thành công qua Hosted UI.
5. Nhấn nút **Create user directory** ở góc dưới bên phải để hoàn tất việc khởi tạo Cognito User Pool.

![Overview](/images/5-Workshop/5.3-Cognito-Auth/5.3.3-configure-options.png)

---

#### 2. Lưu thông tin Pool ID & Client ID

Sau khi khởi tạo thành công, sao chép **User pool ID** (ví dụ: `us-east-1_abc123XYZ`) và **App client ID** (ví dụ: `71a8bc...`), sau đó lưu vào file `.env`:

```env
AWS_REGION=us-east-1
COGNITO_USER_POOL_ID=us-east-1_abc123XYZ
COGNITO_CLIENT_ID=71a8bc...
```
