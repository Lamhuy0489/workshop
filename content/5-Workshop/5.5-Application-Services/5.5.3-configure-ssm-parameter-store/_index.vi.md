---
title: "Quản trị tham số với AWS SSM Parameter Store"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

### Mục tiêu thực hành

Quản lý tập trung và bảo mật toàn bộ cấu hình hệ thống cùng các khóa API bí mật bằng **AWS Systems Manager Parameter Store** dưới dạng tham số mã hóa `SecureString` tại đường dẫn `/huylam-ocr/config`, loại bỏ triệt để nguy cơ lộ lọt khóa bảo mật trong mã nguồn.

---

## 1. Tổng quan về AWS Systems Manager Parameter Store

AWS Systems Manager Parameter Store cung cấp kho lưu trữ cấu hình an toàn, có khả năng phân cấp và kiểm soát phiên bản:
* **Loại tham số SecureString**: Tự động mã hóa dữ liệu nhạy cảm bằng **AWS Key Management Service (KMS)** với khóa mặc định `alias/aws/ssm`.
* **Cấp độ Standard Tier**: Hoàn toàn miễn phí theo hạn ngạch AWS Free Tier, hỗ trợ lưu trữ tới 10,000 tham số với kích thước mỗi tham số lên đến 4 KB.
* **Tích hợp IAM chặt chẽ**: Chỉ các thực thể được cấp quyền (như EC2 gắn role `huylam-ssm-role` hoặc Lambda) mới có quyền đọc và giải mã tham số này.

---

## 2. Các bước tạo tham số trên AWS Management Console

### Bước 2.1: Truy cập Parameter Store
1. Đăng nhập vào AWS Console tại khu vực **ap-southeast-1 (Singapore)**.
2. Tìm kiếm dịch vụ: **Systems Manager -> Parameter Store -> Create parameter**.

---

### Bước 2.2: Cấu hình thông số tham số
Nhập các thông tin chi tiết sau:

| Thuộc tính (Attribute) | Giá trị cấu hình (Configured Value) | Ý nghĩa kỹ thuật |
| :--- | :--- | :--- |
| **Name** | `/huylam-ocr/config` | Đường dẫn phân cấp quản lý cấu hình dự án |
| **Description** | `Master Configuration for Huylam OCR Platform` | Mô tả mục đích sử dụng |
| **Tier** | Standard | Cấp độ tiêu chuẩn miễn phí |
| **Type** | **SecureString** | Tham số mã hóa bảo mật |
| **KMS Key source** | My current account | Sử dụng khóa KMS trong tài khoản hiện tại |
| **KMS Key ID** | `alias/aws/ssm` | Khóa quản lý mặc định của AWS KMS |
| **Data type** | text | Kiểu dữ liệu văn bản thuần (chuỗi JSON) |

---

### Bước 2.3: Thiết lập nội dung giá trị (Value)
Dán chuỗi cấu hình định dạng JSON chuẩn vào ô **Value**:

```json
{
  "ocr_mode": "AUTO",
  "scan_threshold_chars": 50,
  "kaggle_endpoint": "https://huylam-ocr.trycloudflare.com",
  "gemini_api_key": "your-gemini-api-key-here",
  "aws_native_mode_enabled": false
}
```

Nhấp nút **Create parameter**.

---

## 3. Kiểm tra tham số và Cơ chế đọc từ ứng dụng

Sau khi tạo thành công, tham số xuất hiện trong danh sách với ARN:
```text
arn:aws:ssm:ap-southeast-1:677994024390:parameter/huylam-ocr/config
```

![Chi tiết tham số bảo mật SecureString trong AWS Systems Manager Parameter Store](/images/week10/05-ssm-parameter-details.png)

### Cách thức ứng dụng Web Studio đọc cấu hình an toàn:
Trong tệp `src/backend/aws/storage_service.py`, ứng dụng sử dụng thư viện Boto3 để đọc và giải mã tham số tự động:

```python
import boto3
import json

def get_system_config():
    ssm = boto3.client('ssm', region_name='ap-southeast-1')
    response = ssm.get_parameter(
        Name='/huylam-ocr/config',
        WithDecryption=True
    )
    return json.loads(response['Parameter']['Value'])
```

**Checkpoint**: Máy chủ EC2 và hàm Lambda lấy được toàn bộ cấu hình và khóa API khi khởi động mà không cần lưu trữ bất kỳ tệp mật khẩu nào trên ổ đĩa.

---

## 4. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Tham số `/huylam-ocr/config` được tạo ở trạng thái **SecureString** mã hóa KMS.
- Cấu hình hệ thống được quản trị tập trung, cho phép thay đổi chế độ hoạt động (Fast-Path, Vision OCR, AWS Native) mà không cần triển khai lại mã nguồn.
- Tuân thủ 100% nguyên tắc bảo mật Zero Hardcoded Credentials.