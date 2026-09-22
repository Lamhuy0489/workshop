---
title: "Giám sát hệ thống"
date: 2026-09-23
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

### Mục tiêu

Thiết lập và vận hành hệ thống giám sát toàn diện cho nền tảng Hybrid OCR & Translation bằng Amazon CloudWatch, bao gồm giám sát trạng thái sức khỏe của Application Load Balancer và Target Group, các chỉ số tài nguyên của máy chủ EC2, và nhật ký thực thi phi máy chủ của hàm AWS Lambda.

---

## 1. Tổng quan kiến trúc giám sát

Trong một hệ thống lai ghép kết hợp giữa máy chủ ứng dụng (EC2) và các thành phần xử lý phi máy chủ (Serverless S3, Lambda, DynamoDB), việc giám sát tập trung là yêu cầu bắt buộc nhằm đảm bảo tính sẵn sàng cao, phát hiện sớm các điểm nghẽn hiệu năng và tối ưu hóa chi phí vận hành.

Amazon CloudWatch đóng vai trò trung tâm trong kiến trúc quan sát (Observability):
- **Target Group Metrics**: Giám sát chỉ số `HealthyHostCount`, `UnHealthyHostCount`, `TargetResponseTime` và lưu lượng HTTP thông qua Application Load Balancer.
- **Compute Instance Metrics**: Theo dõi `CPUUtilization`, lưu lượng mạng `NetworkIn`/`NetworkOut`, và trạng thái kiểm tra hệ thống `StatusCheckFailed` của máy chủ EC2.
- **Serverless Logging & Metrics**: Thu thập log tập trung thông qua CloudWatch Log Groups cho hàm Lambda `huylam-ocr-processor`, theo dõi số lượt kích hoạt (`Invocations`), thời gian thực thi (`Duration`) và lỗi (`Errors`).
- **CloudWatch Alarms**: Thiết lập ngưỡng cảnh báo tự động khi mức sử dụng CPU của máy chủ vượt quá 80% hoặc khi phát hiện lỗi hệ thống.

---

## 2. Nội dung thực hành

Thực hiện chuyên đề sau:

- **5.10.1 Cấu hình Amazon CloudWatch**: Khám phá nhật ký thực thi của Lambda, theo dõi sức khỏe Target Group, kiểm tra chỉ số hiệu năng EC2 và khởi tạo CloudWatch Alarm cảnh báo quá tải CPU.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ có:

- Nhóm nhật ký CloudWatch Log Group `/aws/lambda/huylam-ocr-processor` thu thập chi tiết mọi sự kiện kích hoạt từ S3.
- Cơ chế giám sát sức khỏe thời gian thực (Health Check) đảm bảo EC2 luôn ở trạng thái `Healthy` đằng sau ALB.
- Cảnh báo CloudWatch Alarm sẵn sàng thông báo khi tài nguyên máy chủ tiệm cận ngưỡng quá tải.
- Bức tranh toàn cảnh về hiệu năng và trạng thái hoạt động của toàn bộ hạ tầng AWS.