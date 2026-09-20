---
title: "Worklog Tuần 3"
date: 2026-09-20
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:
* Tìm hiểu kiến trúc mạng ảo đám mây cô lập trên Amazon Virtual Private Cloud (Amazon VPC).
* Thiết kế và triển khai mạng ảo tùy biến (Custom VPC) với kiến trúc độ sẵn sàng cao Multi-AZ (2 Availability Zones, 4 Subnets).
* Cấu hình phân chia mạng con công khai (Public Subnets) và mạng con nội bộ (Private Subnets) theo chuẩn CIDR IPv4.
* Khởi tạo và gắn kết cổng Internet Gateway (IGW) vào Custom VPC.
* Thiết lập bảng định tuyến (Route Tables) điều hướng lưu lượng Internet (0.0.0.0/0) và lưu lượng cục bộ (Local VPC CIDR).
* Cấu hình cơ chế bảo mật mạng đa tầng với Security Groups (Stateful Firewall) và Network Access Control Lists (Stateless Firewall).
* Khởi tạo máy chủ ảo Amazon EC2 trong Public Subnet thuộc Custom VPC, kích hoạt gán địa chỉ Public IPv4 tự động.
* Xác thực tính liên lạc mạng (Network Connectivity), phân giải tên miền (DNS Resolution), và đo kiểm độ trễ mạng qua dòng lệnh CLI/Terminal.
* Áp dụng nguyên tắc quản trị chi phí FinOps, giải phóng tài nguyên tính toán sau thử nghiệm nhằm bảo vệ hạn mức AWS Free Tier.

### Các công việc đã triển khai trong tuần 3:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2** | - Nghiên cứu lý thuyết Amazon VPC, CIDR block, Subnetting, IPv4 addressing.<br>- Lập kế hoạch phân bổ dải mạng Multi-AZ cho Custom VPC: `10.0.0.0/16`. | Phân chia 4 subnets: 2 Public (`/20`) và 2 Private (`/20`) trải đều trên 2 Availability Zones `ap-southeast-1a` và `ap-southeast-1b`. | [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/) |
| **Thứ 3** | - Sử dụng tính năng "VPC and more" trên AWS Management Console để khởi tạo đồng bộ `huylam-vpc`.<br>- Tạo Internet Gateway `huylam-igw` và gắn kết vào VPC.<br>- Kiểm tra sơ đồ trực quan tài nguyên (VPC Resource Map). | Khởi tạo thành công VPC `vpc-0125f4d6db3fbffa6`, gắn IGW `igw-0b9db3a6eac29ede9`, hệ thống tự sinh các Subnets và Route Tables tương ứng. | [Lab 000003](https://000003.awsstudygroup.com) |
| **Thứ 4** | - Bật tính năng gán Public IPv4 tự động (Auto-assign public IP) cho Public Subnet 1 (`huylam-subnet-public1-ap-southeast-1a`).<br>- Xác thực cấu hình Route Table công khai điều hướng `0.0.0.0/0` qua IGW. | Đảm bảo các tài nguyên compute khi khởi tạo trong Public Subnet tự động nhận địa chỉ IPv4 công khai để có thể truy cập từ bên ngoài. | [Lab 000003](https://000003.awsstudygroup.com) |
| **Thứ 5** | - Tìm hiểu cơ chế tường lửa ảo cấp độ phiên (Stateful): Security Groups.<br>- Khởi tạo Security Group `huylam-vpc-web-sg` (`sg-0dbd6bbde1b366070`) thuộc `huylam-vpc`.<br>- Thiết lập Inbound Rules: TCP 22 (SSH), TCP 80 (HTTP), ICMP IPv4 (Echo Request/Ping). | Security Group được tạo lập thành công; sẵn sàng bảo vệ các máy chủ ảo ở tầng giao vận và tầng ứng dụng. | [AWS Security Groups Guide](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) |
| **Thứ 6** | - Nghiên cứu cơ chế tường lửa mạng cấp độ gói tin (Stateless): Network ACLs (NACL).<br>- Khảo sát cấu hình Default NACL `acl-09a50f9e28bc6477d` gắn với cả 4 subnets.<br>- So sánh chi tiết sự khác biệt giữa Security Groups và Network ACLs. | Nắm vững nguyên lý hoạt động song song của hai lớp bảo vệ: NACL chặn ở ranh giới Subnet, Security Group lọc ở cấp độ máy chủ ảo (ENI). | [AWS NACL Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) |
| **Thứ 7** | - Khởi tạo máy chủ ảo EC2 `huylam-vpc-test-server` (`i-02a465d3907141cfb`) trong Custom VPC.<br>- Kết nối qua EC2 Instance Connect.<br>- Thực hiện kiểm thử ICMP ping ra Internet và truy vấn HTTP request qua `curl`. | Kiểm nghiệm thành công: RTT trung bình 1.13 ms tới máy chủ DNS công cộng 8.8.8.8; phản hồi HTTP 301 Moved Permanently từ Amazon.com. | [Lab 000003](https://000003.awsstudygroup.com) |
| **Chủ Nhật**| - Đo kiểm độ trễ kết nối từ môi trường phát triển cục bộ tới Public IP máy chủ ảo.<br>- Thực hiện quy trình FinOps: Terminate máy chủ EC2 kiểm nghiệm, duy trì VPC/Subnet/IGW với chi phí 0 USD/tháng.<br>- Tổng hợp báo cáo kỹ thuật và triển khai worklog. | Đạt 0% packet loss khi ping từ ngoài vào máy chủ ảo; dọn dẹp an toàn tài nguyên tính toán để bảo toàn hạn mức Free Tier. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

---

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:

#### 1. Định danh tài khoản & Khu vực (Identity & Region):
- **AWS Account ID**: `677994024390`
- **Tên tài khoản (Account Name)**: `huylam`
- **IAM User thực thi**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)

#### 2. Mạng ảo tùy biến Amazon Custom VPC:
- **VPC Name**: `huylam-vpc`
- **VPC ID**: `vpc-0125f4d6db3fbffa6`
- **IPv4 CIDR Block**: `10.0.0.0/16` (65,536 địa chỉ IP khả dụng)
- **Tenancy**: `Default`
- **DNS Resolution**: `Enabled` (Hỗ trợ phân giải tên miền DNS nội bộ AWS)
- **DNS Hostnames**: `Enabled` (Tự động gán tên miền công khai cho tài nguyên nhận Public IP)
- **State**: `available`

#### 3. Cổng kết nối Internet Gateway:
- **Internet Gateway Name**: `huylam-igw`
- **Internet Gateway ID**: `igw-0b9db3a6eac29ede9`
- **Trạng thái gắn kết (Attachment State)**: `attached` vào VPC `vpc-0125f4d6db3fbffa6`

#### 4. Phân chia mạng con Multi-AZ (Subnets Architecture):
| Subnet Name | Subnet ID | Availability Zone | CIDR Block | Loại Subnet | Tự động gán Public IP |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `huylam-subnet-public1-ap-southeast-1a` | `subnet-0efa7c3a5818035dc` | `ap-southeast-1a` | `10.0.0.0/20` | Public | **Yes** (`MapPublicIpOnLaunch: true`) |
| `huylam-subnet-public2-ap-southeast-1b` | `subnet-0e07eb2fd44d1ac91` | `ap-southeast-1b` | `10.0.16.0/20` | Public | **No** |
| `huylam-subnet-private1-ap-southeast-1a`| `subnet-08563e499271091ab` | `ap-southeast-1a` | `10.0.128.0/20`| Private | **No** |
| `huylam-subnet-private2-ap-southeast-1b`| `subnet-07e20dc44c40fac46` | `ap-southeast-1b` | `10.0.144.0/20`| Private | **No** |

#### 5. Bảng định tuyến (Route Tables):
* **Public Route Table (`huylam-rtb-public` - ID: `rtb-012d99ed0350e956f`)**:
  - Gắn kết (Explicit Associations): `huylam-subnet-public1-ap-southeast-1a`, `huylam-subnet-public2-ap-southeast-1b`.
  - Quy tắc định tuyến:
    - `10.0.0.0/16` -> `local` (Định tuyến nội bộ giữa tất cả subnets trong VPC).
    - `0.0.0.0/0` -> `igw-0b9db3a6eac29ede9` (Định tuyến toàn bộ lưu lượng Internet ra ngoài qua IGW).
* **Private Route Table 1 (`huylam-rtb-private1-ap-southeast-1a` - ID: `rtb-0d49944883c933d2a`)**:
  - Gắn kết: `huylam-subnet-private1-ap-southeast-1a`.
  - Quy tắc định tuyến: `10.0.0.0/16` -> `local` (Không định tuyến ra ngoài Internet).
* **Private Route Table 2 (`huylam-rtb-private2-ap-southeast-1b` - ID: `rtb-00f6e53a047aafc1f`)**:
  - Gắn kết: `huylam-subnet-private2-ap-southeast-1b`.
  - Quy tắc định tuyến: `10.0.0.0/16` -> `local` (Không định tuyến ra ngoài Internet).

#### 6. Tường lửa mạng ảo Security Group (`huylam-vpc-web-sg` - ID: `sg-0dbd6bbde1b366070`):
- **VPC trực thuộc**: `vpc-0125f4d6db3fbffa6`
- **Inbound Rules**:
  - SSH (TCP 22): Nguồn `0.0.0.0/0` (Cho phép quản trị từ xa qua SSH / EC2 Instance Connect).
  - HTTP (TCP 80): Nguồn `0.0.0.0/0` (Cho phép truy cập máy chủ web công khai).
  - All ICMP - IPv4: Nguồn `0.0.0.0/0` (Cho phép ping kiểm tra độ trễ mạng và xác định tính liên lạc).
- **Outbound Rules**:
  - All Traffic (Tất cả giao thức và cổng): Đích `0.0.0.0/0` (Cho phép máy chủ khởi tạo kết nối ra ngoài để cập nhật phần mềm, truy vấn DNS, tải gói tin).

#### 7. Tường lửa mạng con Network ACL (`acl-09a50f9e28bc6477d`):
- **VPC trực thuộc**: `vpc-0125f4d6db3fbffa6`
- **Gắn kết**: Cả 4 Subnets của `huylam-vpc`.
- **Inbound Rules**:
  - Rule 100: Type `All traffic`, Protocol `All`, Port range `All`, Source `0.0.0.0/0`, Action `Allow`.
  - Rule `*`: Type `All traffic`, Protocol `All`, Port range `All`, Source `0.0.0.0/0`, Action `Deny` (Mặc định chặn ngầm định).
- **Outbound Rules**:
  - Rule 100: Type `All traffic`, Protocol `All`, Port range `All`, Destination `0.0.0.0/0`, Action `Allow`.
  - Rule `*`: Type `All traffic`, Protocol `All`, Port range `All`, Destination `0.0.0.0/0`, Action `Deny`.

#### 8. Máy chủ ảo kiểm nghiệm Amazon EC2 (`huylam-vpc-test-server` - ID: `i-02a465d3907141cfb`):
- **Instance Type**: `t3.micro` (Hạn mức Free Tier)
- **Hệ điều hành**: Amazon Linux 2023 AMI (`al2023-ami-2023.6.20260218.0-kernel-6.1-x86_64`)
- **VPC / Subnet**: `huylam-vpc` / `huylam-subnet-public1-ap-southeast-1a`
- **Public IPv4**: `54.151.162.47`
- **Private IPv4**: `10.0.14.174`
- **Security Group**: `huylam-vpc-web-sg`
- **Trạng thái vòng đời**: Đã khởi chạy thành công (`running`), thực hiện đo kiểm toàn trình, sau đó thực hiện lệnh Terminate an toàn (`shutting-down` -> `terminated`).

---

### Hình ảnh minh chứng hoàn thành thực tế (Proof of Work & Verifications)

#### 1. Cấu hình thiết lập Custom VPC và bản xem trước tài nguyên (VPC and more)
- **Mô tả**: Sử dụng trình tạo hợp nhất "VPC and more" để thiết lập tên VPC `huylam-vpc`, dải mạng IPv4 CIDR `10.0.0.0/16`, lựa chọn 2 Availability Zones với 2 Public Subnets và 2 Private Subnets.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, ô Name tag, IPv4 CIDR block, cấu hình số lượng Availability Zones, Subnets và sơ đồ Resource Map trực quan.

![Cấu hình thiết lập Custom VPC](/images/week3/01-vpc-create-settings-preview.png)

---

#### 2. Cấu hình NAT Gateway, VPC Endpoints và tùy chọn DNS
- **Mô tả**: Nhằm tối ưu hóa chi phí (FinOps), tùy chọn NAT Gateways được đặt là `None` và VPC Endpoints là `None` để tránh phí duy trì theo giờ. Các tùy chọn DNS Hostnames và DNS Resolution được kích hoạt để đảm bảo phân giải tên miền.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, ô tùy chọn NAT gateways (None), VPC endpoints (None), Enable DNS hostnames, Enable DNS resolution và nút *Create VPC*.

![Cấu hình NAT và DNS Options](/images/week3/02-vpc-create-nat-dns-options.png)

---

#### 3. Sơ đồ tài nguyên mạng trực quan của VPC (VPC Resource Map)
- **Mô tả**: Kiểm tra chi tiết `huylam-vpc` (`vpc-0125f4d6db3fbffa6`) trên AWS Console. Sơ đồ Resource Map hiển thị mối liên kết hoàn chỉnh giữa VPC, 4 Subnets, các Route Tables và Internet Gateway `huylam-igw`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, bảng chi tiết VPC ID cùng sơ đồ luồng dữ liệu kết nối từ Subnets qua Route Tables đến IGW.

![Sơ đồ tài nguyên VPC Resource Map](/images/week3/03-vpc-resource-map.png)

---

#### 4. Kích hoạt tính năng tự động gán Public IPv4 cho Public Subnet
- **Mô tả**: Truy cập cấu hình subnet `huylam-subnet-public1-ap-southeast-1a` (`subnet-0efa7c3a5818035dc`), kích hoạt tùy chọn *Enable auto-assign public IPv4 address* để mọi máy chủ ảo khởi tạo trong subnet này tự động nhận địa chỉ IP truy cập Internet.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, định danh Subnet ID, ô tích chọn *Enable auto-assign public IPv4 address*.

![Kích hoạt Auto-assign Public IP cho Subnet](/images/week3/04-subnet-enable-auto-assign-public-ip.png)

---

#### 5. Thiết lập quy tắc Inbound cho Security Group của Custom VPC
- **Mô tả**: Khởi tạo Security Group `huylam-vpc-web-sg` trực thuộc `huylam-vpc` với các quy tắc Inbound cho phép SSH (cổng 22), HTTP (cổng 80) và toàn bộ gói tin ICMP IPv4 từ nguồn `0.0.0.0/0`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, tên Security Group, liên kết VPC `huylam-vpc`, và danh sách 3 quy tắc Inbound Rules.

![Thiết lập Security Group Inbound Rules](/images/week3/05-security-group-create-rules.png)

---

#### 6. Xác nhận tạo lập thành công Security Group
- **Mô tả**: AWS ghi nhận tạo thành công Security Group `huylam-vpc-web-sg` với định danh `sg-0dbd6bbde1b366070`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thanh thông báo thành công màu xanh lá, bảng chi tiết Security Group Details và danh sách Inbound Rules đã lưu.

![Xác nhận Security Group tạo thành công](/images/week3/06-security-group-created-success.png)

---

#### 7. Cấu hình mạng khi khởi tạo máy chủ ảo Amazon EC2
- **Mô tả**: Trong trình khởi tạo EC2 Launch Instance, chọn VPC đích là `huylam-vpc`, Subnet `huylam-subnet-public1-ap-southeast-1a`, xác nhận Auto-assign public IP ở trạng thái Enable, và gán Security Group có sẵn `huylam-vpc-web-sg`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, khung cấu hình Network Settings chi tiết và bảng Summary tóm tắt cấu hình máy chủ.

![Cấu hình Network Settings cho EC2 Instance](/images/week3/07-ec2-launch-custom-vpc-network-settings.png)

---

#### 8. Thông báo khởi chạy máy chủ ảo EC2 thành công
- **Mô tả**: Hệ thống AWS ghi nhận lệnh khởi tạo thành công máy chủ ảo với mã định danh Instance ID `i-02a465d3907141cfb`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và thanh thông báo *Successfully initiated launch of instance*.

![Thông báo khởi chạy EC2 thành công](/images/week3/08-ec2-launch-success.png)

---

#### 9. Kiểm tra bảng tóm tắt máy chủ ảo EC2 đang hoạt động (Running State)
- **Mô tả**: Máy chủ `huylam-vpc-test-server` đạt trạng thái *Running*, nhận Public IPv4 `54.151.162.47`, Private IPv4 `10.0.14.174`, trực thuộc đúng `huylam-vpc` và Subnet công khai.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và khung Instance Summary chi tiết chứa đầy đủ các trường định danh mạng.

![Bảng tóm tắt EC2 Instance Summary](/images/week3/09-ec2-instance-summary-running.png)

---

#### 10. Kiểm tra kết nối mạng Internet và phân giải DNS qua EC2 Instance Connect
- **Mô tả**: Truy cập terminal máy chủ ảo qua trình duyệt, thực hiện lệnh `ping -c 4 8.8.8.8` (đạt 0% packet loss, RTT 1.13 ms) và lệnh `curl -I https://amazon.com` (nhận mã phản hồi `HTTP/1.1 301 Moved Permanently`).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, cửa sổ dòng lệnh với kết quả thực thi lệnh ping/curl, và thanh trạng thái hiển thị Instance ID `i-02a465d3907141cfb` cùng Public IP `54.151.162.47`.

![Kiểm tra kết nối qua EC2 Instance Connect](/images/week3/10-ec2-instance-connect-terminal-test.png)

---

#### 11. Khảo sát cấu hình Network Access Control List (NACL) của Custom VPC
- **Mô tả**: Kiểm tra Network ACL mặc định `acl-09a50f9e28bc6477d` quản lý cả 4 Subnets của `huylam-vpc`. Quy tắc Inbound Rule 100 cho phép mọi lưu lượng và Rule `*` chặn toàn bộ lưu lượng không xác định.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, định danh NACL ID, số lượng Subnets gắn kết, VPC ID và bảng Inbound Rules chi tiết.

![Khảo sát cấu hình Network ACL](/images/week3/11-vpc-network-acl-inbound-rules.png)

---

### Kiểm nghiệm thực tế và đo kiểm chỉ số kỹ thuật:

#### 1. Đo kiểm độ trễ mạng từ môi trường cục bộ tới máy chủ ảo:
Từ terminal máy trạm phát triển, thực hiện kiểm tra ping trực tiếp đến địa chỉ Public IPv4 `54.151.162.47`:
```bash
ping -c 4 54.151.162.47
```
*Kết quả ghi nhận*:
```text
PING 54.151.162.47 (54.151.162.47): 56 data bytes
64 bytes from 54.151.162.47: icmp_seq=0 ttl=114 time=49.123 ms
64 bytes from 54.151.162.47: icmp_seq=1 ttl=114 time=48.910 ms
64 bytes from 54.151.162.47: icmp_seq=2 ttl=114 time=49.450 ms
64 bytes from 54.151.162.47: icmp_seq=3 ttl=114 time=49.020 ms

--- 54.151.162.47 ping statistics ---
4 packets transmitted, 4 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 48.910/49.126/49.450/0.201 ms
```
*Đánh giá*: Kết nối mạng xuyên suốt từ Việt Nam tới AWS Region Singapore (`ap-southeast-1`) với độ trễ cực thấp (~49 ms) và tỷ lệ mất gói bằng 0%. Quy tắc ICMP trong Security Group hoạt động chính xác.

#### 2. Đo kiểm kết nối hướng ra ngoài (Egress Connectivity) từ máy chủ ảo:
Từ phiên làm việc EC2 Instance Connect bên trong máy chủ:
```bash
ping -c 4 8.8.8.8
```
*Kết quả ghi nhận*:
```text
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=1.12 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=1.11 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=1.20 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=114 time=1.10 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 1.103/1.133/1.200/0.038 ms
```
*Đánh giá*: Máy chủ ảo định tuyến qua Internet Gateway `huylam-igw` đạt độ trễ cực nhanh (~1.13 ms) tới máy chủ DNS công cộng.

#### 3. Đo kiểm khả năng phân giải tên miền DNS và truy vấn giao thức HTTP/HTTPS:
```bash
curl -I https://amazon.com
```
*Kết quả ghi nhận*:
```text
HTTP/1.1 301 Moved Permanently
Server: Server
Date: Sun, 20 Sep 2026 09:39:26 GMT
Content-Type: text/html
Connection: keep-alive
Location: https://www.amazon.com/
x-amz-rid: 7S4DNVXPQM3D0EMFTPF1
Vary: User-Agent,Accept-Encoding
```
*Đánh giá*: DNS Resolver nội bộ của AWS VPC phân giải tên miền thành công, máy chủ thiết lập phiên kết nối TLS/HTTPS và nhận phản hồi chuyển hướng chuẩn 301.

---

### So sánh kiến trúc bảo mật nhiều lớp (Defense in Depth):

| Đặc tính kỹ thuật | Security Group (Tường lửa ảo) | Network ACL (NACL) |
| :--- | :--- | :--- |
| **Cấp độ áp dụng** | Áp dụng ở cấp độ giao diện mạng máy chủ ảo (ENI / Instance level). | Áp dụng ở cấp độ mạng con (Subnet level). |
| **Trạng thái kết nối** | **Stateful**: Tự động cho phép lưu lượng phản hồi bất kể quy tắc Inbound/Outbound. | **Stateless**: Phải mở tường minh cả hai chiều Inbound và Outbound (bao gồm Ephemeral Ports). |
| **Quy tắc cho phép / từ chối**| Chỉ hỗ trợ quy tắc **Allow**. Mọi lưu lượng không nằm trong danh sách sẽ bị từ chối ngầm định. | Hỗ trợ cả hai quy tắc **Allow** và **Deny**. |
| **Thứ tự xử lý** | Đánh giá đồng thời toàn bộ các quy tắc trước khi ra quyết định. | Đánh giá tuần tự theo số thứ tự quy tắc (Rule Number) từ nhỏ đến lớn. |
| **Thời điểm kích hoạt** | Chỉ kiểm tra khi gói tin đã vượt qua tầng NACL của Subnet. | Lớp phòng thủ đầu tiên khi gói tin đi vào hoặc đi ra khỏi Subnet. |

---

### Quản trị chi phí và Thực hành FinOps (FinOps Best Practices):

1. **Tránh khởi tạo NAT Gateway không cần thiết**: NAT Gateway có chi phí duy trì cố định khoảng 0.045 USD/giờ (~32.4 USD/tháng cho mỗi NAT GW). Trong bài thực hành, tùy chọn NAT Gateway được đặt là `None`.
2. **Không phát sinh chi phí cho VPC cốt lõi**: Trên AWS, các tài nguyên VPC, Subnets, Route Tables, Internet Gateway, Network ACLs và Security Groups hoàn toàn miễn phí khi không gắn kèm Elastic IP hoặc NAT Gateway.
3. **Giải phóng tài nguyên tính toán ngay sau đo kiểm**: Máy chủ ảo `i-02a465d3907141cfb` đã được chuyển sang trạng thái `terminated` sau khi thu thập đầy đủ minh chứng thực tế, bảo vệ trọn vẹn số giờ sử dụng miễn phí của AWS Free Tier.

---

### Bài học kinh nghiệm & Kết luận:
1. **Kiến trúc Multi-AZ chuẩn Production**: Việc tổ chức 2 Public Subnets và 2 Private Subnets phân tách trên 2 Availability Zones là nền tảng cốt lõi để triển khai hệ thống có khả năng chịu lỗi cao (High Availability) và cân bằng tải (Application Load Balancer).
2. **Tự động hóa gán IP**: Cần chủ động kiểm tra cờ `MapPublicIpOnLaunch` trên Public Subnet để tránh tình trạng máy chủ ảo khởi động không có Public IP khiến không thể truy cập từ Internet.
3. **Nguyên tắc Defense in Depth**: Kết hợp hài hòa giữa NACL (chặn IP độc hại ở cấp mạng con) và Security Group (kiểm soát chi tiết cổng dịch vụ ở cấp máy chủ) mang lại an toàn bảo mật tối ưu cho toàn bộ hạ tầng đám mây.