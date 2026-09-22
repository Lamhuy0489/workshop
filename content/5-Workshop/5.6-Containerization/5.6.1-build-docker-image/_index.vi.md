---
title: "Build Docker Image"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

### Mục tiêu thực hành

Xây dựng tệp cấu hình `Dockerfile` tối ưu hóa đa tầng trên nền hình ảnh `python:3.11-slim`, biên dịch mã nguồn nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** thành Docker Image và kiểm thử khởi chạy container cục bộ trên cổng 5000.

---

## 1. Xây dựng Dockerfile cho Web Studio

Tại thư mục gốc của dự án, tệp `Dockerfile` được thiết kế nhằm tối ưu hóa bộ nhớ đệm (Build Cache) và giảm thiểu tối đa kích thước image:

```dockerfile
# Sử dụng base image Python 3.11 nhẹ gọn
FROM python:3.11-slim

# Thiết lập biến môi trường hệ thống
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PORT=5000 \
    PYTHONPATH=/app

# Thiết lập thư mục làm việc trong container
WORKDIR /app

# Cài đặt các gói hệ thống phục vụ xử lý ảnh và tài liệu PDF
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    libjpeg-dev \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Sao chép file requirements trước để tận dụng Docker cache
COPY requirements.txt /app/requirements.txt

# Cài đặt các thư viện Python
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r /app/requirements.txt

# Sao chép toàn bộ mã nguồn ứng dụng vào container
COPY src/ /app/src/

# Tạo thư mục chứa dữ liệu và database runtime
RUN mkdir -p /app/data

# Mở cổng kết nối của dịch vụ
EXPOSE 5000

# Lệnh khởi chạy máy chủ Web Studio
CMD ["python3", "src/frontend/server.py"]
```

---

## 2. Thiết lập tệp .dockerignore

Tạo tệp `.dockerignore` tại thư mục gốc để ngăn việc sao chép các tệp rác hoặc tệp nhạy cảm vào image:

```text
__pycache__
*.pyc
*.pyo
*.pyd
.Python
env/
venv/
.git
.gitignore
.env
.pytest_cache/
tests/
raw/
workshop/
*.md
```

---

## 3. Biên dịch Docker Image (docker build)

Mở Terminal tại thư mục gốc của dự án và chạy lệnh build:

```bash
docker build -t huylam-ocr-web-studio:latest .
```

Trong quá trình biên dịch:
1. Docker kéo base image `python:3.11-slim`.
2. Cài đặt các gói hệ thống biên dịch C-bindings cho `PyMuPDF` và `Pillow`.
3. Tải và cài đặt các thư viện phụ thuộc từ `requirements.txt`.
4. Sao chép cây mã nguồn `src/` và gắn thẻ image `huylam-ocr-web-studio:latest`.

Kiểm tra danh sách Docker Image đã tạo:

```bash
docker images | grep huylam-ocr-web-studio
```

---

## 4. Chạy kiểm thử Container cục bộ

Khởi chạy container từ image vừa tạo và chuyển tiếp cổng 5000:

```bash
docker run -d --name huylam-ocr-app -p 5000:5000 huylam-ocr-web-studio:latest
```

Kiểm tra trạng thái hoạt động của container:

```bash
docker ps
```

Mở trình duyệt web và truy cập:
```text
http://localhost:5000
```

Xác nhận trang đăng nhập Web Studio hiển thị hoàn hảo. Sau khi kiểm tra, dừng container thử nghiệm:

```bash
docker stop huylam-ocr-app && docker rm huylam-ocr-app
```

---

## 5. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Tệp `Dockerfile` và `.dockerignore` chuẩn hóa cho ứng dụng Python 3.11.
- Docker Image `huylam-ocr-web-studio:latest` được biên dịch thành công.
- Ứng dụng chạy thử nghiệm ổn định trong môi trường container cô lập.