---
title: "Điều kiện chuẩn bị"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Mục tiêu chuyên đề

Thiết lập và kiểm tra toàn diện môi trường làm việc cục bộ cũng như quyền truy cập vào AWS Management Console, chuẩn bị đầy đủ các công cụ lập trình, SDK và kho mã nguồn của nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** trước khi triển khai hạ tầng.

---

## 1. Yêu cầu tài khoản & quyền hạn AWS

1. **Tài khoản AWS (AWS Account)**:
   * Tài khoản đang hoạt động (ví dụ: `huylam`, Account ID: `677994024390`).
   * Khu vực triển khai mục tiêu: **`ap-southeast-1` (Asia Pacific - Singapore)**.
   * Kích hoạt cảnh báo ngân sách AWS Budgets để kiểm soát chi phí ở mức 0.00 USD.
2. **Quyền hạn IAM (IAM Identity)**:
   * Sử dụng người dùng IAM quản trị phát triển (`dev_admin`) có quyền cấu hình VPC, EC2, Application Load Balancer, S3, DynamoDB, Systems Manager, Lambda và CloudWatch.
   * Tuyệt đối không sử dụng tài khoản gốc (Root Account) cho các thao tác triển khai hàng ngày theo chuẩn AWS Well-Architected Framework.

![Xác thực tài khoản AWS huylam tại khu vực Singapore](/images/week1/01-account-huylam.png)

![Cấu hình ngân sách AWS Budgets kiểm soát chi phí 0.00 USD](/images/week1/03-aws-budgets.png)

---

## 2. Công cụ phát triển trên máy cục bộ

Trước khi bắt đầu, đảm bảo máy trạm của bạn đã cài đặt các công cụ sau:

| Công cụ | Phiên bản khuyến nghị | Mục đích sử dụng |
| :--- | :--- | :--- |
| **Python** | Python 3.11+ | Môi trường runtime chính của backend, engine bóc tách và dịch thuật |
| **pip & venv** | Mặc định theo Python 3.11 | Quản lý gói thư viện và môi trường ảo độc lập |
| **Git** | 2.40+ | Quản lý mã nguồn và kéo kho lưu trữ từ GitHub |
| **Docker Desktop** | 24.0+ | Đóng gói ứng dụng Web Studio thành OCI Container |
| **AWS CLI v2** | 2.15+ | Kiểm tra kết nối và tương tác với tài nguyên AWS từ dòng lệnh |
| **Trình duyệt Web** | Chrome, Safari, Firefox | Truy cập AWS Management Console và kiểm thử giao diện Web Studio |

---

## 3. Các bước kiểm tra môi trường

### Bước 3.1: Kiểm tra phiên bản công cụ cục bộ
Mở Terminal trên máy trạm và thực thi các lệnh kiểm tra:

```bash
# Kiểm tra Python và Pip
python3 --version
pip3 --version

# Kiểm tra Git
git --version

# Kiểm tra Docker
docker --version

# Kiểm tra AWS CLI
aws --version
```

**Checkpoint**: Tất cả các lệnh đều trả về phiên bản hợp lệ mà không có thông báo lỗi.

---

### Bước 3.2: Kiểm tra cấu hình AWS CLI và quyền truy cập
Xác thực danh tính IAM trên máy cục bộ kết nối đến khu vực `ap-southeast-1`:

```bash
# Kiểm tra danh tính người dùng AWS
aws sts get-caller-identity
```

**Kết quả kỳ vọng**:
```json
{
    "UserId": "AIDA...DEVADMIN",
    "Account": "677994024390",
    "Arn": "arn:aws:iam::677994024390:user/dev_admin"
}
```

![Kiểm tra danh tính AWS STS get-caller-identity trên terminal](/images/week1/04-aws-cli-verified.png)

---

### Bước 3.3: Tải mã nguồn dự án từ GitHub
Kéo kho mã nguồn chính thức của dự án về máy cục bộ:

```bash
# Kéo kho mã nguồn dự án
git clone https://github.com/Lamhuy0489/aws.git
cd aws

# Khởi tạo môi trường ảo Python 3.11
python3.11 -m venv venv
source venv/bin/activate

# Cài đặt các thư viện phụ thuộc
pip install --upgrade pip
pip install -r requirements.txt
```

---

## 4. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn đã có:
- Quyền truy cập quản trị vào AWS Management Console tại khu vực `ap-southeast-1`.
- Cấu hình AWS CLI với tài khoản định danh `dev_admin` trên Account `677994024390`.
- Môi trường Python 3.11 và Docker Desktop sẵn sàng hoạt động.
- Kho mã nguồn `Lamhuy0489/aws` đã được kéo về máy và cài đặt đầy đủ các thư viện phụ thuộc.