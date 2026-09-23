---
title: "Worklog Tuần 7"
date: 2026-09-20
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

> [!NOTE] Thời gian thực hiện
> **Từ ngày 14/09/2026 đến ngày 20/09/2026**

### Mục tiêu tuần 7:
* Nghiên cứu và làm chủ mô hình **Hạ tầng dưới dạng mã nguồn (Infrastructure as Code - IaC)** trên nền tảng điện toán đám mây AWS thông qua dịch vụ **AWS CloudFormation**.
* Xây dựng tệp mẫu khai báo hạ tầng (**CloudFormation Template**) chuẩn hóa bằng định dạng YAML, áp dụng cấu trúc tường minh gồm các khối `AWSTemplateFormatVersion`, `Description`, `Parameters`, `Resources` và `Outputs`.
* Tự động hóa phân giải hình ảnh máy ảo (AMI) Amazon Linux 2023 mới nhất theo thời gian thực bằng cơ chế tích hợp kiểu tham số chuyên dụng `AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>` thông qua đường dẫn SSM Parameter Store `/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64`.
* Tự động hóa quy trình khởi tạo và cấu hình phần mềm máy chủ web Apache (`httpd`) bằng kịch bản `UserData` dạng Base64, kết xuất giao diện website hiển thị định danh cá nhân học viên Lâm Quang Huy (MSSV: `0212267`), lớp 67CS, Trường ĐH Xây dựng Hà Nội (HUCE).
* Thiết lập nhóm bảo mật **Security Group** `WebServerSecurityGroup` cho phép lưu lượng truy cập Inbound HTTP cổng 80 từ mọi nguồn (`0.0.0.0/0`) và toàn quyền lưu lượng Outbound nhằm cập nhật gói phần mềm từ kho lưu trữ.
* Khởi tạo và quản trị vòng đời ngăn xếp tài nguyên (**CloudFormation Stack**) `huylam-cfn-stack`, theo dõi tiến trình triển khai qua các mốc sự kiện (Stack Events) cho tới khi đạt trạng thái hoàn tất thành công `CREATE_COMPLETE`.
* Kiểm tra và trích xuất các giá trị đầu ra (**Stack Outputs**) của hạ tầng bao gồm địa chỉ Public IPv4 `47.129.129.6`, mã định danh Security Group `sg-059146261d1b7c5eb`, tên Stack và đường dẫn truy cập website trực tiếp.
* Đo kiểm thực tế tính sẵn sàng của máy chủ web thông qua trình duyệt, kiểm chứng trang thông tin học viên hoạt động chuẩn xác trên Internet công cộng.
* Thực thi kiểm tra phát hiện trôi dạt cấu hình (**CloudFormation Drift Detection**) để đánh giá mức độ đồng bộ và toàn vẹn giữa trạng thái tài nguyên thực tế và bản thiết kế trong template.
* Duy trì kỷ luật tài chính đám mây **FinOps**: Thực hiện giải phóng toàn bộ tài nguyên ngăn xếp bằng quy trình FinOps Teardown một chạm, thu hồi máy chủ EC2 và Security Group nhằm bảo toàn ngân sách AWS Free Tier (0 USD).

---

### Các công việc đã triển khai trong tuần 7:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2 (14/09/2026)** | - Nghiên cứu khái niệm Infrastructure as Code (IaC).<br>- Phân tích ưu điểm của IaC so với thiết lập thủ công qua giao diện Console: tính lặp lại, nhất quán, kiểm soát phiên bản và tự động hóa.<br>- Tìm hiểu kiến trúc dịch vụ AWS CloudFormation. | Nắm vững nguyên lý hoạt động của CloudFormation Engine, cơ chế chuyển dịch từ tệp khai báo tĩnh sang tài nguyên AWS thực tế. | [AWS CloudFormation Concepts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html) |
| **Thứ 3 (15/09/2026)** | - Nghiên cứu cấu trúc tệp mẫu CloudFormation (YAML format).<br>- Tìm hiểu các hàm nội tại (Intrinsic Functions): `!Ref`, `!Sub`, `!GetAtt`.<br>- Tìm hiểu cách khai báo Parameters với các ràng buộc kiểu dữ liệu, giá trị mặc định (Default) và danh sách giá trị cho phép (AllowedValues). | Nắm vững kỹ thuật tham số hóa template, tăng cường khả năng tái sử dụng mã nguồn trên nhiều môi trường khác nhau. | [CloudFormation Template Anatomy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html) |
| **Thứ 4 (16/09/2026)** | - Nghiên cứu cơ chế tích hợp tham số động từ AWS Systems Manager Parameter Store vào CloudFormation.<br>- Cấu hình tham số `LatestAmiId` trích xuất AMI Amazon Linux 2023 mới nhất mà không cần mã hóa cứng (hardcode) AMI ID.<br>- Soạn thảo mã nguồn template `huylam-cfn-week7.yaml`. | Tối ưu hóa tính di động của template giữa các AWS Region mà không lo ngại sự sai khác về Image ID. | [AWS SSM Parameter Types](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#aws-ssm-parameter-types) |
| **Thứ 5 (17/09/2026)** | - Viết kịch bản `UserData` tự động hóa cài đặt Apache Web Server (`httpd`).<br>- Viết mã HTML giao diện Bootstrap Responsive nhúng thông tin định danh học viên Lâm Quang Huy (MSSV: 0212267).<br>- Định nghĩa tài nguyên Security Group và EC2 Instance gắn thẻ `Project = FCJ-Bootcamp-2026`. | Hoàn thiện bản mẫu hạ tầng hoàn chỉnh, sẵn sàng cho công đoạn triển khai Stack trên môi trường đám mây thực tế. | [EC2 User Data in CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html#cfn-ec2-instance-userdata) |
| **Thứ 6 (18/09/2026)** | - Khởi tạo CloudFormation Stack `huylam-cfn-stack` trên AWS Management Console vùng `ap-southeast-1`.<br>- Cung cấp các tham số đầu vào: `EnvironmentName`, `InstanceType` (`t3.micro`), `StudentID`, `StudentName`.<br>- Theo dõi bảng ghi sự kiện Stack Events trong quá trình tạo tài nguyên. | Toàn bộ các tài nguyên được khởi tạo tuần tự và đạt trạng thái `CREATE_COMPLETE` chỉ trong 25 giây. | [Working with Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html) |
| **Thứ 7 (19/09/2026)** | - Kiểm tra các thẻ thông tin chi tiết của Stack: Stack Info, Events, Resources, Outputs, Template.<br>- Trích xuất địa chỉ IP công cộng `47.129.129.6` từ tab Outputs.<br>- Truy cập website qua trình duyệt xác thực giao diện học viên và kiểm tra phản hồi HTTP 200 OK. | Xác thực thành công 100% website sinh viên chạy ổn định trên hạ tầng do CloudFormation tự động cung cấp. | [Viewing Stack Outputs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-view-stack-data-resources.html) |
| **Chủ Nhật**| - Thực hiện thao tác phát hiện sai lệch cấu hình **CloudFormation Drift Detection** cho Stack `huylam-cfn-stack`.<br>- Kiểm toán chi phí đám mây FinOps, bảo toàn ngân sách Free Tier.<br>- Thực hiện quy trình FinOps Teardown xóa Stack, tự động giải phóng sạch toàn bộ EC2 và Security Group.<br>- Tổng hợp báo cáo kỹ thuật Lab 000037 và cập nhật tài liệu Worklog Tuần 7. | Hoàn thành xuất sắc toàn diện mục tiêu Tuần 7, nắm vững năng lực IaC trên AWS và duy trì chi phí phát sinh ở mức 0 USD. | [Detecting Drift on Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/detect-drift-stack.html) |

---

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:

#### 1. Định danh tài khoản & Khu vực (Identity & Region):
- **AWS Account ID**: `677994024390`
- **Tên tài khoản (Account Name)**: `huylam`
- **IAM User thực thi**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **Availability Zone**: `ap-southeast-1c`
- **VPC trực thuộc**: Default VPC (`vpc-0c84feaf395ece4dd`)
- **Subnet thực thi**: `subnet-0bba3228d80514181` (Public Subnet)

#### 2. Thông số ngăn xếp CloudFormation (Stack Specification):
- **Tên Stack**: `huylam-cfn-stack`
- **Stack ID**: `arn:aws:cloudformation:ap-southeast-1:677994024390:stack/huylam-cfn-stack/54395360-b520-11f1-90f4-0a4ed60f7479`
- **Thời gian khởi tạo**: `2026-09-20 18:23:08 UTC`
- **Trạng thái ngăn xếp (Stack Status)**: `CREATE_COMPLETE`
- **Thời gian hoàn thành triển khai**: Khoảng 25 giây từ lúc nộp template
- **Chính sách khôi phục (Rollback Configuration)**: Mặc định (Rollback on failure)
- **Bảo vệ chống xóa (Termination Protection)**: Disabled

#### 3. Cấu hình tham số đầu vào (Stack Parameters):
- **EnvironmentName**: `FCJ-Bootcamp-2026` (Thẻ định danh môi trường triển khai)
- **InstanceType**: `t3.micro` (Phân hạng phần cứng máy chủ đủ điều kiện AWS Free Tier)
- **LatestAmiId**: `/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64`
  - Giá trị AMI được giải mã động (Resolved Value): `ami-085b17e53d4c8f0cb` (Amazon Linux 2023 64-bit x86_64)
- **StudentID**: `0212267` (Mã số sinh viên học viên)
- **StudentName**: `Lam Quang Huy` (Họ và tên học viên)

#### 4. Tài nguyên máy chủ ảo được khởi tạo (EC2 Instance via IaC):
- **Tên phiên bản (Name Tag)**: `huylam-cfn-webserver`
- **Logical ID trong Template**: `WebServerInstance`
- **Physical Resource ID**: `i-0b328b3c9bf942cd7`
- **Loại tài nguyên**: `AWS::EC2::Instance`
- **Trạng thái phiên bản**: `running`
- **Địa chỉ IP công cộng (Public IPv4)**: `47.129.129.6`
- **Địa chỉ IP riêng (Private IPv4)**: `172.31.15.0`
- **Public DNS**: `ec2-47-129-129-6.ap-southeast-1.compute.amazonaws.com`
- **Hệ điều hành**: Amazon Linux 2023
- **Thẻ phân loại gắn kèm (Tags)**:
  - `Name`: `huylam-cfn-webserver`
  - `Project`: `FCJ-Bootcamp-2026`
  - `StudentID`: `0212267`
  - `aws:cloudformation:stack-name`: `huylam-cfn-stack`
  - `aws:cloudformation:logical-id`: `WebServerInstance`

#### 5. Nhóm bảo mật hạ tầng (Security Group via IaC):
- **Logical ID trong Template**: `WebServerSecurityGroup`
- **Physical Resource ID**: `huylam-cfn-stack-WebServerSecurityGroup-oatvCIRD2205`
- **Security Group ID**: `sg-059146261d1b7c5eb`
- **Loại tài nguyên**: `AWS::EC2::SecurityGroup`
- **Quy tắc đầu vào (Inbound Rules)**:
  - Cổng 80 (HTTP) từ dải mạng `0.0.0.0/0` (Internet)
- **Quy tắc đầu ra (Outbound Rules)**:
  - Cho phép toàn bộ lưu lượng Outbound (`0.0.0.0/0`) để máy chủ cập nhật gói `dnf` và khởi chạy máy chủ web

#### 6. Các giá trị đầu ra của ngăn xếp (Stack Outputs):
- **WebServerPublicIp**: `47.129.129.6` - Địa chỉ IPv4 công cộng của máy chủ web
- **WebServerUrl**: `http://47.129.129.6` - Đường dẫn truy cập website trực tiếp qua giao thức HTTP
- **SecurityGroupId**: `huylam-cfn-stack-WebServerSecurityGroup-oatvCIRD2205` - Định danh nhóm bảo mật đã tạo
- **StackName**: `huylam-cfn-stack` - Tên định danh của CloudFormation Stack

#### 7. Đo kiểm phát hiện trôi dạt cấu hình (Drift Detection):
- **Drift Detection ID**: `8240c5e0-b520-11f1-854d-0205312630e5`
- **Thời điểm thực hiện**: `2026-09-20 18:29:44 UTC`
- **Trạng thái kiểm tra (Drift Status)**: Hoàn tất kiểm tra
- **Kết quả tài nguyên Security Group**: `IN_SYNC` (Không có bất kỳ thay đổi nào ngoài template)
- **Kết quả tài nguyên EC2 Instance**: `MODIFIED` (Hệ thống ghi nhận trạng thái mạng bổ sung runtime như ENI và Public IP động)

---

### Tệp mẫu khai báo hạ tầng (CloudFormation Template Code):

Toàn bộ ngăn xếp hạ tầng của Tuần 7 được định nghĩa trong tệp mẫu YAML `huylam-cfn-week7.yaml` với nội dung khai báo chi tiết như sau:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: >
  AWS CloudFormation Lab 000037 - Infrastructure as Code (IaC)
  Student: Lam Quang Huy | Student ID: 0212267 | Class: 67CS - HUCE
  FCJ Cloud Journey Bootcamp 2026

Parameters:
  EnvironmentName:
    Type: String
    Default: FCJ-Bootcamp-2026
    Description: Deployment environment tag name

  StudentName:
    Type: String
    Default: Lam Quang Huy
    Description: Student full name

  StudentID:
    Type: String
    Default: "0212267"
    Description: Student ID number

  InstanceType:
    Type: String
    Default: t3.micro
    AllowedValues:
      - t2.micro
      - t3.micro
    Description: Amazon EC2 instance type (Free Tier eligible)

  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64'
    Description: Automatically resolve the latest Amazon Linux 2023 AMI via AWS Systems Manager Parameter Store

Resources:
  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable HTTP access from anywhere for HuyLam Web Server
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Sub '${AWS::StackName}-web-sg'
        - Key: Project
          Value: !Ref EnvironmentName
        - Key: StudentID
          Value: !Ref StudentID

  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !Ref LatestAmiId
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          dnf update -y
          dnf install -y httpd
          systemctl start httpd
          systemctl enable httpd
          cat <<EOF > /var/www/html/index.html
          <!DOCTYPE html>
          <html lang="vi">
          <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>AWS CloudFormation Lab 000037 - ${StudentName}</title>
            <style>
              body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #0f172a; color: #f8fafc; margin: 0; padding: 40px 20px; display: flex; justify-content: center; align-items: center; min-height: 80vh; }
              .card { background: #1e293b; border-radius: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.5); padding: 36px; max-width: 650px; width: 100%; border: 1px solid #334155; }
              h1 { color: #38bdf8; font-size: 24px; margin-top: 0; border-bottom: 2px solid #334155; padding-bottom: 12px; }
              .badge { display: inline-block; background-color: #0284c7; color: white; padding: 4px 10px; border-radius: 6px; font-weight: 600; font-size: 13px; margin-bottom: 16px; }
              .info-row { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #334155; }
              .label { color: #94a3b8; font-weight: 500; }
              .value { font-weight: 600; color: #f1f5f9; }
              .success { color: #4ade80; font-weight: bold; }
              .footer { margin-top: 24px; text-align: center; color: #64748b; font-size: 13px; }
            </style>
          </head>
          <body>
            <div class="card">
              <span class="badge">AWS CloudFormation IaC</span>
              <h1>Web Server Deployed via CloudFormation</h1>
              <div class="info-row"><span class="label">Hoc vien:</span><span class="value">${StudentName}</span></div>
              <div class="info-row"><span class="label">MSSV:</span><span class="value">${StudentID}</span></div>
              <div class="info-row"><span class="label">Lop:</span><span class="value">67CS - Truong DH Xay dung Ha Noi (HUCE)</span></div>
              <div class="info-row"><span class="label">Stack Name:</span><span class="value">${AWS::StackName}</span></div>
              <div class="info-row"><span class="label">Region:</span><span class="value">${AWS::Region}</span></div>
              <div class="info-row"><span class="label">Du an:</span><span class="value">${EnvironmentName}</span></div>
              <div class="info-row"><span class="label">Trang thai:</span><span class="value success">SUCCESSFULLY PROVISIONED</span></div>
              <div class="footer">FCJ Cloud Journey Bootcamp 2026 - Infrastructure as Code Module</div>
            </div>
          </body>
          </html>
          EOF
      Tags:
        - Key: Name
          Value: huylam-cfn-webserver
        - Key: Project
          Value: !Ref EnvironmentName
        - Key: StudentID
          Value: !Ref StudentID

Outputs:
  WebServerPublicIp:
    Description: Public IPv4 address of the web server
    Value: !GetAtt WebServerInstance.PublicIp

  WebServerUrl:
    Description: HTTP URL to access the deployed website
    Value: !Sub 'http://${WebServerInstance.PublicIp}'

  SecurityGroupId:
    Description: ID of the Security Group created
    Value: !Ref WebServerSecurityGroup

  StackName:
    Description: Name of the deployed CloudFormation Stack
    Value: !Ref 'AWS::StackName'
```

---

### Hình ảnh minh chứng triển khai thực tế trên AWS:

Tất cả các hình ảnh minh chứng dưới đây đều được trích xuất trực tiếp từ các phiên làm việc trên AWS Management Console và cửa sổ trình duyệt thực tế của sinh viên **Lâm Quang Huy (MSSV: 0212267)**. Các khu vực trọng yếu gồm huy hiệu tài khoản `huylam (677994024390)`, khu vực Singapore `ap-southeast-1` và các thông số kỹ thuật cốt lõi đều được đóng khung viền đỏ chuẩn xác:

#### 1. Danh sách CloudFormation Stacks ban đầu:
- **Mô tả**: Bảng điều khiển AWS CloudFormation tại khu vực `ap-southeast-1` trước khi triển khai. Danh sách Stacks ghi nhận trạng thái ban đầu sạch sẽ (`Stacks (0)`, `No stacks to display`) với nút bấm `Create stack` màu cam nổi bật sẵn sàng cho việc khởi tạo hạ tầng.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` góc trên bên phải, thanh công cụ tiêu đề `Stacks (0)` và vùng trung tâm thông báo `No stacks to display`.

![CloudFormation Stacks List Initial](/images/week7/01-cfn-stacks-list-initial.png)

---

#### 2. Tải lên tệp mẫu CloudFormation Template:
- **Mô tả**: Giao diện bước 1 (Create stack - Prerequisite - Prepare template) với tùy chọn `Template is ready`, mục `Template source` chọn `Upload a template file` và đã tải lên thành công tệp tin `huylam-cfn-week7.yaml` từ máy cục bộ.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và khung cấu hình `Specify template` hiển thị tệp `huylam-cfn-week7.yaml` đã sẵn sàng kèm nút điều hướng `Next`.

![Create Stack Upload Template](/images/week7/02-cfn-create-stack-upload-template.png)

---

#### 3. Cấu hình chi tiết ngăn xếp và tham số đầu vào:
- **Mô tả**: Giao diện bước 2 (Specify stack details) nhập tên Stack `huylam-cfn-stack` và cấu hình các tham số: `EnvironmentName` (`FCJ-Bootcamp-2026`), `InstanceType` (`t3.micro`), đường dẫn SSM Parameter `LatestAmiId`, `StudentID` (`0212267`) và `StudentName` (`Lam Quang Huy`).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thẻ `Provide a stack name` và thẻ `Parameters` chứa đầy đủ 5 tham số được định nghĩa trong template.

![Specify Stack Details](/images/week7/03-cfn-specify-stack-details.png)

---

#### 4. Quá trình tạo ngăn xếp đang diễn ra (CREATE_IN_PROGRESS):
- **Mô tả**: Bảng điều khiển ngăn xếp `huylam-cfn-stack` ngay sau khi nộp yêu cầu. Trạng thái ngăn xếp hiển thị huy hiệu màu xanh lục `CREATE_IN_PROGRESS` kèm thời gian bắt đầu khởi tạo `2026-09-21 01:23:08 UTC+0700`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và thẻ tổng quan trạng thái Stack hiển thị `CREATE_IN_PROGRESS`.

![Stack Create In Progress](/images/week7/04-cfn-stack-create-in-progress.png)

---

#### 5. Xác thực máy chủ Web qua trình duyệt thực tế:
- **Mô tả**: Cửa sổ trình duyệt web truy cập địa chỉ IP công cộng `http://47.129.129.6`. Trang web hiển thị thẻ giao diện hiện đại do kịch bản `UserData` tự động thiết lập với đầy đủ thông tin: Học viên: Lam Quang Huy, MSSV: 0212267, Lớp: 67CS, Stack Name: huylam-cfn-stack, Region: ap-southeast-1, Dự án: FCJ-Bootcamp-2026 và trạng thái `SUCCESSFULLY PROVISIONED`.
- **Vùng khoanh đỏ**: Thanh địa chỉ trình duyệt hiển thị URL `http://47.129.129.6/` và khối thẻ thông tin xác nhận kết quả triển khai website sinh viên thành công.

![Web Server Browser Verification](/images/week7/05-cfn-webserver-browser-verification.png)

---

#### 6. Thông tin ngăn xếp đạt trạng thái hoàn tất (CREATE_COMPLETE):
- **Mô tả**: Thẻ thông tin chung (Stack info) của `huylam-cfn-stack` sau khi hoàn thành khởi tạo. Trạng thái cập nhật chính thức thành `CREATE_COMPLETE` kèm thời gian kết thúc `2026-09-21 01:23:33 UTC+0700`, phần mô tả thể hiện thông tin định danh học viên Lâm Quang Huy.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và thẻ chi tiết Stack info xác nhận trạng thái `CREATE_COMPLETE`.

![Stack Info Create Complete](/images/week7/06-cfn-stack-info-create-complete.png)

---

#### 7. Toàn bộ chuỗi sự kiện khởi tạo tài nguyên (Stack Events):
- **Mô tả**: Thẻ sự kiện (Events) liệt kê tuần tự toàn bộ quá trình tự động cung cấp hạ tầng theo thời gian thực: khởi tạo Security Group `WebServerSecurityGroup`, khởi tạo máy chủ ảo `WebServerInstance` và chuyển đổi trạng thái ngăn xếp sang `CREATE_COMPLETE`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và danh sách các bản ghi sự kiện xác nhận tất cả tài nguyên đã được tạo thành công.

![Stack Events All Complete](/images/week7/07-cfn-stack-events-all-complete.png)

---

#### 8. Danh sách tài nguyên vật lý đã tạo (Stack Resources):
- **Mô tả**: Thẻ tài nguyên (Resources) tổng hợp 2 tài nguyên đám mây cốt lõi được định nghĩa trong template: máy chủ ảo `WebServerInstance` (Physical ID: `i-0b328b3c9bf942cd7`) và nhóm bảo mật `WebServerSecurityGroup` (Physical ID: `huylam-cfn-stack-WebServerSecurityGroup-oatvCIRD2205`).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và bảng danh sách tài nguyên `Resources (2)` đạt trạng thái `CREATE_COMPLETE`.

![Stack Resources List](/images/week7/08-cfn-stack-resources-list.png)

---

#### 9. Các giá trị đầu ra của ngăn xếp (Stack Outputs):
- **Mô tả**: Thẻ đầu ra (Outputs) xuất khẩu các giá trị quan trọng được tính toán sau khi tạo tài nguyên: `SecurityGroupId`, `StackName`, địa chỉ IP công cộng `WebServerPublicIp` (`47.129.129.6`) và liên kết URL `WebServerUrl` (`http://47.129.129.6`).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và bảng danh sách Outputs hiển thị đầy đủ 4 khóa giá trị.

![Stack Outputs Values](/images/week7/09-cfn-stack-outputs-values.png)

---

#### 10. Tệp mẫu YAML hiển thị trên bảng điều khiển (Stack Template):
- **Mô tả**: Thẻ cấu hình tệp mẫu (Template) xác thực lại toàn bộ mã nguồn YAML đã được hệ thống lưu trữ và biên dịch. Khối mã hiển thị rõ định danh phiên bản bản mẫu, phần mô tả học viên và các khai báo tham số.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và khung hiển thị mã nguồn mẫu khai báo hạ tầng YAML.

![Stack Template YAML](/images/week7/10-cfn-stack-template-yaml.png)

---

#### 11. Kích hoạt tính năng phát hiện trôi dạt cấu hình (Detect Drift):
- **Mô tả**: Menu thao tác ngăn xếp (Stack actions) mở ra các tùy chọn quản trị nâng cao. Lựa chọn tính năng `Detect drift` để hệ thống tự động so sánh trạng thái thực tế của các tài nguyên đám mây với định nghĩa ban đầu trong bản mẫu CloudFormation.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và menu thả xuống `Stack actions` với tùy chọn `Detect drift` được làm nổi bật.

![Stacks List Actions Menu](/images/week7/11-cfn-stacks-list-actions-menu.png)

---

#### 12. Kết quả kiểm tra trôi dạt cấu hình (Drift Detection Results):
- **Mô tả**: Bảng điều khiển xác nhận thông báo `Drift detection initiated for huylam-cfn-stack` với mã nhận diện kiểm tra `8240c5e0-b520-11f1-854d-0205312630e5` và trạng thái `Drift status` hoàn tất kiểm tra tính toàn vẹn cấu hình.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thanh thông báo hệ thống và khu vực trạng thái kiểm tra Drift status.

![Stack Drift Detection](/images/week7/12-cfn-stack-drift-detection.png)

---

### Tổng kết bài học và giá trị thu hoạch:
1. **Làm chủ tư duy Infrastructure as Code (IaC)**: Chuyển đổi căn bản từ việc thiết lập hạ tầng thủ công, rời rạc sang mô hình quản trị hạ tầng bằng phần mềm, có khả năng tái sử dụng, lập phiên bản và kiểm soát thay đổi chặt chẽ.
2. **Kỹ thuật tham số hóa động linh hoạt**: Sử dụng cơ chế phân giải hình ảnh máy ảo AMI thông qua AWS Systems Manager Parameter Store, giúp bản mẫu có thể vận hành đa khu vực (Multi-Region) mà không cần cấu hình thủ công Image ID.
3. **Tự động hóa hoàn toàn quy trình khởi tạo máy chủ (Bootstrapping)**: Nhúng script `UserData` để tự động hóa 100% công việc cập nhật hệ điều hành, cài đặt web server Apache và cấu hình giao diện người dùng ngay khi phiên bản máy chủ vừa được bật nguồn.
4. **Kiểm soát vòng đời và tính toàn vẹn hạ tầng**: Khai thác sức mạnh của Stack Events và Drift Detection để giám sát tiến độ khởi tạo cũng như phát hiện bất kỳ sự thay đổi cấu hình ngoài luồng nào đối với tài nguyên đám mây.
5. **Kỷ luật tài chính đám mây FinOps**: Ứng dụng khả năng xóa ngăn xếp (Delete Stack) để thu hồi đồng loạt mọi tài nguyên liên quan chỉ với một thao tác duy nhất, đảm bảo loại bỏ hoàn toàn tài nguyên nhàn rỗi và duy trì chi phí ở mức 0 USD.

---

### Quy trình dọn dẹp tài nguyên (FinOps Cleanup):

Nhằm bảo toàn ngân sách đám mây và đưa chi phí về mức 0 USD sau khi kết thúc bài thực hành Tuần 7, toàn bộ ngăn xếp và tài nguyên phụ thuộc được dọn dẹp theo quy trình kỹ thuật chuẩn xác sau:

#### 1. Thực hiện xóa CloudFormation Stack:
Dịch vụ CloudFormation sở hữu cơ chế thu hồi tài nguyên ngược (Reverse Dependency Deletion). Khi thực hiện lệnh xóa ngăn xếp, toàn bộ máy chủ EC2 `huylam-cfn-webserver` và nhóm bảo mật `WebServerSecurityGroup` sẽ tự động được chấm dứt và xóa bỏ mà không cần thao tác riêng lẻ:
```bash
aws cloudformation delete-stack --stack-name huylam-cfn-stack
```

#### 2. Giám sát quá trình xóa ngăn xếp cho tới khi hoàn tất:
Theo dõi trạng thái thu hồi tài nguyên của ngăn xếp thông qua lệnh chờ của AWS CLI:
```bash
aws cloudformation wait stack-delete-complete --stack-name huylam-cfn-stack
```

#### 3. Xác thực kết quả dọn dẹp thực tế trên AWS CLI:
Thực hiện các câu lệnh kiểm tra nhằm đảm bảo hệ thống đã hoàn toàn sạch tài nguyên nhàn rỗi:
```bash
# 1. Xác thực CloudFormation Stack đã bị xóa hoàn toàn
aws cloudformation describe-stacks --stack-name huylam-cfn-stack 2>&1 | grep "does not exist"

# 2. Xác thực máy chủ EC2 đã chuyển sang trạng thái terminated
aws ec2 describe-instances --filters "Name=tag:aws:cloudformation:stack-name,Values=huylam-cfn-stack" \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name]" --output table

# 3. Xác thực không còn Security Group phụ thuộc của bài lab
aws ec2 describe-security-groups --filters "Name=group-name,Values=*huylam-cfn-stack*" \
  --query "SecurityGroups[*].[GroupId,GroupName]" --output table
```
*Kết quả kiểm toán thực tế xác nhận 100% tài nguyên đã được giải phóng thành công, bảo toàn ngân sách AWS Free Tier không phát sinh thêm chi phí.*