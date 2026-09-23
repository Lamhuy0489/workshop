---
title: "Cấu hình Amazon Cognito & Google OAuth 2.0"
date: 2026-09-24
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

### Mục tiêu thực hành

Thiết lập hệ thống xác thực người dùng tập trung bằng dịch vụ quản lý danh tính đám mây **Amazon Cognito User Pool** kết hợp cơ chế liên kết danh tính bên ngoài (**Federated Identity Provider**) qua giao thức **Google OAuth 2.0**, cho phép người dùng đăng nhập an toàn vào nền tảng Web Studio bằng tài khoản Google hoặc tài khoản email cục bộ mà không cần lưu trữ mật khẩu trực tiếp trên máy chủ.

---

## 1. Tổng quan về Amazon Cognito & Xác thực liên kết

Amazon Cognito là dịch vụ quản lý danh tính và kiểm soát truy cập an toàn, có khả năng mở rộng hàng triệu người dùng:
* **Amazon Cognito User Pool (`huylam-ocr-user-pool` / `User pool - qp0rmn`)**:
  * Đóng vai trò là thư mục người dùng tập trung (Identity Directory), hỗ trợ đăng ký, đăng nhập, khôi phục mật khẩu và cấp phát token định danh chuẩn OIDC / JWT (`id_token`, `access_token`, `refresh_token`).
  * Tích hợp miền ủy quyền chuyên biệt: `https://kc4iwg.auth.ap-southeast-1.amazoncognito.com`.
* **Cơ chế liên kết danh tính xã hội (Social Identity Provider - Google OAuth 2.0)**:
  * Cho phép người dùng đăng nhập một chạm (Single Sign-On - SSO) qua tài khoản Google có sẵn.
  * Cognito tự động đối soát thông tin qua OpenID Connect Callback URL (`/oauth2/idpresponse`), ánh xạ các thuộc tính người dùng (`email` và `sub`) và tự động tạo hồ sơ người dùng trong User Pool mà không phát sinh thêm bước cấu hình thủ công.
* **Tuân thủ tiêu chuẩn FinOps 0.00 USD**:
  * Amazon Cognito cung cấp hạn mức miễn phí vĩnh viễn (Always Free) lên tới 50,000 người dùng hoạt động hàng tháng (Monthly Active Users - MAU), hoàn toàn không phát sinh chi phí duy trì.

---

## 2. Các bước triển khai thực tế trên Google Cloud và AWS Management Console

### Bước 2.1: Cấu hình OAuth 2.0 Client trên Google Cloud Console
1. Truy cập **Google Cloud Console -> APIs & Services -> Credentials**.
2. Nhấp chọn **+ Create credentials -> OAuth client ID**:

![Khởi tạo OAuth Client ID trên Google Cloud Console](/images/cognito/01-google-console-oauth-credentials.png)

3. Cấu hình các thông số ủy quyền ứng dụng web:
   * **Application type**: Web application.
   * **Name**: Web client 1.
   * **Authorized JavaScript origins**:
     * `http://localhost:5000`
     * `http://127.0.0.1:5000`
     * `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`
   * **Authorized redirect URIs**:
     * `https://kc4iwg.auth.ap-southeast-1.amazoncognito.com/oauth2/idpresponse`
4. Nhấp nút **Save** và lưu lại **Client ID** cùng **Client secret** được cấp phát:

![Cấu hình URI chuyển hướng và lưu khóa bí mật trên Google Cloud Console](/images/cognito/06-google-console-redirect-uri-saved.png)

---

### Bước 2.2: Khởi tạo User Pool trên Amazon Cognito Console
1. Đăng nhập vào AWS Console tại khu vực **ap-southeast-1 (Singapore)**.
2. Tìm kiếm dịch vụ **Amazon Cognito -> Create user pool** (hoặc trình hướng dẫn thiết lập tài nguyên ứng dụng):
3. Nhập các thông số cơ bản:

| Thuộc tính (Attribute) | Giá trị cấu hình (Configured Value) | Ý nghĩa kỹ thuật |
| :--- | :--- | :--- |
| **Application type** | `Traditional web application` | Ứng dụng web máy chủ lưu trữ (Gunicorn / Flask / Python) |
| **Name your application** | `huylam-ocr-user-pool` | Định danh nhóm người dùng của ứng dụng |
| **Options for sign-in identifiers** | `Email` | Xác thực đăng nhập qua địa chỉ email |
| **Self-registration** | `Enable self-registration` | Cho phép người dùng tự do đăng ký tài khoản mới |

![Thiết lập thông số cơ bản cho ứng dụng trên Amazon Cognito](/images/cognito/02-cognito-setup-application-screen.png)

4. Xác nhận bảng thông tin hỗ trợ đăng nhập liên kết mạng xã hội và OpenID Connect (Social, SAML, or OIDC sign-in):

![Thông báo hỗ trợ đăng nhập liên kết Social và OIDC](/images/cognito/03-cognito-social-signin-info-popup.png)

5. Hoàn tất khởi tạo User Pool. Hệ thống tạo thành công nhóm người dùng `User pool - qp0rmn` và ứng dụng `huylam-ocr-user-pool`:

![Khởi tạo Amazon Cognito User Pool thành công](/images/cognito/04-cognito-user-pool-created-success.png)

---

### Bước 2.3: Kiểm tra trải nghiệm đăng nhập (Sign-in Experience)
1. Trong bảng điều khiển User Pool, chọn tab **Sign-in** để rà soát toàn diện:
   * **Cognito user pool sign-in**: Kích hoạt đăng nhập bằng Email.
   * **Options for choice-based sign-in**: Hỗ trợ đăng nhập bằng mật khẩu (Password).
   * **Device tracking & Account recovery**: Kích hoạt tự phục hồi tài khoản qua tin nhắn email tự động.

![Cấu hình trải nghiệm đăng nhập trong Amazon Cognito](/images/cognito/05-cognito-sign-in-experience-tab.png)

---

### Bước 2.4: Tích hợp Google làm Identity Provider liên kết
1. Tại cột điều hướng bên trái, nhấp chọn mục **Social and external providers -> Add identity provider**.
2. Chọn nhà cung cấp **Google** và điền các thông số kỹ thuật:
   * **Client ID**: `73148288621-r8m9svnv8iiisb6qr084to7fsrivvoc7.apps.googleusercontent.com`
   * **Client secret**: Khóa bí mật đã lưu từ Google Cloud Console.
   * **Authorized scopes**: `profile email openid`
3. Thiết lập bảng ánh xạ thuộc tính người dùng (**Attribute mapping**):
   * `email` (User pool attribute) <-> `email` (Google attribute)
   * `username` (User pool attribute) <-> `sub` (Google attribute)
4. Nhấp nút **Save changes**. Nhà cung cấp Google được kích hoạt thành công:

![Đăng ký thành công Google làm Identity Provider trong Cognito](/images/cognito/07-cognito-identity-provider-google-created.png)

---

### Bước 2.5: Nghiệm thu tính năng đăng nhập trên Web Studio
1. Truy cập vào cổng đăng nhập Web Studio tại máy chủ EC2 ALB (`http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com/login`) hoặc môi trường cục bộ:
2. Nút **Đăng nhập bằng tài khoản Google (AWS Cognito)** hiển thị nổi bật với logo Google chuẩn mực.
3. Khi người dùng nhấp chọn, trình duyệt chuyển hướng an toàn qua luồng ủy quyền Google OIDC và đăng nhập thành công vào giao diện xử lý tài liệu Web Studio:

![Giao diện cổng đăng nhập Web Studio tích hợp AWS Cognito](/images/cognito/08-web-studio-cognito-login-screen.png)

---

## 3. Kết quả mong đợi

Sau khi hoàn thành phần thực hành này, bạn sẽ có:
- Nhóm người dùng **Amazon Cognito User Pool** hoạt động ổn định tại khu vực Singapore (`ap-southeast-1`).
- Nhà cung cấp liên kết danh tính **Google OAuth 2.0** được tích hợp trơn tru, hỗ trợ ánh xạ thuộc tính tự động.
- Cổng đăng nhập Web Studio hỗ trợ đa phương thức xác thực: tài khoản nội bộ và đăng nhập liên kết Google SSO an toàn.
- Kiến trúc xác thực hoàn toàn Serverless, bảo mật cao và duy trì chi phí 0.00 USD trong suốt quá trình vận hành.
