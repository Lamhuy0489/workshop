---
title: "Tạo VPC"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

### Mục tiêu thực hành

Khởi tạo mạng ảo Amazon Virtual Private Cloud (VPC) mang tên `huylam-vpc` tại khu vực `ap-southeast-1` (Singapore) và thiết lập 2 Public Subnet trên 2 Vùng sẵn sàng độc lập (`ap-southeast-1a` và `ap-southeast-1b`) phục vụ cơ chế cân bằng tải sẵn sàng cao Multi-AZ.

---

## 1. Khởi tạo Amazon VPC (huylam-vpc)

### Các bước thực hiện trên AWS Console:
1. Đăng nhập vào AWS Management Console tại khu vực **ap-southeast-1 (Singapore)**.
2. Tìm kiếm và truy cập dịch vụ: **VPC -> Your VPCs -> Create VPC**.
3. Chọn tùy chọn **VPC only** và nhập các thông số sau:

| Thuộc tính (Attribute) | Giá trị cấu hình (Configured Value) | Giải thích kỹ thuật |
| :--- | :--- | :--- |
| **Resources to create** | VPC only | Tạo mạng ảo độc lập có thể tùy biến cấu hình chi tiết |
| **Name tag** | `huylam-vpc` | Tên định danh VPC theo chuẩn đặt tên dự án |
| **IPv4 CIDR block** | `10.0.0.0/16` | Cung cấp dải mạng riêng tư gồm 65,536 địa chỉ IP |
| **IPv6 CIDR block** | No IPv6 CIDR block | Chỉ sử dụng ngăn xếp địa chỉ IPv4 |
| **Tenancy** | Default | Chia sẻ phần cứng máy chủ theo định mức tiết kiệm FinOps |

4. Nhấp nút **Create VPC**.

![Cấu hình tạo VPC huylam-vpc](/images/week3/01-vpc-create-settings-preview.png)

---

## 2. Kích hoạt thuộc tính DNS Hostnames & DNS Resolution

Để máy chủ EC2 và Application Load Balancer có thể phân giải tên miền nội bộ và dịch vụ AWS an toàn:
1. Trong danh sách **Your VPCs**, chọn `huylam-vpc`.
2. Nhấp menu **Actions -> Edit VPC settings**.
3. Tại phần **DNS settings**, đánh dấu chọn cả hai mục:
   * **Enable DNS resolution**: Cho phép phân giải truy vấn DNS nội bộ AWS.
   * **Enable DNS hostnames**: Tự động gán tên miền công khai cho các instance có IP công khai.
4. Nhấp **Save changes**.

![Kích hoạt DNS Hostnames và DNS Resolution](/images/week3/02-vpc-create-nat-dns-options.png)

---

## 3. Tạo 2 Public Subnet trên đa vùng sẵn sàng (Multi-AZ)

Application Load Balancer yêu cầu tối thiểu 2 Subnet đặt trên 2 Availability Zone khác nhau để đảm bảo dự phòng lỗi.

### Tạo Subnet 1 (`ap-southeast-1a`):
* Truy cập **VPC -> Subnets -> Create subnet**.
* Chọn **VPC ID**: `huylam-vpc`.
* **Subnet name**: `huylam-subnet-public1-ap-southeast-1a`.
* **Availability Zone**: `ap-southeast-1a`.
* **IPv4 CIDR block**: `10.0.8.0/21` (Cung cấp 2,048 địa chỉ IP).
* Bật tính năng tự động gán IPv4: Chọn subnet -> **Actions -> Edit subnet settings -> Enable auto-assign public IPv4 address**.

### Tạo Subnet 2 (`ap-southeast-1b`):
* Truy cập **VPC -> Subnets -> Create subnet**.
* Chọn **VPC ID**: `huylam-vpc`.
* **Subnet name**: `huylam-subnet-public2-ap-southeast-1b`.
* **Availability Zone**: `ap-southeast-1b`.
* **IPv4 CIDR block**: `10.0.16.0/21` (Cung cấp 2,048 địa chỉ IP).
* Bật tính năng tự động gán IPv4: Chọn subnet -> **Actions -> Edit subnet settings -> Enable auto-assign public IPv4 address**.

![Sơ đồ tài nguyên VPC Resource Map và phân bố Subnet](/images/week3/03-vpc-resource-map.png)

![Kích hoạt tự động gán IPv4 công khai cho Subnet](/images/week3/04-subnet-enable-auto-assign-public-ip.png)

---

## 4. Kết quả mong đợi

Sau khi hoàn thành phần này:
- VPC `huylam-vpc` (`10.0.0.0/16`) đã được tạo thành công ở trạng thái **Available**.
- DNS Resolution và DNS Hostnames đều được kích hoạt.
- 2 Public Subnet Multi-AZ (`ap-southeast-1a` và `ap-southeast-1b`) đã sẵn sàng để gắn vào Internet Gateway và Application Load Balancer.