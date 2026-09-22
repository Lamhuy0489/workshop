---
title: "Dịch vụ ứng dụng"
date: 2026-09-23
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Mục tiêu chuyên đề

Thiết lập và cấu hình các dịch vụ đám mây cốt lõi của AWS cho nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**, bao gồm cơ sở dữ liệu NoSQL Amazon DynamoDB, kho lưu trữ đối tượng Amazon S3 và trung tâm quản lý cấu hình bảo mật AWS Systems Manager Parameter Store.

---

## 1. Tổng quan các dịch vụ lưu trữ và dữ liệu

Hệ thống bóc tách và dịch thuật tài liệu sử dụng ba dịch vụ AWS Cloud Native để lưu trữ và quản lý dữ liệu an toàn, tuân thủ tiêu chuẩn FinOps 0.00 USD:

1. **Amazon DynamoDB (`document_processing_jobs`)**:
   * Cơ sở dữ liệu NoSQL Serverless lưu trữ trạng thái tiến trình xử lý, siêu dữ liệu tệp (kích thước, loại tệp, số trang), và độ trễ thực thi.
   * Hoạt động ở chế độ cước **On-Demand (`PAY_PER_REQUEST`)**, truy xuất dữ liệu với độ trễ mili-giây và hoàn toàn không phát sinh chi phí khi ở trạng thái nhàn rỗi.
2. **Amazon S3 (`huylam-ocr-documents-ap-southeast-1`)**:
   * Kho lưu trữ đối tượng bền vững 99.999999999% phục vụ lưu trữ tệp tài liệu gốc (`uploads/`) và các tệp kết quả bóc tách, dịch thuật (`outputs/`).
   * Cấu hình chính sách chia sẻ tài nguyên nguồn gốc chéo (CORS) cho phép Web Studio tải tệp lên an toàn.
3. **AWS Systems Manager Parameter Store (`/huylam-ocr/config`)**:
   * Quản lý tập trung toàn bộ cấu hình hệ thống và khóa API dưới dạng tham số bí mật `SecureString` được mã hóa bởi AWS Key Management Service (KMS), loại bỏ hoàn toàn việc mã hóa cứng khóa bí mật trong mã nguồn.

---

## 2. Nội dung các bước thực hành

Chuyên đề này gồm 3 phần thực hành chi tiết:

- **[5.5.1 Cấu hình cơ sở dữ liệu Amazon DynamoDB](5.5.1-configure-amazon-dynamodb/)**: Hướng dẫn tạo bảng `document_processing_jobs` với Partition Key `job_id` và Sort Key `created_at`.
- **[5.5.2 Cấu hình kho lưu trữ đối tượng Amazon S3](5.5.2-configure-amazon-s3/)**: Khởi tạo S3 bucket `huylam-ocr-documents-ap-southeast-1`, tạo thư mục `uploads/`, `outputs/` và cấu hình CORS.
- **[5.5.3 Quản trị tham số bảo mật với AWS SSM Parameter Store](5.5.3-configure-ssm-parameter-store/)**: Tạo tham số `/huylam-ocr/config` kiểu `SecureString` mã hóa KMS.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn sẽ có:
- Bảng Amazon DynamoDB `document_processing_jobs` sẵn sàng ghi nhận tiến trình.
- Kho lưu trữ Amazon S3 với cấu trúc thư mục chuẩn và chính sách CORS an toàn.
- Tham số `/huylam-ocr/config` lưu trữ tập trung cấu hình hệ thống trên AWS SSM Parameter Store.