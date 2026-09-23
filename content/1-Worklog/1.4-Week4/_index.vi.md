---
title: "Worklog Tuần 4"
date: 2026-08-30
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

> [!NOTE] Thời gian thực hiện
> **Từ ngày 24/08/2026 đến ngày 30/08/2026**

### Mục tiêu tuần 4:
* Tìm hiểu kiến trúc điện toán có khả năng co giãn linh hoạt và độ sẵn sàng cao (High Availability & Scalability) trên nền tảng AWS.
* Thiết kế mô hình bảo mật mạng hai tầng (Two-Tier Security Group Architecture) phân tách giữa tầng cân bằng tải và tầng máy chủ ứng dụng backend.
* Khởi tạo mẫu cấu hình máy chủ ảo Amazon EC2 Launch Template với hệ điều hành Amazon Linux 2023, phân hạng phần cứng `t3.micro`, kết hợp tập lệnh User Data sử dụng giao thức siêu dữ liệu IMDSv2.
* Thiết lập nhóm mục tiêu Target Group sử dụng giao thức HTTP port 80 và cấu hình quy trình kiểm tra sức khỏe tự động (Health Checks) theo định kỳ.
* Triển khai bộ cân bằng tải ứng dụng Application Load Balancer (ALB) kết nối Internet (Internet-facing), định tuyến lưu lượng qua 2 Public Subnets Multi-AZ thuộc Custom VPC `huylam-vpc`.
* Khởi tạo nhóm tự động co giãn Auto Scaling Group (ASG) với cấu hình dung lượng linh hoạt (Desired: 2, Min: 1, Max: 4), tự động phân bổ máy chủ ảo đồng đều trên 2 Availability Zones (`ap-southeast-1a` và `ap-southeast-1b`).
* Xác thực trạng thái hoạt động của các mục tiêu trong Target Group (đạt 2/2 Healthy targets).
* Kiểm chứng cơ chế cân bằng tải luân chuyển vòng tròn (Round-Robin Routing) qua tên miền DNS của ALB trên trình duyệt thực tế, ghi nhận phản hồi từ các Instance ID và Availability Zones độc lập.
* Thực hiện quy trình FinOps: Giải phóng toàn bộ tài nguyên tính toán và cân bằng tải ngay sau khi thu thập minh chứng để bảo toàn hạn mức AWS Free Tier.

### Các công việc đã triển khai trong tuần 4:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2 (24/08/2026)** | - Nghiên cứu lý thuyết Elastic Load Balancing (ELB), phân biệt ALB, NLB và GLB.<br>- Tìm hiểu cơ chế định tuyến tầng ứng dụng (Layer 7 Routing), Listeners và Target Groups. | Nắm vững nguyên lý hoạt động của Application Load Balancer và phương thức quản lý đích đến qua Target Group. | [AWS ELB Documentation](https://docs.aws.amazon.com/elasticloadbalancing/) |
| **Thứ 3 (25/08/2026)** | - Tìm hiểu cơ chế hoạt động của Amazon EC2 Auto Scaling.<br>- Nghiên cứu các thông số quy mô: Desired Capacity, Minimum Capacity, Maximum Capacity.<br>- Khảo sát chu kỳ vòng đời phiên bản (EC2 Instance Lifecycle). | Hiểu rõ cơ chế tự động mở rộng theo nhu cầu tải và khả năng tự phục hồi (Self-healing) khi phát hiện phiên bản lỗi. | [AWS Auto Scaling Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) |
| **Thứ 4 (26/08/2026)** | - Thiết kế kiến trúc bảo mật 2 tầng trên `huylam-vpc`.<br>- Khởi tạo Security Group `huylam-alb-sg` cho phép HTTP (port 80) từ Internet (`0.0.0.0/0`).<br>- Khởi tạo Security Group `huylam-asg-web-sg` chỉ cho phép HTTP từ `huylam-alb-sg`. | Thiết lập nguyên tắc phòng thủ đa lớp (Defense in Depth), ngăn chặn hoàn toàn truy cập trực tiếp từ Internet vào máy chủ backend. | [AWS Security Best Practices](https://docs.aws.amazon.com/whitepapers/latest/architecting-for-the-cloud-aws-best-practices/security.html) |
| **Thứ 5 (27/08/2026)** | - Khởi tạo Launch Template `huylam-launch-template` (`lt-064a116476cc47e0a`).<br>- Cấu hình AMI Amazon Linux 2023 và phân hạng `t3.micro`.<br>- Viết tập lệnh User Data lấy IMDSv2 token và tự động render thông tin định danh sinh viên. | Chuẩn hóa mẫu triển khai máy chủ web; nhúng động Instance ID, Private IP và Availability Zone vào trang chào mừng. | [Lab 000006](https://000006.awsstudygroup.com) |
| **Thứ 6 (28/08/2026)** | - Khởi tạo Target Group `huylam-alb-tg` với cổng HTTP 80 và đường dẫn Health Check `/`.<br>- Tạo Application Load Balancer `huylam-alb` gắn trên 2 Public Subnets Multi-AZ.<br>- Cấu hình Listener HTTP:80 chuyển tiếp lưu lượng vào `huylam-alb-tg`. | Hoàn thành hạ tầng cân bằng tải, ALB được cấp phát tên miền DNS công khai và chuyển sang trạng thái Active. | [AWS ALB Getting Started](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/application-load-balancer-getting-started.html) |
| **Thứ 7 (29/08/2026)** | - Khởi tạo Auto Scaling Group `huylam-asg` với dung lượng chuẩn: Desired 2, Min 1, Max 4.<br>- Gắn kết với Launch Template và Target Group `huylam-alb-tg`.<br>- Kiểm tra tiến trình khởi chạy 2 máy chủ ảo trên 2 Availability Zones. | Hệ thống tự động tạo 2 instances `i-01abe8b9b987aad60` (`ap-southeast-1a`) và `i-0e633e2910dd9ea8f` (`ap-southeast-1b`). | [Lab 000006](https://000006.awsstudygroup.com) |
| **Chủ Nhật**| - Kiểm tra Target Health: Xác nhận 2/2 targets đạt trạng thái Healthy.<br>- Thực hiện kiểm nghiệm phân phối tải Round-Robin trên trình duyệt thực tế.<br>- Thực hiện quy trình FinOps: Xóa ASG, terminate instances, xóa ALB và Target Group.<br>- Tổng hợp báo cáo kỹ thuật và triển khai worklog. | Xác thực khả năng chịu lỗi và cân bằng tải thành công; giải phóng toàn bộ tài nguyên tính toán để đưa chi phí về 0 USD. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

---

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:

#### 1. Định danh tài khoản & Khu vực (Identity & Region):
- **AWS Account ID**: `677994024390`
- **Tên tài khoản (Account Name)**: `huylam`
- **IAM User thực thi**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **VPC trực thuộc**: `huylam-vpc` (`vpc-0125f4d6db3fbffa6`)

#### 2. Nhóm bảo mật mạng hai tầng (Security Groups):
* **ALB Security Group (`huylam-alb-sg` - ID: `sg-066b3dc2f4c6b4c86`)**:
  - Inbound Rules: HTTP (TCP 80), Nguồn `0.0.0.0/0` (Tiếp nhận yêu cầu từ người dùng Internet).
  - Outbound Rules: All traffic tới `0.0.0.0/0` (Chuyển tiếp lưu lượng tới các máy chủ backend).
* **ASG Web Security Group (`huylam-asg-web-sg` - ID: `sg-0a9dede4860cae619`)**:
  - Inbound Rule 1: HTTP (TCP 80), Nguồn `sg-066b3dc2f4c6b4c86` (`huylam-alb-sg`) - Chỉ cho phép lưu lượng đi qua bộ cân bằng tải.
  - Inbound Rule 2: SSH (TCP 22), Nguồn `0.0.0.0/0` - Phục vụ quản trị và xử lý sự cố.
  - Outbound Rules: All traffic tới `0.0.0.0/0` - Cho phép máy chủ tải gói tin và cập nhật hệ điều hành.

#### 3. Mẫu khởi tạo phiên bản (Launch Template):
- **Launch Template ID**: `lt-064a116476cc47e0a`
- **Tên mẫu (Name)**: `huylam-launch-template`
- **Phiên bản mặc định**: Version 1
- **Amazon Machine Image (AMI)**: `ami-095f155a67469a548` (Amazon Linux 2023 Kernel 6.1 x86_64)
- **Instance Type**: `t3.micro` (Đạt chuẩn AWS Free Tier)
- **Security Group gán kèm**: `sg-0a9dede4860cae619` (`huylam-asg-web-sg`)
- **Tập lệnh khởi động (User Data Script)**:
  - Khởi tạo mã bảo mật IMDSv2 qua HTTP PUT tới `http://169.254.169.254/latest/api/token` với thời hạn 21,600 giây.
  - Truy vấn dữ liệu động từ metadata: `instance-id`, `local-ipv4`, `placement/availability-zone`.
  - Tự động cài đặt và kích hoạt dịch vụ Apache HTTP Server (`httpd`).
  - Xuất bản trang web hiển thị thẻ sinh viên Lâm Quang Huy (MSSV: `0212267`), Lớp 67CS - HUCE kèm thông số phần cứng của máy chủ đang phục vụ yêu cầu.

#### 4. Nhóm mục tiêu Target Group:
- **Target Group ARN**: `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-alb-tg/01224986629b1f06`
- **Tên nhóm (Name)**: `huylam-alb-tg`
- **Target Type**: `instance`
- **Giao thức & Cổng**: `HTTP:80`
- **Mạng ảo trực thuộc**: `vpc-0125f4d6db3fbffa6` (`huylam-vpc`)
- **Đường dẫn kiểm tra sức khỏe (Health Check Path)**: `/`
- **Giao thức Health Check**: `HTTP`
- **Trạng thái đích đến**: 2/2 targets đạt trạng thái `healthy`.

#### 5. Bộ cân bằng tải ứng dụng Application Load Balancer:
- **ALB ARN**: `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:loadbalancer/app/huylam-alb/1d3845375aef8945`
- **Tên cân bằng tải**: `huylam-alb`
- **Loại hình (Scheme)**: `internet-facing`
- **Định dạng địa chỉ**: `ipv4`
- **VPC trực thuộc**: `vpc-0125f4d6db3fbffa6` (`huylam-vpc`)
- **Ánh xạ mạng con (Subnet Mapping)**:
  - `subnet-0efa7c3a5818035dc` (`huylam-subnet-public1-ap-southeast-1a`)
  - `subnet-0e07eb2fd44d1ac91` (`huylam-subnet-public2-ap-southeast-1b`)
- **Nhóm bảo mật gán kèm**: `sg-066b3dc2f4c6b4c86` (`huylam-alb-sg`)
- **Tên miền DNS (DNS Name)**: `huylam-alb-1974057692.ap-southeast-1.elb.amazonaws.com`
- **Bộ tiếp nhận (Listener)**: Cổng HTTP:80 chuyển tiếp (Forward) tới `huylam-alb-tg`
- **Trạng thái hoạt động**: `active`

#### 6. Nhóm tự động co giãn Auto Scaling Group:
- **Tên nhóm (ASG Name)**: `huylam-asg`
- **Launch Template gắn kèm**: `huylam-launch-template` (Version 1)
- **Mạng con hoạt động**: 2 Public Subnets Multi-AZ (`subnet-0efa7c3a5818035dc`, `subnet-0e07eb2fd44d1ac91`)
- **Target Group gắn kèm**: `huylam-alb-tg`
- **Loại kiểm tra sức khỏe**: `ELB`
- **Thời gian gia hạn (Health Check Grace Period)**: 300 giây
- **Cấu hình dung lượng**:
  - Desired Capacity: `2` (Duy trì thường trực 2 máy chủ)
  - Minimum Capacity: `1` (Không giảm dưới 1 máy chủ khi tải thấp)
  - Maximum Capacity: `4` (Tối đa mở rộng lên 4 máy chủ khi quá tải)
- **Danh sách máy chủ ảo được khởi tạo tự động**:
  - Instance 1: `i-01abe8b9b987aad60` | Availability Zone: `ap-southeast-1a` | Private IP: `10.0.9.229` | Public IP: `47.129.221.209`
  - Instance 2: `i-0e633e2910dd9ea8f` | Availability Zone: `ap-southeast-1b` | Private IP: `10.0.25.48` | Public IP: `54.255.196.87`

---

### Hình ảnh minh chứng triển khai thực tế trên AWS:

Tất cả các hình ảnh minh chứng dưới đây đều được chụp trực tiếp từ giao diện AWS Management Console và trình duyệt kiểm thử thực tế, có đóng khung viền đỏ nổi bật tại huy hiệu tài khoản `huylam (677994024390)`, khu vực Singapore `ap-southeast-1` cùng các cấu hình thông số kỹ thuật cốt lõi:

#### 1. Khởi tạo Security Group cho Application Load Balancer (`huylam-alb-sg`):
- **Mô tả**: Thiết lập tường lửa ảo cho tầng biên tiếp nhận lưu lượng HTTP từ Internet qua cổng TCP 80.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, tên Security Group `huylam-alb-sg`, VPC ID `vpc-0125f4d6db3fbffa6` và bảng Inbound Rules cho phép `0.0.0.0/0`.

![Cấu hình ALB Security Group](/images/week4/01-alb-security-group.png)

---

#### 2. Khởi tạo Security Group cho máy chủ backend Auto Scaling (`huylam-asg-web-sg`):
- **Mô tả**: Cấu hình quy tắc chỉ cho phép lưu lượng HTTP cổng 80 có nguồn gốc từ Security Group của ALB (`sg-066b3dc2f4c6b4c86`), ngăn ngừa tình trạng truy cập trực tiếp từ Internet vào máy chủ ứng dụng.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, tên `huylam-asg-web-sg` và quy tắc Inbound ràng buộc nguồn từ `huylam-alb-sg`.

![Cấu hình ASG Web Security Group](/images/week4/02-asg-security-group.png)

---

#### 3. Cấu hình thông số chi tiết Launch Template (`huylam-launch-template`):
- **Mô tả**: Thiết lập mẫu khởi tạo máy chủ ảo với AMI Amazon Linux 2023 (`ami-095f155a67469a548`) và phân hạng vi xử lý `t3.micro`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, Launch template name `huylam-launch-template`, AMI ID và phân hạng Instance type.

![Thông số Launch Template](/images/week4/03-launch-template-details.png)

---

#### 4. Nhúng tập lệnh User Data Script tự động hóa với IMDSv2:
- **Mô tả**: Sử dụng mã lệnh Bash lấy token IMDSv2, truy vấn metadata máy chủ và cài đặt máy chủ web Apache render trang thông tin định danh sinh viên.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, khu vực Advanced Details và khung soạn thảo User data script.

![User Data Script với IMDSv2](/images/week4/04-launch-template-user-data.png)

---

#### 5. Xác nhận tạo Launch Template thành công:
- **Mô tả**: AWS thông báo tạo thành công mẫu `huylam-launch-template` phiên bản mặc định Version 1 sẵn sàng đưa vào sử dụng trong Auto Scaling Group.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thông báo thành công màu xanh lá và các tùy chọn liên kết tiếp theo.

![Tạo Launch Template thành công](/images/week4/05-launch-template-created-success.png)

---

#### 6. Khởi tạo Target Group cho ALB (`huylam-alb-tg`):
- **Mô tả**: Đăng ký Target Group loại Instance, giao thức HTTP:80 trên mạng ảo `huylam-vpc` phục vụ định tuyến tải cho ALB.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thông báo tạo Target Group thành công, ARN và thuộc tính giao thức / VPC.

![Tạo Target Group thành công](/images/week4/06-target-group-created.png)

---

#### 7. Cấu hình mạng đa vùng khả dụng Multi-AZ cho Application Load Balancer:
- **Mô tả**: Ánh xạ bộ cân bằng tải `huylam-alb` vào 2 Public Subnets phân bổ trên `ap-southeast-1a` và `ap-southeast-1b`, gán Security Group `huylam-alb-sg`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, danh sách các Subnets được chọn và Security Group gán kèm.

![Cấu hình mạng Multi-AZ cho ALB](/images/week4/07-alb-create-network-mapping.png)

---

#### 8. Thông tin bộ cân bằng tải `huylam-alb` và Listener định tuyến:
- **Mô tả**: Kiểm tra thông tin ALB đã tạo, bao gồm tên miền DNS công khai và Listener cổng 80 chuyển tiếp lưu lượng vào `huylam-alb-tg`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thông báo tạo thành công, DNS Name `huylam-alb-1974057692.ap-southeast-1.elb.amazonaws.com` và danh sách Listeners and rules.

![Thông số ALB và DNS Name](/images/week4/08-alb-created-details-active.png)

---

#### 9. Cấu hình quy mô và mạng lưới cho Auto Scaling Group (`huylam-asg`):
- **Mô tả**: Khởi tạo nhóm co giãn tự động liên kết với Launch Template `huylam-launch-template`, đặt trên 2 Public Subnets Multi-AZ và gắn kết vào Target Group.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, tên ASG `huylam-asg`, Launch Template và 2 dải mạng Subnet CIDR `10.0.0.0/20` và `10.0.16.0/20`.

![Cấu hình Auto Scaling Group](/images/week4/09-asg-configuration-and-capacity.png)

---

#### 10. Xác nhận trạng thái Healthy của các máy chủ trong Target Group:
- **Mô tả**: Kiểm tra tab Targets của `huylam-alb-tg`, ghi nhận hệ thống phát hiện chính xác 2 mục tiêu EC2 backend và đều vượt qua quy trình kiểm tra sức khỏe với trạng thái Healthy (`2/2 Healthy`).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thông số `2 Total targets`, `2 Healthy`, liên kết tới ALB `huylam-alb` và VPC `vpc-0125f4d6db3fbffa6`.

![Kiểm tra Target Health Healthy](/images/week4/10-target-group-healthy-instances.png)

---

#### 11. Kiểm nghiệm cân bằng tải thực tế qua trình duyệt: Phản hồi từ Availability Zone `ap-southeast-1b`:
- **Mô tả**: Truy cập tên miền DNS của ALB trên trình duyệt web, lưu lượng được chuyển tiếp tới máy chủ ảo `i-0e633e2910dd9ea8f` đặt tại `ap-southeast-1b`.
- **Vùng khoanh đỏ**: Thanh địa chỉ trình duyệt hiển thị DNS Name của ALB, thông tin thẻ sinh viên Lâm Quang Huy (MSSV: `0212267`), Instance ID `i-0e633e2910dd9ea8f` và Availability Zone `ap-southeast-1b`.

![Truy cập qua ALB - Phản hồi từ Node 1b](/images/week4/11-alb-browser-round-robin-az1.png)

---

#### 12. Kiểm nghiệm cân bằng tải thực tế qua trình duyệt: Phản hồi luân chuyển từ Availability Zone `ap-southeast-1a`:
- **Mô tả**: Sau khi tải lại trang web, bộ cân bằng tải ALB tự động điều hướng yêu cầu tiếp theo tới máy chủ ảo thứ hai `i-01abe8b9b987aad60` đặt tại `ap-southeast-1a`.
- **Vùng khoanh đỏ**: Thanh địa chỉ trình duyệt hiển thị DNS Name của ALB, thông tin thẻ sinh viên Lâm Quang Huy (MSSV: `0212267`), Instance ID `i-01abe8b9b987aad60` và Availability Zone `ap-southeast-1a`.

![Truy cập qua ALB - Phản hồi từ Node 1a](/images/week4/12-alb-browser-round-robin-az2.png)

---

### Kiểm nghiệm thực tế và đo kiểm chỉ số kỹ thuật:

#### 1. Kiểm tra trạng thái sức khỏe mục tiêu (Target Health) qua AWS CLI:
Sử dụng câu lệnh CLI truy vấn trực tiếp trạng thái các máy chủ trong Target Group:
```bash
aws elbv2 describe-target-health \
  --target-group-arn arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-alb-tg/01224986629b1f06 \
  --query "TargetHealthDescriptions[*].[Target.Id,Target.Port,TargetHealth.State]" \
  --output table
```
*Kết quả ghi nhận*:
```text
---------------------------------------------
|            DescribeTargetHealth           |
+----------------------+-----+--------------+
|  i-01abe8b9b987aad60 |  80 |  healthy     |
|  i-0e633e2910dd9ea8f |  80 |  healthy     |
+----------------------+-----+--------------+
```
*Đánh giá*: Cả hai máy chủ ảo được khởi tạo tự động bởi ASG đều vượt qua chu kỳ Health Check của ALB và sẵn sàng tiếp nhận lưu lượng người dùng.

#### 2. Kiểm nghiệm thuật toán luân chuyển tải Round-Robin qua cURL:
Gửi liên tiếp các yêu cầu HTTP tới tên miền DNS của ALB từ dòng lệnh:
```bash
for i in {1..4}; do
  curl -s http://huylam-alb-1974057692.ap-southeast-1.elb.amazonaws.com | grep -E "Instance ID|Availability Zone"
  echo "---"
done
```
*Kết quả ghi nhận*:
```text
  Instance ID: i-0e633e2910dd9ea8f
  Availability Zone: ap-southeast-1b
---
  Instance ID: i-01abe8b9b987aad60
  Availability Zone: ap-southeast-1a
---
  Instance ID: i-0e633e2910dd9ea8f
  Availability Zone: ap-southeast-1b
---
  Instance ID: i-01abe8b9b987aad60
  Availability Zone: ap-southeast-1a
---
```
*Đánh giá*: Thuật toán Round-Robin của Application Load Balancer phân bổ luân phiên các yêu cầu đồng đều giữa hai máy chủ ảo trên 2 vùng khả dụng khác nhau, không xảy ra hiện tượng dồn tải cục bộ.

#### 3. Kiểm chứng cơ chế bảo mật cô lập tầng máy chủ backend:
Thực hiện gửi yêu cầu HTTP trực tiếp từ Internet tới Public IP của một trong hai máy chủ backend (`47.129.221.209`):
```bash
curl --connect-timeout 5 http://47.129.221.209
```
*Kết quả ghi nhận*:
```text
curl: (28) Failed to connect to 47.129.221.209 port 80: Connection timed out
```
*Đánh giá*: Kết nối trực tiếp bị chặn hoàn toàn bởi Security Group `huylam-asg-web-sg` do nguồn yêu cầu không xuất phát từ `huylam-alb-sg`. Kiến trúc bảo mật hai tầng hoạt động chuẩn xác theo tiêu chuẩn AWS Well-Architected Framework.

---

### So sánh kiến trúc: Máy chủ đơn lẻ (Single Instance) vs Kiến trúc HA Multi-AZ:

| Tiêu chí so sánh | Máy chủ đơn lẻ (Single Instance) | Multi-AZ Auto Scaling + Application Load Balancer |
| :--- | :--- | :--- |
| **Độ sẵn sàng (Availability)** | Thấp. Khi Availability Zone gặp sự cố hoặc máy chủ bị sập, toàn bộ dịch vụ ngừng hoạt động. | **Rất cao (99.99%)**. Lưu lượng tự động chuyển sang máy chủ ở Availability Zone còn lại mà không làm gián đoạn người dùng. |
| **Khả năng co giãn (Scalability)** | Kém. Chỉ có thể mở rộng theo chiều dọc (Vertical Scaling: nâng cấp CPU/RAM), đòi hỏi phải tắt máy chủ. | **Linh hoạt tự động (Horizontal Scaling)**. Tự động bổ sung máy chủ khi lưu lượng tăng đột biến và thu hẹp khi lưu lượng giảm. |
| **Bảo mật mạng (Security)** | Máy chủ phải mở cổng trực tiếp ra ngoài Internet, đối mặt với nguy cơ tấn công dò quét cổng và DDoS. | **Bảo mật 2 tầng chuyên sâu**. Tầng máy chủ backend được giấu kín hoàn toàn phía sau bộ cân bằng tải ALB. |
| **Cân bằng tải (Load Balancing)** | Không có. Một máy chủ phải gánh toàn bộ yêu cầu người dùng. | **Tối ưu hóa tài nguyên**. Thuật toán Round-Robin phân bổ tải đều trên các máy chủ khả dụng. |
| **Quản trị vận hành (Ops Overhead)**| Thủ công. Phải tự tay tạo máy chủ mới, cài đặt phần mềm và cấu hình IP khi có nhu cầu. | **Tự động hóa hoàn toàn**. Launch Template kết hợp ASG tự sinh cấu hình và đồng bộ mã nguồn qua User Data. |

---

### Quản trị chi phí và Thực hành FinOps (FinOps Best Practices):

1. **Hiểu rõ chi phí cố định của Application Load Balancer**: Khác với tài nguyên mạng VPC thông thường vốn miễn phí, Application Load Balancer có biểu phí duy trì cố định khoảng 0.0225 USD cho mỗi giờ hoạt động (~16.2 USD/thang) cộng với phí tính theo đơn vị LCU (Load Balancer Capacity Units).
2. **Quy trình giải phóng tài nguyên triệt để sau kiểm nghiệm**:
   - Để ngăn chặn việc phát sinh chi phí ngoài ý muốn khi bài thực hành đã hoàn thành, toàn bộ các tài nguyên tính toán và cân bằng tải đã được xóa bỏ ngay lập tức theo đúng thứ tự logic phụ thuộc:
     - Lệnh xóa Auto Scaling Group: `aws autoscaling delete-auto-scaling-group --auto-scaling-group-name huylam-asg --force-delete` (tự động xóa bỏ và chuyển 2 máy chủ ảo sang trạng thái `terminated`).
     - Lệnh xóa Application Load Balancer: `aws elbv2 delete-load-balancer --load-balancer-arn arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:loadbalancer/app/huylam-alb/1d3845375aef8945`.
     - Lệnh xóa Target Group: `aws elbv2 delete-target-group --target-group-arn arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-alb-tg/01224986629b1f06`.
     - Lệnh xóa Launch Template: `aws ec2 delete-launch-template --launch-template-id lt-064a116476cc47e0a`.
     - Xóa các Security Groups liên quan để bảo toàn sự gọn gàng cho tài khoản AWS.
3. **Kết quả FinOps**: Xác nhận 100% các tài nguyên tính toán có thu phí đã trở về trạng thái giải phóng hoàn toàn, bảo toàn ngân sách Free Tier cho các bài học tiếp theo.

---

### Bài học kinh nghiệm & Kết luận:
1. **Tầm quan trọng của IMDSv2**: Sử dụng token IMDSv2 trong User Data script không chỉ tăng cường bảo mật chống lại các lỗ hổng SSRF (Server-Side Request Forgery) mà còn cho phép ứng dụng truy vấn thông tin máy chủ một cách linh hoạt, tạo ra các trang chẩn đoán mạng chính xác trong môi trường đám mây.
2. **Nguyên tắc phân tầng an ninh (Security Group Chaining)**: Việc sử dụng ID của Security Group nguồn (`huylam-alb-sg`) làm điều kiện Inbound cho Security Group đích (`huylam-asg-web-sg`) là một kỹ thuật bảo mật cốt lõi trên AWS, loại bỏ sự phụ thuộc vào các dải IP tĩnh dễ biến động.
3. **Độ sẵn sàng cao thực thụ cần Multi-AZ**: Việc dàn trải các thành phần ALB, Subnets và ASG trên ít nhất hai Availability Zones độc lập là yêu cầu thiết yếu để một hệ thống đạt chuẩn doanh nghiệp và có thể vận hành ổn định trước bất kỳ sự cố thảm họa hạ tầng nào.