---
title: "Tự đánh giá"
date: 2026-09-23
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

### Thông tin thực tập sinh

- **Họ và tên**: Lâm Quang Huy
- **Mã số sinh viên**: 0212267
- **Lớp chuyên ngành**: 67CS (Khoa Công nghệ thông tin)
- **Đơn vị đào tạo**: Trường Đại học Xây dựng Hà Nội (HUCE)
- **Chương trình thực tập**: AWS First Cloud AI Journey (FCAJ) Bootcamp 2026
- **Đề tài dự án tốt nghiệp**: Nền tảng bóc tách, nhận dạng ký tự quang học lai và dịch thuật tài liệu kỹ thuật trên đám mây AWS (Serverless Hybrid Document OCR, Parsing & Technical Translation Platform)

---

## 1. Tổng kết hành trình thực tập tại AWS FCAJ 2026

Trong suốt 12 tuần thực tập chuyên sâu tại chương trình **AWS First Cloud AI Journey (FCAJ)**, tôi đã hoàn thành lộ trình đào tạo toàn diện từ các dịch vụ hạ tầng đám mây cốt lõi đến các giải pháp trí tuệ nhân tạo tạo sinh tiên tiến nhất của AWS:
- **Tuần 1 đến Tuần 4**: Thiết lập tài nguyên định danh AWS IAM, quản trị máy chủ Amazon EC2 (Amazon Linux 2023), phân bổ không gian mạng ảo cô lập Amazon VPC, cấu hình định tuyến Internet Gateway và phân tách mạng con Multi-AZ.
- **Tuần 5 đến Tuần 8**: Cấu hình lưu trữ tệp Amazon S3, cơ sở dữ liệu phi quan hệ Amazon DynamoDB On-Demand, quản trị máy chủ phi máy chủ qua AWS Systems Manager (Fleet & Session Manager theo chuẩn Zero Trust), đóng gói OCI Container bằng Docker và xuất bản lên Amazon ECR.
- **Tuần 9 đến Tuần 12**: Thiết kế kiến trúc tổng thể dự án tốt nghiệp, phát triển ứng dụng Web Studio (Flask SPA), triển khai máy chủ cân bằng tải Application Load Balancer với chuỗi bảo mật Security Group Chaining, tự động hóa quy trình phi máy chủ Event-Driven S3 -> Lambda -> DynamoDB và tích hợp cụm GPU Kaggle xử lý OCR 0.00 USD song song với tùy chọn AWS Native AI (Amazon Bedrock & Amazon Textract).

---

## 2. Bảng tự đánh giá năng lực theo chuẩn đầu ra

Dưới đây là bảng tự đánh giá chi tiết mức độ hoàn thành nhiệm vụ và năng lực chuyên môn sau 12 tuần thực tập:

| STT | Tiêu chí đánh giá chuyên môn | Xuất sắc | Tốt | Đạt | Ghi chú minh chứng thực tế |
| :---: | :--- | :---: | :---: | :---: | :--- |
| 1 | Năng lực thiết kế kiến trúc đám mây AWS | [x] | [ ] | [ ] | Hoàn thành sơ đồ kiến trúc Three-Tier Multi-AZ kết hợp Serverless Event-Driven chuẩn Well-Architected. |
| 2 | Kỹ năng quản trị hạ tầng mạng (VPC / Subnet / SG) | [x] | [ ] | [ ] | Triển khai VPC `10.0.0.0/16`, 2 Public Subnet Multi-AZ, Internet Gateway và Chained Security Groups cô lập EC2 cổng 5000. |
| 3 | Quản trị máy chủ và bảo mật Zero Trust (SSM) | [x] | [ ] | [ ] | Loại bỏ hoàn toàn cổng SSH 22 và Bastion Host; kết nối máy chủ an toàn qua Session Manager và IAM Role `huylam-ssm-role`. |
| 4 | Kỹ thuật phát triển và đóng gói ứng dụng (Docker/ECR) | [x] | [ ] | [ ] | Xây dựng Dockerfile OCI Container `python:3.11-slim`, tối ưu dung lượng và đẩy lên Amazon ECR repository `huylam-web-app`. |
| 5 | Tự động hóa hướng sự kiện (Event-Driven Serverless) | [x] | [ ] | [ ] | Kết nối S3 Event `s3:ObjectCreated:*` trực tiếp tới AWS Lambda `huylam-ocr-processor` với độ trễ phản hồi tức thì 214 ms. |
| 6 | Quản trị chi phí và FinOps (Cost Optimization) | [x] | [ ] | [ ] | Vận hành toàn bộ hệ thống với chi phí 0.00 USD nằm trọn vẹn trong AWS Free Tier; tối ưu chi phí OCR qua cụm GPU Kaggle. |
| 7 | Viết tài liệu kỹ thuật và xây dựng Workshop | [x] | [ ] | [ ] | Biên soạn trọn bộ tài liệu Hugo song ngữ 12 chuyên đề chi tiết, tích hợp ảnh chụp màn hình có gắn khung viền đỏ chuẩn xác. |
| 8 | Khả năng tự nghiên cứu và giải quyết bài toán kỹ thuật | [x] | [ ] | [ ] | Tự thiết kế thuật toán bóc tách lai Fast-Path PyMuPDF (0.1s/trang) và cơ chế xoay vòng API Keys tự động. |
| 9 | Tinh thần kỷ luật và tiến độ công việc | [x] | [ ] | [ ] | Cập nhật đầy đủ 12 bài báo cáo Worklog tuần, 3 bài blog chuyên sâu và hoàn thành xuất sắc các mốc đánh giá của mentor. |
| 10 | Tác phong kỹ thuật chuyên nghiệp | [x] | [ ] | [ ] | Tuân thủ các nguyên tắc thiết kế hạ tầng doanh nghiệp, quản lý mã nguồn trên Git và tự động hóa triển khai CI/CD. |

---

## 3. Đánh giá theo 5 trụ cột AWS Well-Architected Framework

### 3.1. Vận hành xuất sắc (Operational Excellence)
- **Tự động hóa triển khai**: Toàn bộ trang tài liệu báo cáo thực tập được quản lý mã nguồn phiên bản trên GitHub (`Lamhuy0489/workshop`) và tự động hóa biên dịch, xuất bản thông qua GitHub Actions lên GitHub Pages.
- **Giám sát hệ thống tập trung**: Khởi tạo Log Group `/aws/lambda/huylam-ocr-processor`, thu thập số liệu Target Group HealthyHostCount và thiết lập CloudWatch Alarms để cảnh báo sự cố ngay lập tức.
- **Quản lý quy trình bằng mã**: Ứng dụng chạy dưới dạng systemd daemon `huylam-ocr.service`, có kịch bản triển khai tự động `deploy.sh` hỗ trợ khởi động lại và cập nhật mã nguồn không gián đoạn.

### 3.2. An toàn và bảo mật (Security)
- **Nguyên tắc đặc quyền tối thiểu (Least Privilege)**: Phân tách rõ ràng giữa vai trò máy chủ `huylam-ssm-role` và vai trò thực thi hàm Lambda `huylam-ocr-lambda-role`. Không sử dụng Access Keys tĩnh trên máy chủ; toàn bộ quyền giao tiếp đều thông qua AWS STS Temporary Credentials.
- **Chuỗi tường lửa phân tầng (Security Group Chaining)**: Máy chủ EC2 chỉ mở cổng TCP 5000 cho duy nhất định danh Security Group của ALB (`huylam-alb-sg`), ngăn chặn triệt để nguy cơ quét cổng hoặc tấn công trực diện từ mạng công cộng.
- **Mã hóa dữ liệu lưu trữ**: Kích hoạt mã hóa Server-Side Encryption (SSE-S3 AES-256) trên toàn bộ tài liệu lưu trữ tại Amazon S3. Dữ liệu cấu hình nhạy cảm được lưu dưới dạng SecureString trên AWS Systems Manager Parameter Store.

### 3.3. Độ tin cậy (Reliability)
- **Kiến trúc mạng đa vùng sẵn sàng (Multi-AZ)**: Application Load Balancer được cấu hình trải rộng qua 2 Vùng sẵn sàng độc lập (`ap-southeast-1a` và `ap-southeast-1b`), đảm bảo khả năng chuyển mạch dự phòng tức thì khi một trung tâm dữ liệu gặp sự cố.
- **Cơ chế phục hồi lỗi tự động (Failover)**: Tầng OCR hỗ trợ chuyển hướng thông minh từ cụm GPU Kaggle sang Google Gemini Flash khi kết nối mạng gián đoạn, đảm bảo tiến trình bóc tách văn bản của người dùng không bao giờ bị đình trệ.

### 3.4. Hiệu năng vượt trội (Performance Efficiency)
- **Động cơ phân luồng thông minh (Fast-Path PyMuPDF)**: Xử lý hơn 80% tài liệu văn phòng dạng PDF số hóa bằng thuật toán trích xuất vector chỉ mất từ 0.1s đến 0.3s mỗi trang, nhanh gấp hàng chục lần so với việc đưa toàn bộ văn bản qua các mô hình thị giác nhân tạo nặng nề.
- **Tự động co giãn theo nhu cầu (Serverless Scale-to-Zero)**: Tận dụng cơ chế phi máy chủ của AWS Lambda và Amazon DynamoDB On-Demand để tiếp nhận sự kiện tải lên với độ trễ dưới 1 giây mà không tiêu hao tài nguyên máy chủ khi không có tác vụ.

### 3.5. Tối ưu hóa chi phí (Cost Optimization / FinOps)
- **Giữ vững ngân sách 0.00 USD**: Toàn bộ hạ tầng lưu trữ (S3), cơ sở dữ liệu (DynamoDB), hàm phi máy chủ (Lambda), quản trị máy chủ (SSM) và máy chủ kiểm thử (EC2 t2.micro) đều được kiểm soát nghiêm ngặt trong phạm vi AWS Free Tier.
- **Tận dụng tài nguyên GPU miễn phí**: Tích hợp máy chủ tính toán GPU 2x NVIDIA T4 (32GB VRAM) của Kaggle qua đường hầm Cloudflare Tunnel, mang lại khả năng thị giác máy tính cực mạnh cho mô hình Qwen2.5-VL mà không phát sinh chi phí thuê máy chủ EC2 GPU đắt đỏ (p3/g4dn).

---

## 4. Định hướng phát triển và kế hoạch tương lai

- **Chứng chỉ chuyên môn**: Hoàn thành kỳ thi và đạt chứng chỉ **AWS Certified Solutions Architect – Associate (SAA-C03)** trong quý tới, tiếp tục hướng tới chứng chỉ **AWS Certified Machine Learning – Specialty (MLS-C01)**.
- **Hạ tầng dưới dạng mã (IaC)**: Chuyển đổi toàn bộ quy trình thiết lập tài nguyên thủ công sang các mẫu tự động hóa Declarative Template bằng **AWS CloudFormation** và **Terraform**.
- **Container Orchestration**: Di chuyển máy chủ Web Studio từ máy chủ EC2 đơn lẻ sang dịch vụ điều phối container phi máy chủ **Amazon Elastic Container Service (Amazon ECS on AWS Fargate)** để tối ưu khả năng mở rộng quy mô tự động.
- **Đóng góp cộng đồng**: Tiếp tục tham gia tích cực các hoạt động của cộng đồng AWS User Group Vietnam, chia sẻ các bài viết kỹ thuật chuyên sâu về Serverless, FinOps và tích hợp AI trên nền tảng đám mây.

---

## 5. Lời kết

Chương trình thực tập **AWS First Cloud AI Journey 2026** là bước đệm then chốt giúp tôi hoàn thiện tư duy kiến trúc hệ thống, rèn luyện kỹ năng kỹ thuật thực chiến và định hình rõ nét lộ trình sự nghiệp trở thành một Kỹ sư Kiến trúc Đám mây (Cloud Solutions Architect) chuyên nghiệp. Tôi xin gửi lời cảm ơn chân thành tới ban cố vấn (mentors), các anh chị kỹ sư AWS và trường Đại học Xây dựng Hà Nội đã tạo điều kiện và hướng dẫn tận tình trong suốt thời gian qua.