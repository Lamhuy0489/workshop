---
title: "Cấu hình mạng"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

### Mục tiêu thực hành

Cấu hình Internet Gateway, thiết lập bảng định tuyến Route Table công khai và xây dựng chuỗi bảo mật phân tầng (Security Group Chaining) để bảo vệ máy chủ ứng dụng Web Studio sau Application Load Balancer.

---

## 1. Cấu hình Internet Gateway (huylam-igw)

Internet Gateway đóng vai trò là cửa ngõ giao tiếp hai chiều giữa các tài nguyên trong VPC với mạng Internet công cộng.

### Các bước thực hiện:
1. Truy cập **VPC Console -> Internet Gateways -> Create internet gateway**.
2. Đặt tên định danh: `huylam-igw`.
3. Nhấp **Create internet gateway**.
4. Sau khi tạo thành công, chọn menu **Actions -> Attach to VPC**.
5. Chọn VPC đích: `huylam-vpc` và nhấp **Attach internet gateway**.
6. Kiểm tra trạng thái Internet Gateway chuyển sang **Attached**.

---

## 2. Cấu hình Bảng định tuyến công khai (huylam-rtb-public)

Bảng định tuyến chịu trách nhiệm điều phối lưu lượng mạng từ các Subnet ra bên ngoài Internet thông qua `huylam-igw`.

### Các bước thực hiện:
1. Truy cập **VPC Console -> Route Tables -> Create route table**.
2. Nhập tên: `huylam-rtb-public`, chọn VPC: `huylam-vpc`.
3. Nhấp **Create route table**.
4. Chuyển sang thẻ **Routes -> Edit routes**:
   * Nhấp **Add route**.
   * **Destination**: `0.0.0.0/0` (Toàn bộ lưu lượng Internet ngoại vi).
   * **Target**: Chọn **Internet Gateway** và chọn `huylam-igw`.
   * Nhấp **Save changes**.
5. Chuyển sang thẻ **Subnet associations -> Edit subnet associations**:
   * Đánh dấu chọn cả 2 Subnet: `huylam-subnet-public1-ap-southeast-1a` và `huylam-subnet-public2-ap-southeast-1b`.
   * Nhấp **Save associations**.

---

## 3. Thiết lập chuỗi bảo mật phân tầng (Security Group Chaining)

Áp dụng nguyên tắc an ninh tối thiểu (Principle of Least Privilege) của AWS Well-Architected Framework:

```text
[ Internet Client ]
       │
       ▼ Inbound HTTP: 80 (0.0.0.0/0)
┌─────────────────────────────────┐
│     huylam-alb-sg (ALB)         │
└────────────────┬────────────────┘
                 │
                 ▼ Inbound TCP: 5000 (Source: sg-0dca819306a96bfdb)
┌─────────────────────────────────┐
│     huylam-web-sg (EC2)         │
└─────────────────────────────────┘
```

### Bước 3.1: Tạo Security Group cho ALB (huylam-alb-sg)
1. Truy cập **EC2 Console -> Network & Security -> Security Groups -> Create security group**.
2. **Security group name**: `huylam-alb-sg`.
3. **Description**: `Security group for Application Load Balancer`.
4. **VPC**: Chọn `huylam-vpc`.
5. **Inbound rules**:
   * Type: **HTTP**, Port: `80`, Source: `Anywhere-IPv4` (`0.0.0.0/0`), Description: `Allow public HTTP access`.
6. **Outbound rules**:
   * Giữ mặc định: **All traffic** (`0.0.0.0/0`).
7. Nhấp **Create security group**.
8. Ghi lại mã định danh được tạo (ví dụ: `sg-0dca819306a96bfdb`).

### Bước 3.2: Tạo Security Group cho Web Server (huylam-web-sg)
1. Nhấp **Create security group**.
2. **Security group name**: `huylam-web-sg`.
3. **Description**: `Security group for EC2 Web Studio behind ALB`.
4. **VPC**: Chọn `huylam-vpc`.
5. **Inbound rules**:
   * Quy tắc 1 (Ứng dụng Web Studio):
     * Type: **Custom TCP**, Port: `5000`.
     * Source: Chọn **Custom** và nhập mã Security Group của ALB (`huylam-alb-sg` hoặc `sg-0dca819306a96bfdb`).
     * Description: `Allow traffic only from ALB`.
   * Quy tắc 2 (Quản trị dự phòng SSH):
     * Type: **SSH**, Port: `22`, Source: `0.0.0.0/0` (hoặc giới hạn IP của quản trị viên).
6. **Outbound rules**:
   * Thêm quy tắc: Type: **All traffic**, Destination: `0.0.0.0/0` (Đảm bảo máy chủ EC2 có thể kết nối Internet để tải các gói Python và kéo kho Git).
7. Nhấp **Create security group**.

---

## 4. Kết quả mong đợi

Sau khi hoàn thành phần này:
- `huylam-igw` đã liên kết thành công vào `huylam-vpc`.
- `huylam-rtb-public` chuyển tiếp lưu lượng Internet `0.0.0.0/0` cho cả 2 Subnet Multi-AZ.
- `huylam-alb-sg` sẵn sàng tiếp nhận lưu lượng HTTP cổng 80 từ toàn cầu.
- `huylam-web-sg` tạo lập hàng rào cô lập an toàn, chỉ cho phép cổng 5000 tiếp nhận traffic từ ALB.