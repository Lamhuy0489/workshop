---
title: "Triển khai máy chủ ứng dụng EC2"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.7.2. </b> "
---

### Mục tiêu thực hành

Khởi chạy máy chủ ảo Amazon EC2 `huylam-ocr-web-server` với cấu hình bảo mật IAM Instance Profile, kết nối quản trị an toàn qua AWS Systems Manager Session Manager (không mở cổng SSH 22), triển khai mã nguồn Web Studio từ GitHub và thiết lập dịch vụ daemon systemd Gunicorn tự động phục hồi.

---

## 1. Khởi tạo IAM Role cho EC2 (huylam-ssm-role)

Áp dụng phương pháp phân quyền không lưu trữ khóa tĩnh (Zero Hardcoded Credentials):
1. Truy cập **IAM Console -> Roles -> Create role**.
2. **Trusted entity type**: Chọn **AWS service**, Use case: **EC2**.
3. **Permissions policies**: Tìm và đánh dấu chọn 3 chính sách:
   * **`AmazonSSMManagedInstanceCore`**: Cho phép quản trị máy chủ qua AWS Systems Manager Session Manager.
   * **`AmazonS3FullAccess`**: Cho phép ứng dụng đọc ghi tài liệu vào S3 bucket.
   * **`AmazonDynamoDBFullAccess`**: Cho phép ứng dụng ghi bản ghi tiến trình vào DynamoDB.
4. **Role name**: Đặt tên `huylam-ssm-role`.
5. Nhấp **Create role**.

---

## 2. Khởi chạy máy chủ ảo Amazon EC2 (huylam-ocr-web-server)

1. Truy cập **EC2 Console -> Instances -> Launch an instance**.
2. **Name**: `huylam-ocr-web-server`.
3. **Application and OS Images (Amazon Machine Image)**:
   * Chọn **Amazon Linux**, AMI: **Amazon Linux 2023 AMI** (Free Tier eligible).
4. **Instance type**: Chọn **`t2.micro`** (1 vCPU, 1 GiB RAM - Free Tier eligible).
5. **Key pair (login)**: Chọn **Proceed without a key pair (Not recommended)**.
   * *Lý do kỹ thuật*: Toàn bộ quá trình truy cập và quản trị sẽ được thực hiện qua AWS Systems Manager Session Manager, loại bỏ hoàn toàn việc lưu trữ tệp `.pem` cục bộ và rủi ro mất khóa.
6. **Network settings**:
   * Nhấp **Edit**.
   * **VPC**: Chọn **`huylam-vpc`**.
   * **Subnet**: Chọn **`huylam-subnet-public1-ap-southeast-1a`**.
   * **Auto-assign public IP**: Chọn **Enable**.
   * **Firewall (security groups)**: Chọn **Select existing security group** và chọn **`huylam-web-sg`** (`sg-0ff9ea20c6c3a8dc8`).
7. **Advanced details**:
   * **IAM instance profile**: Chọn **`huylam-ssm-role`**.
8. Nhấp nút **Launch instance**.
9. Ghi nhận mã Instance ID được tạo (ví dụ: `i-0566e1eedaacea52d`).

---

## 3. Đăng ký máy chủ vào Target Group

1. Truy cập **EC2 Console -> Target Groups -> huylam-ocr-tg**.
2. Chuyển sang thẻ **Targets** và nhấp nút **Register targets**.
3. Tại bảng **Available instances**, đánh dấu chọn `huylam-ocr-web-server` (`i-0566e1eedaacea52d`).
4. Nhập cổng kết nối: **`5000`**.
5. Nhấp **Include as pending below**, sau đó nhấp **Register pending targets**.

---

## 4. Kết nối và Triển khai qua AWS Systems Manager Session Manager

### Bước 4.1: Mở phiên kết nối Session Manager
1. Truy cập **EC2 Console -> Instances**, chọn `huylam-ocr-web-server`.
2. Nhấp nút **Connect** ở góc phải trên.
3. Chọn thẻ **Session Manager** và nhấp nút **Connect**.
4. Trình duyệt sẽ mở một cửa sổ dòng lệnh bash trực tiếp trên máy chủ mà không yêu cầu cổng SSH 22.

---

### Bước 4.2: Cài đặt môi trường runtime và tải mã nguồn
Thực thi tuần tự các lệnh sau trong phiên Session Manager:

```bash
# Chuyển sang quyền quản trị root
sudo su

# Cập nhật hệ thống và cài đặt Python 3.11, Git
dnf update -y
dnf install -y git python3.11 python3.11-pip

# Khởi tạo thư mục ứng dụng và kéo mã nguồn từ GitHub
mkdir -p /opt/huylam-ocr && cd /opt/huylam-ocr
git clone https://github.com/Lamhuy0489/aws.git .

# Thiết lập môi trường ảo Python 3.11
python3.11 -m venv venv
source venv/bin/activate

# Cài đặt các thư viện phụ thuộc và máy chủ WSGI Gunicorn
pip install --upgrade pip
pip install -r requirements.txt gunicorn
```

---

### Bước 4.3: Cấu hình systemd service tự động khởi chạy (huylam-ocr.service)
Tạo tệp cấu hình dịch vụ nền để hệ điều hành tự động quản lý vòng đời ứng dụng:

```bash
cat << 'EOF' > /etc/systemd/system/huylam-ocr.service
[Unit]
Description=Huylam OCR Web Studio Platform
After=network.target

[Service]
User=root
WorkingDirectory=/opt/huylam-ocr
Environment="PORT=5000"
Environment="AWS_DEFAULT_REGION=ap-southeast-1"
ExecStart=/opt/huylam-ocr/venv/bin/gunicorn -w 2 -b 0.0.0.0:5000 src.frontend.server:app
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

Kích hoạt và khởi động dịch vụ:

```bash
# Nạp lại cấu hình systemd
systemctl daemon-reload

# Kích hoạt dịch vụ khởi động cùng hệ điều hành và chạy ngay lập tức
systemctl enable --now huylam-ocr.service

# Kiểm tra trạng thái hoạt động
systemctl status huylam-ocr.service
```

**Checkpoint**: Dòng trạng thái hiển thị `Active: active (running)`.

---

## 5. Xác nhận trạng thái Healthy trên Target Group

1. Quay lại AWS Console: **EC2 -> Target Groups -> huylam-ocr-tg**.
2. Chọn thẻ **Targets**.
3. Đợi khoảng 30 - 60 giây để ALB thực hiện kiểm tra sức khỏe qua đường dẫn `/login`.
4. Xác nhận cột **Health status** chuyển sang màu xanh: **Healthy (1/1)**.

---

## 6. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Máy chủ EC2 `huylam-ocr-web-server` chạy Amazon Linux 2023 được bảo vệ an toàn.
- Quản trị viên truy cập từ xa qua AWS Systems Manager Session Manager không cần khóa SSH.
- Ứng dụng Web Studio vận hành ổn định qua dịch vụ nền Gunicorn trên cổng nội bộ 5000.
- Target Group ghi nhận trạng thái **Healthy (1/1)**, sẵn sàng nhận lưu lượng từ Load Balancer.