---
title: "Worklog Tuần 6"
date: 2026-09-21
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:
* Nghiên cứu và làm chủ bộ giải pháp quản trị máy chủ từ xa **AWS Systems Manager (SSM)**: Fleet Manager, Session Manager, Parameter Store và Run Command theo chuẩn Zero Trust Architecture.
* Triển khai kiến trúc bảo mật máy chủ ảo **Zero Inbound Ports**: Cấu hình Security Group `huylam-ssm-sg` hoàn toàn không mở cổng Inbound (0 Inbound Rules, không mở cổng SSH 22 truyền thống), triệt tiêu hoàn toàn bề mặt tấn công từ Internet.
* Thiết lập phân quyền IAM Role chuyên dụng `huylam-ssm-role`: Gắn chính sách `AmazonSSMManagedInstanceCore` và `AmazonSSMReadOnlyAccess` cho phép máy chủ EC2 tự động đăng ký và liên lạc hai chiều an toàn với Systems Manager Control Plane qua SSM Agent.
* Kiểm nghiệm kết nối từ xa không cần SSH Key thông qua **AWS Systems Manager Session Manager**: Truy cập shell Linux tương tác trực tiếp trên trình duyệt web, xác thực phiên làm việc dưới quyền người dùng an toàn `ssm-user`.
* Quản trị tập trung tham số và bí mật ứng dụng với **AWS Systems Manager Parameter Store**: Khởi tạo tham số chuỗi thông thường (String) và tham số mã hóa bảo mật cao (SecureString) tích hợp khóa mã hóa AWS Key Management Service (AWS KMS `alias/aws/ssm`).
* Kiểm chứng giải mã tham số bảo mật động ngay trong phiên Session Manager: Sử dụng AWS CLI thực thi lệnh `aws ssm get-parameter --with-decryption` trích xuất thông tin định danh sinh viên Lâm Quang Huy (MSSV: `0212267`) và mật khẩu cơ sở dữ liệu đã giải mã.
* Tự động hóa tác vụ quản trị hàng loạt bằng **AWS Systems Manager Run Command**: Thực thi tài liệu lệnh `AWS-RunShellScript` trên máy chủ từ xa mà không cần đăng nhập trực tiếp, thu thập kết quả thực thi đạt trạng thái `Success` và kiểm tra nhật ký chi tiết (Standard Output).
* Thiết lập cơ chế quản trị tài nguyên tập trung với **AWS Resource Groups & Tagging**: Xây dựng Resource Group `huylam-fcj-resources` gom cụm tự động 6 tài nguyên dựa trên thẻ phân loại chuẩn hóa `Project = FCJ-Bootcamp-2026`.
* Duy trì kỷ luật tài chính đám mây **FinOps**: Đo kiểm định kỳ trang tổng quan AWS Billing and Cost Management, ghi nhận chi phí lũy kế Month-to-date (0.10 USD), kiểm soát 2 chỉ tiêu ngân sách AWS Budgets đạt trạng thái `Healthy` và giải phóng tài nguyên sau thực nghiệm.

---

### Các công việc đã triển khai trong tuần 6:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2** | - Nghiên cứu tổng quan dịch vụ AWS Systems Manager (SSM).<br>- Tìm hiểu cơ chế hoạt động của SSM Agent trên hệ điều hành Linux.<br>- Phân tích mô hình bảo mật Zero Trust: Loại bỏ Bastion Host và cổng SSH 22 công khai. | Nắm vững kiến trúc quản trị máy chủ qua kênh truyền mã hóa TLS ra ngoài (Outbound HTTPS 443) tới Systems Manager Service Endpoints. | [AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/) |
| **Thứ 3** | - Khởi tạo IAM Role `huylam-ssm-role` với các quyền quản trị SSM.<br>- Khởi tạo Security Group `huylam-ssm-sg` (`sg-08a93d881545a9cf8`) không có bất kỳ quy tắc Inbound nào.<br>- Triển khai máy chủ EC2 `huylam-ssm-instance` (`i-07c150e87aa231c61`) trong VPC `huylam-vpc`. | Máy chủ ảo tự động đăng ký thành công vào SSM Fleet Manager với trạng thái kết nối `Online`. | [SSM IAM Policies](https://docs.aws.amazon.com/systems-manager/latest/userguide/security-iam-awsmanpolicies.html) |
| **Thứ 4** | - Kiểm tra tính năng AWS Systems Manager Session Manager.<br>- Kết nối shell tương tác vào máy chủ từ bảng điều khiển AWS Console.<br>- Đo kiểm môi trường Linux: Kiểm tra định danh `ssm-user`, đường dẫn làm việc, phiên bản nhân Linux và xuất thông tin sinh viên Lâm Quang Huy (MSSV: 0212267). | Thiết lập kết nối thành công 100% qua trình duyệt mà không cần sử dụng tệp khóa riêng tư SSH (.pem / .ppk). | [Session Manager Documentation](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) |
| **Thứ 5** | - Nghiên cứu cơ chế quản trị cấu hình AWS Systems Manager Parameter Store.<br>- Tạo tham số String `/huylam/app/environment` và `/huylam/app/student_name`.<br>- Khởi tạo tham số bí mật SecureString `/huylam/app/db_password` mã hóa bằng AWS KMS Default Key `alias/aws/ssm`. | Phân tách hoàn toàn dữ liệu cấu hình và bí mật ra khỏi mã nguồn ứng dụng, tuân thủ nguyên tắc 12-Factor App. | [SSM Parameter Store Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) |
| **Thứ 6** | - Thực nghiệm trích xuất và giải mã dữ liệu tham số từ Session Manager terminal.<br>- Gọi AWS CLI SSM API để đọc tham số định danh sinh viên.<br>- Gọi AWS CLI SSM API với cờ `--with-decryption` giải mã an toàn mật khẩu cơ sở dữ liệu `HuyLam2026!SecureDBPassword`. | Xác thực thành công năng lực giải mã tham số ứng dụng theo thời gian thực dựa trên quyền IAM Instance Profile. | [AWS KMS with Parameter Store](https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html) |
| **Thứ 7** | - Cấu hình và thực thi AWS Systems Manager Run Command.<br>- Chọn tài liệu điều khiển `AWS-RunShellScript` nhắm mục tiêu máy chủ `i-07c150e87aa231c61`.<br>- Chạy script thu thập thông tin phần cứng, mạng và định danh sinh viên Lâm Quang Huy. | Lệnh thực thi đạt mã trạng thái `Success`, ghi nhận toàn bộ Standard Output trực tiếp trên AWS Management Console. | [SSM Run Command Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) |
| **Chủ Nhật**| - Thiết lập AWS Resource Group `huylam-fcj-resources` gom cụm tự động 6 tài nguyên qua tag `Project = FCJ-Bootcamp-2026`.<br>- Kiểm toán chi phí FinOps trên Billing Dashboard: MTD 0.10 USD, 2 Budgets đạt chuẩn Healthy.<br>- Thực hiện quy trình FinOps Teardown: Thu hồi và giải phóng toàn bộ tài nguyên nhằm bảo toàn ngân sách Free Tier.<br>- Biên tập hoàn thiện tài liệu kỹ thuật và báo cáo minh chứng. | Hoàn thành xuất sắc toàn bộ nội dung thực hành Tuần 6 và các bài Lab liên quan, duy trì mức phát sinh chi phí 0 USD. | [AWS Resource Groups Guide](https://docs.aws.amazon.com/ARG/latest/userguide/welcome.html) |

---

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:

#### 1. Định danh tài khoản & Khu vực (Identity & Region):
- **AWS Account ID**: `677994024390`
- **Tên tài khoản (Account Name)**: `huylam`
- **IAM User thực thi**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **VPC trực thuộc**: `huylam-vpc` (`vpc-0125f4d6db3fbffa6`)
- **Subnet thực thi**: `subnet-0efa7c3a5818035dc` (Public Subnet)

#### 2. Phân quyền danh tính IAM Role chuyên dụng (IAM Role & Policies):
- **Tên IAM Role**: `huylam-ssm-role`
- **Role ARN**: `arn:aws:iam::677994024390:role/huylam-ssm-role`
- **Thực thể ủy thác (Trusted Entity)**: `ec2.amazonaws.com`
- **Chính sách phân quyền gắn kèm (Attached Policies)**:
  - `AmazonSSMManagedInstanceCore`: Cho phép phiên bản EC2 sử dụng các chức năng cốt lõi của dịch vụ AWS Systems Manager (Fleet Manager, Session Manager, Run Command).
  - `AmazonSSMReadOnlyAccess`: Cung cấp quyền đọc thông tin cấu hình và giải mã tham số từ Parameter Store.

#### 3. Nhóm bảo mật tối ưu hóa Zero Trust (Security Group):
- **Tên Security Group**: `huylam-ssm-sg`
- **Security Group ID**: `sg-08a93d881545a9cf8`
- **Mô tả cấu hình**: `Security Group for Systems Manager - Zero Inbound SSH - Lam Quang Huy 0212267`
- **Quy tắc đầu vào (Inbound Rules)**: **0 quy tắc (No inbound rules)**. Không mở bất kỳ cổng nào từ bên ngoài, bao gồm cổng 22 (SSH), 80 (HTTP) hay 443 (HTTPS).
- **Quy tắc đầu ra (Outbound Rules)**: Cho phép toàn bộ lưu lượng Outbound (`All traffic`, `0.0.0.0/0`) để SSM Agent thiết lập kết nối an toàn chiều ra tới các endpoint của AWS Systems Manager thông qua giao thức TLS.

#### 4. Máy chủ ảo được quản lý (Managed EC2 Instance):
- **Tên phiên bản (Name Tag)**: `huylam-ssm-instance`
- **Instance ID**: `i-07c150e87aa231c61`
- **Phân hạng phần cứng**: `t3.micro` (2 vCPUs, 1 GiB RAM)
- **Địa chỉ IP riêng (Private IPv4)**: `10.0.1.240`
- **Địa chỉ IP công cộng (Public IPv4)**: `13.212.80.147`
- **Hệ điều hành**: Amazon Linux 2023 (Kernel 6.18.48-109.150.amzn2023.x86_64)
- **Trạng thái SSM Agent (Ping Status)**: `Online`
- **Phiên bản SSM Agent**: `3.3.4624.0`
- **Trạng thái kết nối Session Manager**: `Connected`

#### 5. Quản trị tham số tập trung (SSM Parameter Store):
- **Tham số môi trường ứng dụng**:
  - Tên tham số: `/huylam/app/environment`
  - Kiểu dữ liệu: `String`
  - Phân hạng: `Standard Tier`
  - Giá trị: `Production-Bootcamp`
- **Tham số định danh sinh viên**:
  - Tên tham số: `/huylam/app/student_name`
  - Kiểu dữ liệu: `String`
  - Phân hạng: `Standard Tier`
  - Giá trị: `Lam Quang Huy - MSSV: 0212267 - Class: 67CS`
- **Tham số bí mật bảo mật cao (Database Password)**:
  - Tên tham số: `/huylam/app/db_password`
  - Kiểu dữ liệu: `SecureString`
  - Phân hạng: `Standard Tier`
  - Khóa mã hóa (KMS Key): Khóa mặc định AWS KMS `alias/aws/ssm`
  - Giá trị mã hóa an toàn: `HuyLam2026!SecureDBPassword`
  - Thẻ định danh (Tags): `Project = FCJ-Bootcamp-2026`, `StudentID = 0212267`

#### 6. Điều khiển và tự động hóa tác vụ (SSM Run Command):
- **Command ID**: `ab82dba7-47c6-4727-9a72-ffe6d89b1598`
- **Tài liệu lệnh (Command Document)**: `AWS-RunShellScript`
- **Mục tiêu thực thi (Target)**: `i-07c150e87aa231c61` (`huylam-ssm-instance`)
- **Trạng thái thực thi (Status)**: `Success` (Response code: 0)
- **Nội dung kết quả trả về (Standard Output)**:
  ```text
  === AWS SYSTEMS MANAGER RUN COMMAND LAB ===
  Student Name: Lam Quang Huy
  Student ID: 0212267
  Class: 67CS - HUCE
  Host: ip-10-0-1-240.ap-southeast-1.compute.internal
  Current User: ssm-user
  Kernel: 6.18.48-109.150.amzn2023.x86_64
  SSM Agent Status: Active (running)
  Uptime: up 18 minutes
  ```

#### 7. Gom cụm và quản trị tài nguyên (AWS Resource Groups):
- **Tên Resource Group**: `huylam-fcj-resources`
- **Resource Group ARN**: `arn:aws:resource-groups:ap-southeast-1:677994024390:group/huylam-fcj-resources`
- **Mô tả**: `Resource Group for student Lam Quang Huy 0212267 - FCJ Bootcamp 2026`
- **Tiêu chí nhóm (Grouping Criteria)**: Lọc theo cặp khóa-giá trị thẻ `Project = FCJ-Bootcamp-2026`
- **Số lượng tài nguyên thành viên (Group Resources)**: 6 tài nguyên, bao gồm:
  1. Parameter: `/huylam/app/student_name` (SSM Parameter)
  2. Parameter: `/huylam/app/db_password` (SSM Parameter)
  3. Security Group: `huylam-ssm-sg` (`sg-08a93d881545a9cf8`)
  4. EC2 Instance: `huylam-ssm-instance` (`i-07c150e87aa231c61`)
  5. EC2 Instance phụ trợ: `huylam-ssm-instance` (`i-0a5174023e7d2f6dc`)
  6. Resource Group: `huylam-fcj-resources` (Bản thân nhóm tài nguyên)

#### 8. Quản trị tài chính đám mây FinOps (Billing & Cost Management):
- **Chi phí phát sinh lũy kế trong tháng (Month-to-date - MTD)**: `0.10 USD`
- **Dự báo tổng chi phí cuối tháng (Month-end Forecast)**: `0.26 USD`
- **Trạng thái ngân sách cảnh báo (AWS Budgets)**: 2 ngân sách thiết lập hoạt động bình thường, trạng thái `OK / Healthy`.
- **Phát hiện dị thường chi phí (Cost Anomaly Detection)**: Trạng thái `None detected`.
- **Đánh giá FinOps**: Toàn bộ hệ thống máy chủ và dịch vụ cấu hình trong bài thực hành đều nằm trong định mức miễn phí (AWS Free Tier), không phát sinh bất kỳ khoản phí vượt ngưỡng nào.

---

### Hình ảnh minh chứng triển khai thực tế trên AWS:

Tất cả các hình ảnh minh chứng dưới đây đều được trích xuất trực tiếp từ các phiên làm việc trên AWS Management Console và cửa sổ dòng lệnh AWS Systems Manager thực tế của sinh viên **Lâm Quang Huy (MSSV: 0212267)**. Các khu vực trọng yếu gồm huy hiệu tài khoản `huylam (677994024390)`, khu vực Singapore `ap-southeast-1` và các thông số kỹ thuật cốt lõi đều được đóng khung viền đỏ chuẩn xác:

#### 1. Máy chủ EC2 kết nối thành công vào Systems Manager Fleet Manager:
- **Mô tả**: Giao diện AWS Systems Manager Fleet Manager hiển thị phiên bản máy chủ `i-07c150e87aa231c61` (`huylam-ssm-instance`) đã đăng ký thành công với tư cách là một nút được quản lý (Managed Node), trạng thái kết nối `Online`, hệ điều hành Amazon Linux 2023.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và hàng bản ghi máy chủ ảo `i-07c150e87aa231c61` với trạng thái `Online`.

![Fleet Manager Managed Nodes](/images/week6/01-ssm-fleet-manager-managed-node.png)

---

#### 2. Cấu hình Security Group chuẩn Zero Trust - 0 Inbound Rules:
- **Mô tả**: Bảng điều khiển Amazon EC2 Security Group `huylam-ssm-sg` (`sg-08a93d881545a9cf8`). Nhóm bảo mật hoàn toàn không có quy tắc Inbound (Inbound rules count: 0), chứng minh phiên bản máy chủ không mở cổng SSH 22 nhưng vẫn được quản trị thông suốt qua Systems Manager.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thẻ thông tin chi tiết Security Group ID và bảng Inbound rules hiển thị `No security group rules found`.

![Security Group 0 Inbound Rules](/images/week6/02-ec2-security-group-no-inbound.png)

---

#### 3. Giao diện kết nối EC2 qua SSM Session Manager:
- **Mô tả**: Trang kết nối máy chủ EC2 Connect to Linux instance, chọn phương thức kết nối an toàn `SSM Session Manager`. Bảng điều khiển xác nhận SSM agent Ping status đạt `Online`, Session Manager connection status đạt `Connected`, gắn IAM role `huylam-ssm-role` và nút kết nối `Connect` màu cam.
- **Vùng khoanh đỏ**: Thanh điều hướng phiên bản máy chủ `i-07c150e87aa231c61`, thẻ lựa chọn phương thức kết nối `SSM Session Manager`, thẻ thông tin trạng thái `SSM agent info` và nút bấm `Connect`.

![SSM Session Manager Connect](/images/week6/03-ssm-session-manager-connect.png)

---

#### 4. Thực thi dòng lệnh trên cửa sổ dòng lệnh tương tác Session Manager:
- **Mô tả**: Cửa sổ phiên làm việc tương tác dòng lệnh trực tiếp trong trình duyệt web qua Session Manager. Thực thi các lệnh xác thực môi trường: `whoami` (trả về `ssm-user`), `pwd`, `uname -r` và xuất chuỗi thông tin định danh sinh viên: `Student: Lam Quang Huy - MSSV: 0212267 - Class: 67CS - Zero Inbound SSH via SSM`.
- **Vùng khoanh đỏ**: Mã phiên và Instance ID `i-07c150e87aa231c61 (huylam-ssm-instance)` trên thanh tiêu đề và khối lệnh thực thi hiển thị thông tin sinh viên trong terminal.

![Session Manager Terminal Commands](/images/week6/04-ssm-session-manager-commands.png)

---

#### 5. Khởi tạo tham số chuỗi định danh trên Parameter Store:
- **Mô tả**: Bảng điều khiển AWS Systems Manager Parameter Store tạo tham số chuỗi `/huylam/app/student_name` thuộc loại `String`, phân hạng `Standard Tier`, lưu trữ giá trị định danh: `Lam Quang Huy - MSSV: 0212267 - Class: 67CS`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, trường nhập tên tham số `/huylam/app/student_name` và trường nhập giá trị tham số chứa thông tin sinh viên.

![Parameter Store Create String](/images/week6/05-ssm-parameter-store-create-string.png)

---

#### 6. Khởi tạo tham số bí mật SecureString tích hợp AWS KMS:
- **Mô tả**: Bảng điều khiển Parameter Store tạo tham số bảo mật `/huylam/app/db_password` thuộc loại `SecureString`, sử dụng khóa mã hóa mặc định của AWS KMS `alias/aws/ssm` và gắn thẻ quản trị `Project = FCJ-Bootcamp-2026`, `StudentID = 0212267`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, khu vực cấu hình khóa KMS `alias/aws/ssm` và bảng thẻ gắn kèm (Tags).

![Parameter Store SecureString Details](/images/week6/06-ssm-parameter-store-details.png)

---

#### 7. Giải mã tham số bảo mật SecureString qua Session Manager Terminal:
- **Mô tả**: Cửa sổ dòng lệnh Session Manager thực hiện đọc tham số chuỗi và giải mã tham số bí mật theo thời gian thực. Lệnh `aws ssm get-parameter --name "/huylam/app/db_password" --with-decryption` giải mã thành công mật khẩu `HuyLam2026!SecureDBPassword` thông qua KMS integration.
- **Vùng khoanh đỏ**: Mã phiên và Instance ID trên thanh tiêu đề cùng khối câu lệnh AWS CLI và kết quả giải mã hiển thị trong cửa sổ terminal.

![Session Manager Parameter Decryption](/images/week6/07-ssm-parameter-decryption-test.png)

---

#### 8. Soạn thảo và cấu hình tác vụ AWS Systems Manager Run Command:
- **Mô tả**: Bảng điều khiển Run Command chọn tài liệu `AWS-RunShellScript` để thực thi tập lệnh quản trị từ xa trên máy chủ đích `huylam-ssm-instance` mà không cần duy trì phiên đăng nhập tương tác.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, tài liệu lệnh `AWS-RunShellScript` được lựa chọn và khung soạn thảo nội dung tập lệnh bash shell.

![SSM Run Command Submit](/images/week6/08-ssm-run-command-submit.png)

---

#### 9. Kết quả thực thi Run Command đạt trạng thái Success:
- **Mô tả**: Kết quả thực thi lệnh điều khiển mã `ab82dba7-47c6-4727-9a72-ffe6d89b1598` trên máy chủ `i-07c150e87aa231c61` đạt trạng thái `Success` (mã phản hồi 0). Khối kết quả chuẩn (Output) in đầy đủ thông tin sinh viên Lâm Quang Huy (MSSV: 0212267) và trạng thái hệ thống.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thẻ trạng thái lệnh `Step 1 - Command description and status` đạt `Success` và khối hiển thị kết quả chuẩn `Output`.

![SSM Run Command Output Success](/images/week6/09-ssm-run-command-output-success.png)

---

#### 10. Khởi tạo nhóm tài nguyên AWS Resource Groups dựa trên thẻ:
- **Mô tả**: Bảng điều khiển AWS Resource Groups khởi tạo nhóm tài nguyên `huylam-fcj-resources` với tiêu chí phân loại dựa trên thẻ (Tag-based grouping criteria), khóa `Project` với giá trị `FCJ-Bootcamp-2026`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thông tin định danh nhóm tài nguyên `huylam-fcj-resources` và cấu hình thẻ phân loại `Project = FCJ-Bootcamp-2026`.

![Resource Groups Details](/images/week6/10-resource-groups-details.png)

---

#### 11. Danh sách các tài nguyên thành viên trong Resource Group:
- **Mô tả**: Bảng tổng hợp các tài nguyên thành viên thuộc nhóm `huylam-fcj-resources` (`Group resources (6)`). Hệ thống tự động phát hiện và gom nhóm toàn bộ máy chủ EC2, Security Group, IAM Role và các SSM Parameters cùng chia sẻ thẻ dự án.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, định danh ARN của nhóm tài nguyên và bảng danh sách 6 tài nguyên thành viên được gom cụm tự động.

![Resource Groups Members](/images/week6/11-resource-groups-members.png)

---

#### 12. Kiểm toán chi phí và trạng thái ngân sách FinOps:
- **Mô tả**: Trang tổng quan AWS Billing and Cost Management ghi nhận chi phí thực tế phát sinh lũy kế trong tháng (Month-to-date cost) là `0.10 USD`, dự báo chi phí cả tháng là `0.26 USD`. Khu vực Cost monitor xác nhận `2 active budget(s)` đều ở trạng thái `OK / Healthy`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thẻ tổng hợp chi phí `Cost summary` (MTD $0.10) và thẻ giám sát ngân sách `Cost monitor` (Budgets status OK).

![AWS Billing and Cost FinOps](/images/week6/12-aws-billing-cost-finops.png)

---

### Tổng kết bài học và giá trị thu hoạch:
1. **Kiến trúc Zero Trust toàn diện**: Nắm vững phương pháp bảo mật loại bỏ hoàn toàn việc mở cổng SSH (port 22) từ Internet, giảm thiểu nguy cơ bị quét cổng và tấn công Brute-force.
2. **Quản trị máy chủ hiện đại**: Làm chủ kênh truyền thông của SSM Agent, điều khiển máy chủ tương tác (Session Manager) và tự động hóa không tương tác (Run Command) qua bảng điều khiển trung tâm và AWS CLI.
3. **Bảo mật bí mật và cấu hình tập trung**: Hiểu sâu sắc sự khác biệt giữa String thông thường và SecureString mã hóa bởi AWS KMS, đảm bảo tuân thủ các chuẩn mực bảo mật ngành.
4. **Quản trị tài nguyên thông minh (Resource Governance)**: Sử dụng AWS Resource Groups và chiến lược gắn thẻ Tagging chuẩn mực để quản lý vòng đời tài nguyên, phục vụ tự động hóa và phân bổ chi phí minh bạch.
5. **Kỷ luật tài chính đám mây FinOps**: Kiểm soát chặt chẽ chi phí phát sinh (chỉ tiêu hao 0.10 USD), vận hành hệ thống máy chủ và lưu trữ an toàn trong phạm vi miễn phí của AWS Free Tier.