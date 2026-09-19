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
* Khởi tạo máy chủ ảo Amazon EC2 chạy hệ điều hành Amazon Linux 2023 với cấu hình User Data tự động hóa cài đặt Apache HTTP Server.
* Thiết kế, phân quyền và gắn IAM Role cho Amazon EC2 truy cập an toàn tài nguyên Amazon S3 qua Instance Metadata Service (IMDS).
* Xác thực hoạt động toàn trình qua giao thức HTTP, trình duyệt web và dòng lệnh AWS CLI.

### Các công việc đã triển khai trong tuần 2:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2** | - Tìm hiểu Amazon S3 & Static Website Hosting.<br>- Tạo S3 Bucket `huylam-static-web-677994024390`.<br>- Tải tệp cổng thông tin `index.html`.<br>- Bật Static website hosting & Cấu hình Bucket Policy. | Bucket hoạt động ở chế độ Public Read; website tĩnh truy cập thành công qua Endpoint toàn cầu với mã phản hồi HTTP 200 OK. | [Lab 000057](https://000057.awsstudygroup.com) |
| **Thứ 3** | - Tìm hiểu IAM Role cho EC2 compute.<br>- Tạo IAM Role `huylam-ec2-s3-readonly-role` gắn policy `AmazonS3ReadOnlyAccess`.<br>- Gắn IAM Role vào EC2 Instance.<br>- Kiểm tra quyền truy cập S3 từ máy ảo qua AWS CLI bằng EC2 Instance Connect. | Hiểu nguyên lý cấp quyền tự động qua EC2 Instance Metadata Service (IMDS), truy vấn thành công S3 Bucket mà không lưu trữ cứng Access Key trên máy chủ. | [Lab 000048](https://000048.awsstudygroup.com) |
| **Thứ 4** | - Tìm hiểu EC2 User Data tự động hóa.<br>- Cấu hình Security Group mở cổng HTTP (80) và SSH (22).<br>- Khởi tạo máy chủ ảo EC2 `t3.micro` với bash script User Data tự cài đặt Apache httpd.<br>- Kiểm tra trang web qua Public IPv4. | Máy chủ ảo tự động triển khai dịch vụ Apache ngay khi khởi động; web server phản hồi HTTP 200 OK với thông tin sinh viên Lâm Quang Huy. | [Lab 000004](https://000004.awsstudygroup.com) |
| **Thứ 5** | - Cấu hình kiểm thử chính sách IAM Deny.<br>- Xác minh thứ tự ưu tiên của chính sách phân quyền AWS IAM. | Nắm rõ nguyên lý Explicit Deny luôn ghi đè Explicit Allow khi kiểm thử qua AWS CLI. | [Lab 000002](https://000002.awsstudygroup.com) |
| **Thứ 6** | - Khởi tạo Amazon RDS MySQL trong gói Free Tier.<br>- Cấu hình Security Group khép kín chỉ nhận kết nối từ EC2. | Cơ sở dữ liệu RDS đạt trạng thái Available, bảo đảm an toàn dữ liệu nội bộ. | [Lab 000005](https://000005.awsstudygroup.com) |
| **Thứ 7** | - Kết nối EC2 tới RDS MySQL.<br>- Triển khai ứng dụng đọc/ghi cơ sở dữ liệu.<br>- Dọn dẹp tài nguyên (Clean up) và tổng hợp báo cáo. | Hoàn tất kiến trúc 3 tầng (Web - App - DB), bảo toàn ngân sách Free Tier. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:

#### 1. Tài nguyên Amazon S3 (Lab 000057):
- **AWS Account ID**: `677994024390`
- **Tên tài khoản (Account Name)**: `huylam`
- **IAM User thực thi**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **Default Region**: `ap-southeast-1` (Singapore)
- **S3 Bucket Name**: `huylam-static-web-677994024390`
- **S3 Bucket ARN**: `arn:aws:s3:::huylam-static-web-677994024390`
- **Bucket Website Endpoint**: `http://huylam-static-web-677994024390.s3-website-ap-southeast-1.amazonaws.com`
- **Chính sách bảo mật (Bucket Policy)**: Cấp quyền `s3:GetObject` công khai cho tài nguyên tĩnh theo nguyên tắc Least Privilege.
- **Trạng thái xác thực CLI**: Lệnh `aws s3api get-bucket-policy` và `aws s3api get-bucket-website` đã kiểm tra thành công.

#### 2. Tài nguyên Amazon EC2 & Web Server (Lab 000004):
- **Instance Name**: `huylam-web-server`
- **Instance ID**: `i-0520ad41a8d6ce258`
- **Instance Type**: `t3.micro` (Thuộc hạn mức AWS Free Tier)
- **Hệ điều hành (AMI)**: `Amazon Linux 2023 AMI` (Kernel 6.1, Architecture x86_64)
- **VPC / Subnet**: Default VPC `vpc-0c84feaf395ece4dd` / Subnet `subnet-0497f256c54a58825` (`ap-southeast-1a`)
- **Public IPv4 Address**: `47.129.234.42`
- **Security Group**: `sg-0eb53b21a70a15a9c` (Cấu hình Inbound: TCP 22 SSH từ 0.0.0.0/0, TCP 80 HTTP từ 0.0.0.0/0)
- **Dịch vụ Web**: Apache HTTP Server `httpd 2.4.68` phản hồi mã trạng thái HTTP 200 OK.
- **Cơ chế tự động hóa**: EC2 User Data script tự động cập nhật hệ điều hành, cài đặt Apache httpd, kích hoạt dịch vụ systemd và khởi tạo trang index thông tin sinh viên Lâm Quang Huy.

#### 3. Cấu hình IAM Role cho EC2 (Lab 000048):
- **Role Name**: `huylam-ec2-s3-readonly-role`
- **Role ARN**: `arn:aws:iam::677994024390:role/huylam-ec2-s3-readonly-role`
- **Trusted Entity**: AWS Service `ec2.amazonaws.com` với hành động `sts:AssumeRole`.
- **Attached Policy**: AWS Managed Policy `AmazonS3ReadOnlyAccess` (`arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess`).
- **Phương thức kết nối & kiểm chứng**: Sử dụng EC2 Instance Connect để truy cập máy chủ qua giao diện điều khiển và thực thi thành công lệnh `aws s3 ls` liệt kê bucket `huylam-static-web-677994024390` mà không cần cấu hình Access Key tĩnh.

---

### Hình ảnh minh chứng hoàn thành thực tế (Proof of Work & Verifications)

#### Phần 1: Amazon S3 Static Website Hosting (Lab 000057)

##### 1. Khởi tạo S3 Bucket định danh toàn cầu
- **Mô tả**: Tạo bucket cá nhân `huylam-static-web-677994024390` tại vùng `ap-southeast-1` (Singapore), gắn liền với mã tài khoản chính chủ.
- **Vùng khoanh đỏ**: Thông tin tài khoản `huylam (677994024390)`, khu vực AWS Region và tên Bucket.

![Cấu hình khởi tạo S3 Bucket](/images/week2/01-create-bucket-config.png)

---

##### 2. Cấu hình gỡ bỏ Block Public Access có kiểm soát
- **Mô tả**: Vô hiệu hóa tính năng chặn truy cập công khai và xác nhận chấp thuận cảnh báo bảo mật theo quy chuẩn của dịch vụ Static Website Hosting.
- **Vùng khoanh đỏ**: Ô bỏ chọn *Block all public access* và ô tích chọn xác nhận rủi ro an toàn.

![Cấu hình Block Public Access](/images/week2/02-block-public-access-settings.png)

---

##### 3. Thiết lập mã hóa phía máy chủ (SSE-S3)
- **Mô tả**: Áp dụng cơ chế mã hóa mặc định Server-Side Encryption với khóa quản lý bởi Amazon S3 (SSE-S3) để bảo vệ toàn vẹn dữ liệu ở trạng thái lưu trữ.
- **Vùng khoanh đỏ**: Tùy chọn mã hóa *SSE-S3* và nút *Create bucket*.

![Thiết lập mã hóa SSE-S3](/images/week2/03-default-encryption-sse-s3.png)

---

##### 4. Xác nhận khởi tạo S3 Bucket thành công
- **Mô tả**: Hệ thống AWS ghi nhận và tạo lập thành công S3 Bucket định danh `huylam-static-web-677994024390`.
- **Vùng khoanh đỏ**: Thông báo xác nhận *Successfully created bucket* cùng tiêu đề Bucket chính chủ.

![Xác nhận tạo Bucket thành công](/images/week2/04-bucket-created-success.png)

---

##### 5. Tải tệp cổng thông tin `index.html` lên Bucket
- **Mô tả**: Tải tệp giao diện cổng thông tin đám mây `index.html` (17.5 KB) lên thư mục gốc của S3 Bucket.
- **Vùng khoanh đỏ**: Danh sách tệp tin hiển thị `index.html`, đường dẫn đích `s3://huylam-static-web-677994024390` và nút *Upload*.

![Tải tệp index.html](/images/week2/05-upload-index-html.png)

---

##### 6. Kích hoạt tính năng Static Website Hosting
- **Mô tả**: Cấu hình chế độ *Host a static website* và chỉ định tệp chỉ mục chính là `index.html`.
- **Vùng khoanh đỏ**: Lựa chọn *Enable*, chế độ *Host a static website* và trường tệp chỉ mục `index.html`.

![Kích hoạt Static Website Hosting](/images/week2/06-enable-static-hosting.png)

---

##### 7. Ghi nhận đường dẫn Bucket Website Endpoint
- **Mô tả**: Hệ thống sinh ra đường dẫn website toàn cầu tương ứng với vùng Singapore.
- **Vùng khoanh đỏ**: Thông báo cập nhật thành công, trạng thái *Enabled* và đường dẫn *Bucket website endpoint*.

![Đường dẫn Bucket Website Endpoint](/images/week2/07-static-hosting-endpoint.png)

---

##### 8. Thực thi chính sách bảo mật S3 Bucket Policy (JSON)
- **Mô tả**: Soạn thảo và lưu trữ chính sách phân quyền JSON cấp quyền `s3:GetObject` cho người dùng công khai đọc nội dung web tĩnh.
- **Vùng khoanh đỏ**: Trạng thái *Block all public access: Off*, thông báo cập nhật thành công và đoạn mã JSON Bucket policy.

![Cấu hình S3 Bucket Policy](/images/week2/08-bucket-policy-json.png)

---

##### 9. Xác thực website hoạt động thực tế trên toàn cầu
- **Mô tả**: Truy cập đường dẫn website qua trình duyệt thực tế, giao diện cổng thông tin hiển thị trạng thái *Production Status: Online* cùng đầy đủ thông tin định danh sinh viên Lâm Quang Huy.
- **Vùng khoanh đỏ**: Thanh địa chỉ trình duyệt, huy hiệu *Production Status: Online* và bảng thông tin định danh sinh viên.

![Xác thực website hoạt động thực tế](/images/week2/09-website-live-verification.png)

---

#### Phần 2: Cấu hình IAM Role cho máy chủ ảo Amazon EC2 (Lab 000048)

##### 10. Chọn thực thể tin cậy (Trusted Entity) cho IAM Role
- **Mô tả**: Thiết lập vai trò IAM Role mới với loại thực thể tin cậy là *AWS service* và Use Case chỉ định cho dịch vụ *EC2*, cho phép các máy ảo EC2 ủy quyền thực thi các lệnh AWS API.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thẻ chọn *AWS service* và Use Case *EC2*.

![Chọn thực thể tin cậy IAM Role](/images/week2/10-iam-role-trusted-entity.png)

---

##### 11. Gán chính sách phân quyền AmazonS3ReadOnlyAccess
- **Mô tả**: Tìm kiếm và gắn chính sách được quản lý bởi AWS `AmazonS3ReadOnlyAccess` vào IAM Role, tuân thủ nguyên tắc đặc quyền tối thiểu (chỉ cho phép đọc danh sách và nội dung đối tượng S3, không có quyền ghi hay xóa).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và hàng chính sách `AmazonS3ReadOnlyAccess` đã được tích chọn.

![Gán chính sách AmazonS3ReadOnlyAccess](/images/week2/11-iam-role-permissions-s3-readonly.png)

---

##### 12. Đặt tên IAM Role và kiểm tra Trust Policy
- **Mô tả**: Đặt tên Role là `huylam-ec2-s3-readonly-role`, kiểm tra lại cấu hình phân quyền và tài liệu JSON Trust Policy xác nhận quyền `sts:AssumeRole` dành riêng cho `ec2.amazonaws.com`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, ô nhập tên Role `huylam-ec2-s3-readonly-role` và khối JSON Trust policy.

![Đặt tên Role và xem trước Trust Policy](/images/week2/12-iam-role-name-review.png)

---

##### 13. Xác nhận khởi tạo IAM Role thành công
- **Mô tả**: Bảng điều khiển IAM Console hiển thị thông báo tạo mới thành công và ghi nhận IAM Role `huylam-ec2-s3-readonly-role` trong danh mục tài nguyên phân quyền.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thông báo màu xanh *Role huylam-ec2-s3-readonly-role created* và hàng dữ liệu tương ứng trong bảng danh mục IAM Role.

![Xác nhận tạo Role thành công](/images/week2/13-iam-role-created-success.png)

---

#### Phần 3: Khởi tạo Amazon EC2 Web Server với User Data tự động hóa (Lab 000004)

##### 14. Cấu hình Security Group mở cổng HTTP và SSH
- **Mô tả**: Thiết lập nhóm bảo mật mạng (Security Group) cho máy chủ ảo, định tuyến các quy tắc Inbound cho phép cổng 22 (SSH) quản trị và cổng 80 (HTTP) tiếp nhận lưu lượng truy cập web từ Internet.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, các quy tắc mở cổng SSH/HTTP trong *Inbound security groups rules* và bảng tóm tắt *Summary*.

![Cấu hình Security Group](/images/week2/14-ec2-launch-security-group.png)

---

##### 15. Thiết lập bash script tự động hóa trong EC2 User Data
- **Mô tả**: Nhập tập lệnh khởi tạo (bootstrap script) tại mục *Advanced details > User data* để tự động tải gói cập nhật, cài đặt dịch vụ Apache httpd, kích hoạt daemon hệ thống và tạo trang web hiển thị thẻ sinh viên Lâm Quang Huy ngay trong lần khởi động đầu tiên.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, khung nhập liệu mã nguồn *User data script* và thông tin tóm tắt *Summary*.

![Cấu hình EC2 User Data script](/images/week2/15-ec2-launch-user-data-script.png)

---

##### 16. Xác nhận khởi tạo máy chủ ảo EC2 thành công
- **Mô tả**: Hệ thống AWS phê duyệt lệnh khởi tạo và chuyển máy chủ ảo `huylam-web-server` mang mã định danh `i-0520ad41a8d6ce258` vào tiến trình kích hoạt tự động.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và thông báo màu xanh *Successfully initiated launch of instance (i-0520ad41a8d6ce258)*.

![Khởi tạo máy chủ EC2 thành công](/images/week2/16-ec2-launch-success.png)

---

#### Phần 4: Kiểm nghiệm dịch vụ Web Server và xác thực IAM Role (Lab 000004 & Lab 000048)

##### 17. Xác thực Web Server hoạt động thực tế qua Public IPv4
- **Mô tả**: Truy cập địa chỉ IP công khai của EC2 `http://47.129.234.42` bằng trình duyệt web. Máy chủ Apache HTTP Server 2.4.68 phản hồi mã trạng thái HTTP 200 OK, kết xuất hoàn chỉnh giao diện thẻ nhận diện sinh viên thực tập Lâm Quang Huy.
- **Vùng khoanh đỏ**: Thanh địa chỉ trình duyệt hiển thị IP `47.129.234.42` và thẻ hiển thị thông tin máy chủ ảo Amazon EC2 Apache Web Server.

![Xác thực Web Server qua trình duyệt](/images/week2/17-ec2-web-server-browser-verification.png)

---

##### 18. Kiểm nghiệm IAM Role qua EC2 Instance Connect với lệnh `aws s3 ls`
- **Mô tả**: Kết nối trực tiếp vào phiên dòng lệnh Linux của máy chủ `i-0520ad41a8d6ce258` bằng dịch vụ an toàn EC2 Instance Connect. Thực thi lệnh `aws s3 ls` để kiểm tra quyền truy cập lưu trữ S3. Hệ thống trả về thành công S3 Bucket `huylam-static-web-677994024390` thông qua IAM Role và IMDS mà hoàn toàn không cần lưu trữ cứng bất kỳ Access Key hay Secret Key nào trên máy chủ.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, kết quả truy vấn lệnh `aws s3 ls` trong terminal và thanh trạng thái định danh instance `i-0520ad41a8d6ce258`.

![Kiểm nghiệm IAM Role qua EC2 Instance Connect](/images/week2/18-ec2-instance-connect-s3-ls.png)