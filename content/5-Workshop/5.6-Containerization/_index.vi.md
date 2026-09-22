---
title: "Đóng gói ứng dụng"
date: 2026-09-23
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Mục tiêu chuyên đề

Đóng gói nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** thành OCI Container tiêu chuẩn bằng Docker trên nền tảng `python:3.11-slim`, tối ưu hóa kích thước image và xuất bản lên kho lưu trữ **Amazon Elastic Container Registry (Amazon ECR)**.

---

## 1. Tổng quan đóng gói Container

Việc đóng gói ứng dụng Web Studio thành Docker Container mang lại các ưu thế kỹ thuật vượt trội:
- **Tính nhất quán môi trường (Environment Parity)**: Đảm bảo toàn bộ các thư viện bóc tách C-extension (`PyMuPDF`), động cơ xuất bản Word (`python-docx`), máy chủ WSGI Gunicorn và các SDK đám mây hoạt động đồng nhất giữa máy trạm và máy chủ đám mây AWS.
- **Tối ưu hóa bảo mật và dung lượng**: Sử dụng hình ảnh cơ sở siêu nhẹ `python:3.11-slim`, thiết lập tài khoản người dùng không đặc quyền (`non-root user`) và cấu hình `.dockerignore` loại bỏ các tệp dữ liệu rác.
- **Lưu trữ bảo mật trên Amazon ECR**: Amazon Elastic Container Registry cung cấp khả năng quét lỗ hổng bảo mật tự động (Basic Scanning) và kiểm soát quyền truy cập chặt chẽ qua AWS IAM.

---

## 2. Nội dung các bước thực hành

Chuyên đề này gồm 2 phần thực hành chi tiết:

- **[5.6.1 Xây dựng Dockerfile chuẩn OCI Container](5.6.1-build-docker-image/)**: Viết `Dockerfile`, thiết lập `.dockerignore`, biên dịch và kiểm thử container cục bộ trên cổng 5000.
- **[5.6.2 Đẩy Docker Image lên Amazon ECR](5.6.2-push-image-to-ecr/)**: Khởi tạo repository `huylam-web-app`, đăng nhập Docker CLI qua AWS STS Token và đẩy container image lên Amazon ECR.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn sẽ có:
- Tệp `Dockerfile` và `.dockerignore` tối ưu cho ứng dụng Python 3.11.
- Docker Image `huylam-ocr-web-studio:latest` được xây dựng và chạy thử nghiệm thành công.
- Container Image được đẩy và lưu trữ an toàn trên Amazon ECR (`677994024390.dkr.ecr.ap-southeast-1.amazonaws.com/huylam-web-app:latest`).
- Sẵn sàng phục vụ triển khai trên máy chủ EC2 hoặc dịch vụ điều phối Amazon ECS Fargate.