---
title: "Chuẩn bị dự án"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Mục tiêu chuyên đề

Nắm vững kiến trúc mô-đun của mã nguồn nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**, cấu hình các biến môi trường kết nối dịch vụ AWS, chạy thử nghiệm Web Studio trên môi trường cục bộ và thực thi bộ kiểm thử tự động hóa.

---

## 1. Cấu trúc mô-đun mã nguồn dự án

Kho lưu trữ mã nguồn `Lamhuy0489/aws` được tổ chức theo chuẩn kiến trúc hướng dịch vụ, phân tách rõ ràng giữa tầng xử lý lõi backend và giao diện người dùng frontend:

```text
aws/
├── src/
│   ├── backend/
│   │   ├── parsers/               # Động cơ bóc tách lai đa tầng
│   │   │   ├── fast_parser.py     # Tầng 1: Fast-Path bằng PyMuPDF (0.1s - 0.3s/trang)
│   │   │   ├── ocr_dispatcher.py  # Tầng 2: OCR chọn lọc (Kaggle GPU / Gemini Failover)
│   │   │   └── hybrid_engine.py   # Bộ điều phối phân luồng bóc tách hợp nhất
│   │   ├── llm/                   # Động cơ dịch thuật và quản trị khóa
│   │   │   ├── translator.py      # Dịch thuật tài liệu kỹ thuật bảo toàn 100% Markdown
│   │   │   └── key_tour_manager.py# Quản trị xoay vòng API Keys (Round-Robin)
│   │   ├── exporters/             # Bộ xuất bản đa định dạng
│   │   │   ├── docx_exporter.py   # Xuất bản tệp Microsoft Word (.docx)
│   │   │   ├── pdf_exporter.py    # Xuất bản tệp PDF in ấn chuẩn A4
│   │   │   └── markdown_exporter.py # Xuất bản tệp Markdown (.md)
│   │   └── aws/                   # Tầng tích hợp AWS Cloud SDK Boto3
│   │       ├── storage_service.py # Giao tiếp Amazon S3, DynamoDB, SSM Parameter Store
│   │       └── lambda_s3_trigger.py # Mã nguồn xử lý sự kiện cho AWS Lambda
│   └── frontend/                  # Ứng dụng Web Studio đơn trang (SPA)
│       ├── server.py              # Máy chủ web Flask phục vụ API và Static assets
│       ├── static/                # Mã nguồn JavaScript, CSS giao diện
│       │   ├── js/studio.js       # Xử lý tương tác kéo thả tệp, hiển thị Split-view
│       │   ├── js/spa_router.js   # Điều hướng đơn trang không giật lag
│       │   └── js/i18n.js         # Từ điển đa ngôn ngữ (Tiếng Việt & Tiếng Anh)
│       └── templates/             # Giao diện HTML của Web Studio
├── tests/                         # Bộ kiểm thử tự động hóa toàn diện
├── Dockerfile                     # Tệp đóng gói container chuẩn OCI Container
├── requirements.txt               # Danh mục thư viện phụ thuộc Python
```

### Sơ đồ kiến trúc động cơ xử lý bóc tách & dịch thuật (Hybrid Processing Engine Architecture):

![Sơ đồ kiến trúc động cơ bóc tách lai và dịch thuật tài liệu kỹ thuật trên AWS](/images/architecture/aws-hybrid-ocr-engine-architecture.png?width=100%&classes=border,shadow)

> [!NOTE] Định dạng tệp sơ đồ Hybrid Engine
> * **Ảnh kết xuất độ nét cao**: `/images/architecture/aws-hybrid-ocr-engine-architecture.png` (Chuẩn Retina 1380x840)
> * **Sơ đồ đồ họa Vector SVG**: `/images/architecture/aws-hybrid-ocr-engine-architecture.svg`
> * **Tệp thiết kế nguồn Draw.io**: `/images/architecture/aws-hybrid-ocr-engine-architecture.drawio` (Hỗ trợ mở và chỉnh sửa trực tiếp trên [diagrams.net](https://app.diagrams.net/) với các stencil AWS4 chính thức).

---

## 2. Cấu hình biến môi trường (.env)

Tạo tệp `.env` trong thư mục gốc của dự án từ tệp mẫu `.env.example`:

```bash
cp .env.example .env
```

Cập nhật các tham số môi trường phù hợp với tài khoản AWS của bạn:

```ini
# Cấu hình cổng mạng máy chủ
PORT=5000
FLASK_ENV=production
FLASK_SECRET_KEY=huylam-super-secret-key-2026

# Cấu hình khu vực và kho lưu trữ AWS
AWS_DEFAULT_REGION=ap-southeast-1
AWS_S3_BUCKET_DOCUMENTS=huylam-ocr-documents-ap-southeast-1
DYNAMODB_JOBS_TABLE=document_processing_jobs
SSM_PARAMETER_CONFIG=/huylam-ocr/config

# Khóa API trí tuệ nhân tạo (Môi trường cục bộ)
GEMINI_API_KEY=your-gemini-api-key-here
KAGGLE_TUNNEL_ENDPOINT=https://your-kaggle-tunnel.trycloudflare.com
OCR_MODE=AUTO
```

> [!NOTE]
> Khi triển khai trên Amazon EC2 với IAM Instance Profile (`huylam-ssm-role`), ứng dụng sẽ tự động lấy thông tin xác thực IAM và tải cấu hình từ SSM Parameter Store mà không cần khai báo Access Key tĩnh trong tệp `.env`.

---

## 3. Khởi chạy ứng dụng Web Studio trên máy cục bộ

Kích hoạt môi trường ảo và khởi động máy chủ Flask:

```bash
# Kích hoạt môi trường ảo
source venv/bin/activate

# Khởi động ứng dụng bằng Python
python -m src.frontend.server
```

Hoặc khởi chạy thông qua máy chủ WSGI Gunicorn chuẩn môi trường triển khai thực tế:

```bash
gunicorn -w 2 -b 0.0.0.0:5000 src.frontend.server:app
```

Mở trình duyệt web và truy cập địa chỉ:
```text
http://localhost:5000
```

Hệ thống sẽ tự động chuyển hướng người dùng đến cổng đăng nhập Web Studio (`/login`).

---

## 4. Thực thi bộ kiểm thử tự động (Automated Testing)

Thực thi bộ kiểm thử tự động bằng `pytest` để xác nhận tất cả các mô-đun bóc tách, dịch thuật và tích hợp AWS đều hoạt động chính xác:

```bash
pytest -v tests/
```

**Checkpoint**: Toàn bộ các bài kiểm thử đơn vị (`tests/test_fast_parser.py`, `tests/test_translator.py`, `tests/test_docx_exporter.py`, `tests/test_aws_storage.py`) đều đạt kết quả **PASSED (100%)**.

---

## 5. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn đã:
- Hiểu rõ cấu trúc kiến trúc mã nguồn của nền tảng OCR & Dịch thuật.
- Thiết lập tệp cấu hình môi trường `.env` kết nối chuẩn xác với các tài nguyên AWS.
- Khởi chạy thành công Web Studio trên cổng 5000 bằng cả Flask và Gunicorn.
- Xác thực toàn bộ logic xử lý thông qua bộ kiểm thử tự động `pytest`.