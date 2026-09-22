---
title: "Workshop"
date: 2026-09-23
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Chuỗi Bài Thực Hành: Xây Dựng & Triển Khai Serverless Hybrid Document OCR, Parsing & Technical Translation Platform trên AWS

#### Tổng quan chuỗi bài thực hành
Trong chuỗi workshop thực hành này, chúng ta sẽ từng bước xây dựng, cấu hình và vận hành hoàn chỉnh nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** trên nền tảng điện toán đám mây AWS.

Hệ thống kết hợp giữa kiến trúc mạng doanh nghiệp 3 tầng (Three-Tier Enterprise Cloud Architecture) và kiến trúc phi máy chủ hướng sự kiện (Event-Driven Serverless):
* **Tầng Phân Phối Lưu Lượng (Ingress Layer)**: Cấu hình Application Load Balancer (ALB) Multi-AZ phân phối lưu lượng HTTP cổng 80 và cấp phát đường link công khai (Public DNS URL) phục vụ người dùng Internet.
* **Tầng Máy Chủ Ứng Dụng (Application Layer)**: Khởi chạy máy chủ ảo Amazon EC2 (Amazon Linux 2023, t2.micro) đặt sau chuỗi bảo mật Security Group Chaining, vận hành dịch vụ daemon systemd Gunicorn chạy ứng dụng Web Studio trên cổng nội bộ 5000, quản trị từ xa an toàn qua AWS Systems Manager Session Manager.
* **Tầng Lưu Trữ & Cơ Sở Dữ Liệu (Storage & Database Layer)**: Lưu trữ tài liệu gốc và tệp kết quả trên Amazon S3, quản lý nhật ký và trạng thái tiến trình bóc tách trên cơ sở dữ liệu NoSQL Amazon DynamoDB ở chế độ On-Demand.
* **Tầng Bảo Mật & Quản Trị Cấu Hình**: Lưu trữ tập trung các tham số và khóa API mã hóa KMS trong AWS Systems Manager Parameter Store (`SecureString`).
* **Tầng Tự Động Hóa Hướng Sự Kiện (Event-Driven Pipeline)**: Tự động kích hoạt luồng xử lý phi đồng bộ thời gian thực qua Amazon S3 Event Notification -> AWS Lambda -> Amazon DynamoDB -> Amazon CloudWatch Logs với chi phí 0.00 USD.

---

#### Nội dung chi tiết 12 chuyên đề thực hành

1. [Chuyên đề 5.1: Tổng quan Workshop & Kiến trúc giải pháp](5.1-Workshop-overview/)
2. [Chuyên đề 5.2: Điều kiện chuẩn bị môi trường](5.2-Prerequiste/)
3. [Chuyên đề 5.3: Cấu trúc dự án & Mã nguồn Web Studio](5.3-Project-foundation/)
4. [Chuyên đề 5.4: Thiết lập hạ tầng mạng VPC Multi-AZ & Bảo mật phân tầng](5.4-VPC/)
   * [5.4.1 Khởi tạo Amazon VPC Multi-AZ](5.4-VPC/5.4.1-create-vpc/)
   * [5.4.2 Cấu hình định tuyến & Chuỗi Security Groups](5.4-VPC/5.4.2-configure-network/)
5. [Chuyên đề 5.5: Cấu hình Dịch vụ Lưu trữ S3, DynamoDB & SSM](5.5-Application-Services/)
   * [5.5.1 Cấu hình cơ sở dữ liệu Amazon DynamoDB](5.5-Application-Services/5.5.1-configure-amazon-dynamodb/)
   * [5.5.2 Cấu hình kho lưu trữ đối tượng Amazon S3](5.5-Application-Services/5.5.2-configure-amazon-s3/)
   * [5.5.3 Quản trị tham số bảo mật với AWS SSM Parameter Store](5.5-Application-Services/5.5.3-configure-ssm-parameter-store/)
6. [Chuyên đề 5.6: Đóng gói Container với Docker & Amazon ECR](5.6-Containerization/)
   * [5.6.1 Xây dựng Dockerfile chuẩn OCI Container](5.6-Containerization/5.6.1-build-docker-image/)
   * [5.6.2 Đẩy Docker Image lên Amazon ECR](5.6-Containerization/5.6.2-push-image-to-ecr/)
7. [Chuyên đề 5.7: Triển khai Máy chủ Web Studio & Cân bằng tải ALB](5.7-Deploy-Application/)
   * [5.7.1 Cấu hình Target Group & Application Load Balancer](5.7-Deploy-Application/5.7.1-configure-load-balancer/)
   * [5.7.2 Triển khai EC2 Web Server qua SSM Session Manager](5.7-Deploy-Application/5.7.2-deploy-application-server/)
8. [Chuyên đề 5.8: Cấp phát URL công khai & Định tuyến lưu lượng Internet](5.8-Domain-and-HTTPS/)
   * [5.8.1 Kiểm tra phân giải DNS & Truy cập qua Public URL](5.8-Domain-and-HTTPS/5.8.1-configure-public-dns/)
9. [Chuyên đề 5.9: Tự động hóa hướng sự kiện Event-Driven Pipeline](5.9-CI-CD/)
   * [5.9.1 Thiết lập S3 Event Notification kích hoạt AWS Lambda](5.9-CI-CD/5.9.1-configure-event-driven-pipeline/)
10. [Chuyên đề 5.10: Giám sát hệ thống với Amazon CloudWatch](5.10-Monitoring/)
    * [5.10.1 Cấu hình CloudWatch Logs & Metrics](5.10-Monitoring/5.10.1-configure-cloudwatch/)
11. [Chuyên đề 5.11: Kiểm thử tích hợp toàn trình (End-to-End Testing)](5.11-Testing/)
12. [Chuyên đề 5.12: Quản trị chi phí FinOps & Giải phóng tài nguyên](5.12-Cleanup/)