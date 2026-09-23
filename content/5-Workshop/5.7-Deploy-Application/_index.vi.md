---
title: "Triển khai ứng dụng"
date: 2026-09-23
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### Mục tiêu chuyên đề

Triển khai nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** lên hạ tầng điện toán đám mây AWS theo mô hình 3 tầng doanh nghiệp, bao gồm cân bằng tải Application Load Balancer (ALB) và máy chủ ảo Amazon EC2 quản trị an toàn qua AWS Systems Manager Session Manager.

---

## 1. Tổng quan kiến trúc triển khai

Quy trình triển khai máy chủ Web Studio được thiết kế nhằm tối ưu hóa tính sẵn sàng cao, bảo mật phân tầng và quản trị không cần mật khẩu SSH:

1. **Bộ cân bằng tải ứng dụng (Application Load Balancer - ALB)**:
   * Tiếp nhận lưu lượng HTTP trên cổng tiêu chuẩn 80 từ toàn bộ người dùng Internet.
   * Phân phối lưu lượng qua nhiều Vùng sẵn sàng (Multi-AZ) và tự động giám sát sức khỏe máy chủ ứng dụng qua đường dẫn `/login`.
2. **Nhóm mục tiêu (Target Group `huylam-ocr-tg`)**:
   * Định tuyến các yêu cầu từ ALB vào cổng nội bộ 5000 của máy chủ ứng dụng EC2.
3. **Máy chủ ứng dụng (Amazon EC2 `huylam-ocr-web-server`)**:
   * Khởi chạy phiên bản t2.micro trên nền Amazon Linux 2023, đặt bên trong `huylam-vpc`.
   * Gắn IAM Instance Profile `huylam-ssm-role` để tương tác trực tiếp với S3 và DynamoDB mà không cần lưu khóa API tĩnh.
   * Quản trị viên kết nối trực tiếp vào máy chủ thông qua **AWS Systems Manager Session Manager** mà không cần mở cổng SSH 22 ra Internet.
   * Vận hành máy chủ WSGI Gunicorn dưới dạng dịch vụ hệ thống nền **systemd service** (`huylam-ocr.service`), tự động phục hồi khi có lỗi và khởi chạy cùng hệ điều hành.

---

## 2. Nội dung các bước thực hành

Chuyên đề này gồm 3 phần thực hành chi tiết:

- **[5.7.1 Cấu hình Target Group & Application Load Balancer](5.7.1-configure-load-balancer/)**: Tạo Target Group trên cổng 5000, thiết lập Health Check và khởi tạo ALB Multi-AZ.
- **[5.7.2 Triển khai EC2 Web Server qua SSM Session Manager](5.7.2-deploy-application-server/)**: Tạo IAM Role, khởi chạy EC2, kết nối qua Session Manager, cài đặt môi trường Python 3.11 và kích hoạt dịch vụ systemd Gunicorn.
- **[5.7.3 Cấu hình Quản trị viên và Kết nối Kaggle GPU OCR](5.7.3-configure-admin-and-kaggle-ocr/)**: Khởi chạy máy chủ bóc tách Qwen2.5-VL trên Kaggle GPU, mở Cloudflare Tunnel và nhập Endpoint vào cụm khóa Web Studio.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn sẽ có:
- Application Load Balancer `huylam-ocr-alb` ở trạng thái **Active**.
- Target Group `huylam-ocr-tg` ghi nhận trạng thái **Healthy 1/1**.
- Máy chủ EC2 vận hành dịch vụ Web Studio ổn định trên cổng 5000.
- Hệ thống sẵn sàng phân phối lưu lượng truy cập từ Internet.