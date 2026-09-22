---
title: "Cấu hình Amazon S3"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

### Mục tiêu thực hành

Khởi tạo và cấu hình kho lưu trữ đối tượng **Amazon S3 Bucket** mang tên `huylam-ocr-documents-ap-southeast-1` tại khu vực `ap-southeast-1`, thiết lập phân vùng thư mục `uploads/` và `outputs/`, áp dụng chính sách CORS bảo mật để phục vụ nền tảng Web Studio.

---

## 1. Khởi tạo Amazon S3 Bucket

Amazon Simple Storage Service (Amazon S3) cung cấp độ bền dữ liệu 99.999999999% (11 số 9) và khả năng mở rộng vô hạn:

### Các bước thực hiện trên AWS Console:
1. Đăng nhập vào AWS Console, truy cập dịch vụ: **Amazon S3 -> Buckets -> Create bucket**.
2. Cấu hình các thông số tạo bucket:

| Thuộc tính (Attribute) | Giá trị cấu hình (Configured Value) | Giải thích kỹ thuật |
| :--- | :--- | :--- |
| **Bucket name** | `huylam-ocr-documents-ap-southeast-1` | Tên định danh duy nhất toàn cầu cho bucket |
| **AWS Region** | `ap-southeast-1` (Singapore) | Khu vực đặt bucket đồng bộ với hạ tầng VPC |
| **Object Ownership** | ACLs disabled (recommended) | Sử dụng chính sách IAM để kiểm soát quyền truy cập |
| **Block Public Access** | **Block all public access = ON** | Khóa hoàn toàn truy cập công khai để bảo vệ dữ liệu riêng tư |
| **Bucket Versioning** | Disable | Tiết kiệm chi phí lưu trữ FinOps |
| **Default encryption** | Server-side encryption with Amazon S3 managed keys (SSE-S3) | Mã hóa dữ liệu tự động ở trạng thái nghỉ |

3. Nhấp **Create bucket**.

---

## 2. Tạo cấu trúc thư mục uploads/ và outputs/

1. Trong danh sách Buckets, nhấp vào `huylam-ocr-documents-ap-southeast-1`.
2. Tạo thư mục `uploads/`:
   * Nhấp **Create folder**, nhập tên: `uploads`.
   * Thư mục này tiếp nhận các tệp tài liệu gốc (PDF, ảnh scan PNG/JPG) do người dùng tải lên.
   * Nhấp **Create folder**.
3. Tạo thư mục `outputs/`:
   * Nhấp **Create folder**, nhập tên: `outputs`.
   * Thư mục này tự động lưu trữ các kết quả sau bóc tách và dịch thuật theo từng mã tiến trình (`outputs/<job_id>/filename.md`, `filename.docx`, `filename.pdf`).
   * Nhấp **Create folder**.

---

## 3. Cấu hình chính sách chia sẻ tài nguyên nguồn gốc chéo (CORS)

Để trình duyệt Web Studio có thể tải tài liệu trực tiếp lên S3 một cách an toàn thông qua kỹ thuật Presigned URL:

1. Chuyển sang thẻ **Permissions** của bucket.
2. Cuộn xuống mục **Cross-origin resource sharing (CORS)** và chọn **Edit**.
3. Dán đoạn mã cấu hình JSON sau:

```json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "PUT",
            "POST",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [
            "ETag"
        ],
        "MaxAgeSeconds": 3000
    }
]
```

4. Nhấp **Save changes**.

---

## 4. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Bucket `huylam-ocr-documents-ap-southeast-1` được tạo an toàn với **Block all public access = ON**.
- Hai thư mục chức năng `uploads/` và `outputs/` đã sẵn sàng hoạt động.
- Chính sách CORS được kích hoạt, cho phép giao diện Web Studio tải và nhận tệp trực tiếp từ Amazon S3.