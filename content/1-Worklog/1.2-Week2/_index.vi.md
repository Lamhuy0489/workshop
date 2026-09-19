---
title: "Worklog Tuần 2"
date: 2026-09-19
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:
* Tìm hiểu kiến trúc lưu trữ đối tượng và cơ chế bảo mật trên Amazon S3.
* Thực hành khởi tạo S3 Bucket định danh toàn cầu, gỡ bỏ Block Public Access có kiểm soát.
* Cấu hình và kích hoạt tính năng Static Website Hosting không máy chủ (Serverless Web Hosting).
* Soạn thảo và thực thi chính sách phân quyền S3 Bucket Policy (JSON) theo nguyên tắc đặc quyền tối thiểu (Least Privilege).
* Xác thực hoạt động toàn trình qua giao thức HTTP và dòng lệnh AWS CLI.
* Khám phá các dịch vụ máy chủ ảo Amazon EC2, EC2 User Data, IAM Role và Amazon RDS MySQL.

### Các công việc đã triển khai trong tuần 2:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2** | - Tìm hiểu Amazon S3 & Static Website Hosting.<br>- Tạo S3 Bucket `huylam-static-web-677994024390`.<br>- Tải tệp cổng thông tin `index.html`.<br>- Bật Static website hosting & Cấu hình Bucket Policy. | Bucket hoạt động ở chế độ Public Read; website tĩnh truy cập thành công qua Endpoint toàn cầu với mã phản hồi HTTP 200 OK. | [Lab 000057](https://000057.awsstudygroup.com) |
| **Thứ 3** | - Tìm hiểu IAM Role cho EC2.<br>- Gán IAM Role cho máy chủ ảo.<br>- Kiểm tra quyền truy cập S3 từ máy ảo qua AWS CLI. | Hiểu nguyên lý cấp quyền tự động qua EC2 Instance Metadata Service (IMDS), không lưu trữ cứng Access Key trên máy tính. | [Lab 000048](https://000048.awsstudygroup.com) |
| **Thứ 4** | - Tìm hiểu EC2 User Data.<br>- Khởi tạo EC2 với bash script cài đặt Web Server tự động.<br>- Kiểm tra trang web qua Public IPv4. | Máy chủ ảo tự động triển khai dịch vụ Apache ngay khi khởi động. | [Lab 000004](https://000004.awsstudygroup.com) |
| **Thứ 5** | - Cấu hình kiểm thử chính sách IAM Deny.<br>- Xác minh thứ tự ưu tiên của chính sách phân quyền AWS IAM. | Nắm rõ nguyên lý Explicit Deny luôn ghi đè Explicit Allow khi kiểm thử qua AWS CLI. | [Lab 000002](https://000002.awsstudygroup.com) |
| **Thứ 6** | - Khởi tạo Amazon RDS MySQL trong gói Free Tier.<br>- Cấu hình Security Group khép kín chỉ nhận kết nối từ EC2. | Cơ sở dữ liệu RDS đạt trạng thái Available, bảo đảm an toàn dữ liệu nội bộ. | [Lab 000005](https://000005.awsstudygroup.com) |
| **Thứ 7** | - Kết nối EC2 tới RDS MySQL.<br>- Triển khai ứng dụng đọc/ghi cơ sở dữ liệu.<br>- Dọn dẹp tài nguyên (Clean up) và tổng hợp báo cáo. | Hoàn tất kiến trúc 3 tầng (Web - App - DB), bảo toàn ngân sách Free Tier. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:
- **AWS Account ID**: `677994024390`
- **Tên tài khoản (Account Name)**: `huylam`
- **IAM User thực thi**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **Default Region**: `ap-southeast-1` (Singapore)
- **S3 Bucket Name**: `huylam-static-web-677994024390`
- **S3 Bucket ARN**: `arn:aws:s3:::huylam-static-web-677994024390`
- **Bucket Website Endpoint**: `http://huylam-static-web-677994024390.s3-website-ap-southeast-1.amazonaws.com`
- **Chính sách bảo mật (Bucket Policy)**: Cấp quyền `s3:GetObject` công khai cho tài nguyên tĩnh.
- **Trạng thái xác thực CLI**: Lệnh `aws s3api get-bucket-policy` và `aws s3api get-bucket-website` đã kiểm tra thành công.

---

### Hình ảnh minh chứng hoàn thành thực tế (Proof of Work & Verifications)

#### 1. Khởi tạo S3 Bucket định danh toàn cầu
- **Mô tả**: Tạo bucket cá nhân `huylam-static-web-677994024390` tại vùng `ap-southeast-1` (Singapore), gắn liền với mã tài khoản chính chủ.
- **Vùng khoanh đỏ**: Thông tin tài khoản `huylam (677994024390)`, khu vực AWS Region và tên Bucket.

![Cấu hình khởi tạo S3 Bucket](/images/week2/01-create-bucket-config.png)

---

#### 2. Cấu hình gỡ bỏ Block Public Access có kiểm soát
- **Mô tả**: Vô hiệu hóa tính năng chặn truy cập công khai và xác nhận chấp thuận cảnh báo bảo mật theo quy chuẩn của dịch vụ Static Website Hosting.
- **Vùng khoanh đỏ**: Ô bỏ chọn *Block all public access* và ô tích chọn xác nhận rủi ro an toàn.

![Cấu hình Block Public Access](/images/week2/02-block-public-access-settings.png)

---

#### 3. Thiết lập mã hóa phía máy chủ (SSE-S3)
- **Mô tả**: Áp dụng cơ chế mã hóa mặc định Server-Side Encryption với khóa quản lý bởi Amazon S3 (SSE-S3) để bảo vệ toàn vẹn dữ liệu ở trạng thái lưu trữ.
- **Vùng khoanh đỏ**: Tùy chọn mã hóa *SSE-S3* và nút *Create bucket*.

![Thiết lập mã hóa SSE-S3](/images/week2/03-default-encryption-sse-s3.png)

---

#### 4. Xác nhận khởi tạo S3 Bucket thành công
- **Mô tả**: Hệ thống AWS ghi nhận và tạo lập thành công S3 Bucket định danh `huylam-static-web-677994024390`.
- **Vùng khoanh đỏ**: Thông báo xác nhận *Successfully created bucket* cùng tiêu đề Bucket chính chủ.

![Xác nhận tạo Bucket thành công](/images/week2/04-bucket-created-success.png)

---

#### 5. Tải tệp cổng thông tin `index.html` lên Bucket
- **Mô tả**: Tải tệp giao diện cổng thông tin đám mây `index.html` (17.5 KB) lên thư mục gốc của S3 Bucket.
- **Vùng khoanh đỏ**: Danh sách tệp tin hiển thị `index.html`, đường dẫn đích `s3://huylam-static-web-677994024390` và nút *Upload*.

![Tải tệp index.html](/images/week2/05-upload-index-html.png)

---

#### 6. Kích hoạt tính năng Static Website Hosting
- **Mô tả**: Cấu hình chế độ *Host a static website* và chỉ định tệp chỉ mục chính là `index.html`.
- **Vùng khoanh đỏ**: Lựa chọn *Enable*, chế độ *Host a static website* và trường tệp chỉ mục `index.html`.

![Kích hoạt Static Website Hosting](/images/week2/06-enable-static-hosting.png)

---

#### 7. Ghi nhận đường dẫn Bucket Website Endpoint
- **Mô tả**: Hệ thống sinh ra đường dẫn website toàn cầu tương ứng với vùng Singapore.
- **Vùng khoanh đỏ**: Thông báo cập nhật thành công, trạng thái *Enabled* và đường dẫn *Bucket website endpoint*.

![Đường dẫn Bucket Website Endpoint](/images/week2/07-static-hosting-endpoint.png)

---

#### 8. Thực thi chính sách bảo mật S3 Bucket Policy (JSON)
- **Mô tả**: Soạn thảo và lưu trữ chính sách phân quyền JSON cấp quyền `s3:GetObject` cho người dùng công khai đọc nội dung web tĩnh.
- **Vùng khoanh đỏ**: Trạng thái *Block all public access: Off*, thông báo cập nhật thành công và đoạn mã JSON Bucket policy.

![Cấu hình S3 Bucket Policy](/images/week2/08-bucket-policy-json.png)

---

#### 9. Xác thực website hoạt động thực tế trên toàn cầu
- **Mô tả**: Truy cập đường dẫn website qua trình duyệt thực tế, giao diện cổng thông tin hiển thị trạng thái *Production Status: Online* cùng đầy đủ thông tin định danh sinh viên Lâm Quang Huy.
- **Vùng khoanh đỏ**: Thanh địa chỉ trình duyệt, huy hiệu *Production Status: Online* và bảng thông tin định danh sinh viên.

![Xác thực website hoạt động thực tế](/images/week2/09-website-live-verification.png)