---
title: "Đề xuất dự án"
date: 2026-09-18
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS

## Nền Tảng Bóc Tách, OCR Lai & Dịch Thuật Tài Liệu Kỹ Thuật Trên Đám Mây AWS

---

# 1. Tóm tắt dự án (Executive Summary)

**Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS** là nền tảng xử lý, số hóa và dịch thuật tài liệu kỹ thuật thông minh thế hệ mới, kết hợp giữa kỹ thuật trích xuất cấu trúc văn bản nhanh (Native Document Parsing), mô hình thị giác nhân tạo chọn lọc (Selective Vision OCR) và động cơ dịch thuật chuyên sâu bảo toàn định dạng Markdown. Hệ thống cho phép người dùng tự động bóc tách, nhận dạng các tệp tài liệu phức tạp (hợp đồng, hóa đơn, báo cáo tài chính dạng PDF hoặc ảnh scan đa ngôn ngữ) thành định dạng có cấu trúc chuẩn như Markdown (`.md`), Microsoft Word (`.docx`) và PDF có khả năng tìm kiếm mà vẫn giữ nguyên vẹn 100% bố cục, tiêu đề và bảng biểu số liệu.

Hệ thống được thiết kế theo kiến trúc phi máy chủ (Serverless) và hướng sự kiện (Event-Driven) trên AWS, hỗ trợ mô hình tính cước thông minh nhằm tối ưu hóa triệt để chi phí vận hành (0 USD trong giai đoạn thử nghiệm):
- **Cơ chế xử lý hai tầng (Two-Stage Hybrid Parsing)**:
  - *Tầng 1 (Fast-Path)*: Tự động bóc tách trực tiếp luồng văn bản số và cấu trúc bảng bằng thư viện native (`PyMuPDF`) với tốc độ 0.1 - 0.3 giây/trang cho các tài liệu PDF văn phòng thông thường mà không phát sinh chi phí tính toán AI.
  - *Tầng 2 (Selective Vision OCR)*: Tự động phân loại và chỉ gửi các trang là ảnh scan hoặc chứa biểu mẫu phức tạp sang mô hình thị giác AI để nhận dạng.
- **Động cơ Dịch thuật Kỹ thuật Đa ngôn ngữ (Technical Translation Engine)**:
  - Tự động phân trang và chuyển ngữ văn bản bóc tách sang nhiều ngôn ngữ (Tiếng Việt, Tiếng Anh, Tiếng Nhật, Tiếng Hàn, Tiếng Trung, Tiếng Pháp, Tiếng Đức).
  - Giữ nguyên vẹn các thành phần cú pháp Markdown: bảng biểu số liệu, đoạn mã nguồn, tiêu đề và liên kết.
- **Khả năng mở rộng đa chế độ (Multi-Mode Extensibility)**:
  - *Chế độ FinOps Tiết kiệm (Mặc định)*: Kết hợp tài nguyên GPU/TPU miễn phí trên Kaggle (chạy Qwen2.5-VL qua Cloudflare Tunnel) và cơ chế tự phục hồi (Failover) sang Google Gemini 3.6 Flash.
  - *Chế độ Đám mây Khép kín AWS Native (Tùy chọn mở rộng)*: Cho phép người dùng chủ động kích hoạt các mô hình nội bộ của AWS như **Amazon Bedrock** (Anthropic Claude 3.5 Haiku, Amazon Nova) hoặc **Amazon Textract** / **Amazon Translate** khi doanh nghiệp có yêu cầu nghiêm ngặt về bảo mật dữ liệu tuyệt đối (Zero Data Outflow). Chế độ này được cấu hình ở dạng tùy chọn phụ nhằm bảo vệ tối đa ngân sách thực tập FinOps của học viên.
- **Tầng Quản lý Cấu hình & Khóa bảo mật**: Quản lý tập trung qua **AWS Systems Manager (SSM) Parameter Store (SecureString)** được mã hóa bởi **AWS KMS**, giải quyết triệt để rủi ro lộ khóa API.
- **Tầng Lưu trữ & Dữ liệu**: Tệp gốc và tệp kết quả sau xử lý lưu trữ trên **Amazon S3**; Siêu dữ liệu tiến trình (Metadata) và thời gian thực thi lưu trên **Amazon DynamoDB**.
- **Tầng Giao tiếp & Phân phối**: Cung cấp API tiếp nhận qua **Amazon API Gateway** (hỗ trợ cấp S3 Presigned URL để tải tệp lớn an toàn) và giao diện web đơn trang (SPA) phân phối toàn cầu qua **Amazon S3 + Amazon CloudFront** có chứng chỉ SSL/TLS từ **AWS Certificate Manager (ACM)**.

---

# 2. Vấn đề và Giải pháp (Problem Statement & Solution)

## 2.1. Vấn đề thực tế
1. **Hạn chế của OCR truyền thống**: Các công cụ OCR thông thường chỉ bóc tách văn bản thô dạng phẳng (flat text), làm vỡ cấu trúc bảng biểu, xáo trộn thứ tự đọc của văn bản nhiều cột, và làm mất hoàn toàn các định dạng phân cấp tiêu đề.
2. **Chi phí và thời gian xử lý khi lạm dụng mô hình Vision AI**: Việc nạp toàn bộ tài liệu 50 - 100 trang vào các mô hình AI thị giác nặng gây nghẽn cổ chai, thời gian chờ đợi kéo dài và phát sinh chi phí tính toán GPU rất lớn, trong khi đa số trang tài liệu văn phòng đã có sẵn văn bản số.
3. **Thách thức dịch thuật tài liệu kỹ thuật**: Các công cụ dịch tự động thông thường làm vỡ cú pháp bảng biểu số liệu, thay đổi cấu trúc định dạng tài liệu và làm mất ngữ cảnh kỹ thuật chuyên ngành.
4. **Yêu cầu bảo mật dữ liệu nội bộ**: Một số tổ chức yêu cầu toàn bộ luồng xử lý AI phải diễn ra khép kín trong hạ tầng AWS mà không được truyền dữ liệu ra ngoài Internet.

## 2.2. Giải pháp đề xuất
Dự án xây dựng nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** giải quyết trọn vẹn các thách thức trên:
- **Tối ưu hóa tốc độ và chi phí bằng cơ chế Hybrid**: 80-90% các trang PDF số được bóc tách tức thì ở Tầng 1 với chi phí 0 đồng; chỉ 10-20% các trang scan mới kích hoạt Tầng 2.
- **Bảo toàn 100% định dạng và bảng biểu**: Tái lập chính xác bảng biểu thành Markdown Table (`| Cột 1 | Cột 2 |`), phân định rõ ràng các cấp tiêu đề và đoạn văn, hỗ trợ xuất sang định dạng `.md`, `.docx` và `.pdf` chuẩn A4.
- **Dịch thuật giữ nguyên cấu trúc**: Ứng dụng LLM với prompt chuyên biệt giúp dịch thuật chính xác thuật ngữ kỹ thuật mà không làm biến dạng cấu trúc văn bản.
- **Hỗ trợ tùy chọn AWS Native khép kín**: Tích hợp sẵn adapter kết nối Amazon Bedrock / Amazon Textract / Amazon Translate khi người dùng lựa chọn, tạo nên một hệ sinh thái đám mây khép kín hoàn chỉnh.

---

# 3. Sơ đồ kiến trúc giải pháp (Architecture Diagram)

```text
[ Người Dùng / Trình Duyệt Web Studio ]
              │
              ▼ HTTPS (SSL/TLS)
[ Amazon CloudFront + Amazon S3 Static Hosting ] (Giao diện SPA Studio & Preview)
              │
              ▼ REST API Request (Xin Presigned URL hoặc Tra Cứu Trạng Thái)
[ Amazon API Gateway ]
              │
              ├──> 1. Trả về S3 Presigned PUT URL an toàn
              │
[ Amazon S3 Bucket ]
      │
      ├── /uploads/ (File PDF / Ảnh gốc tải lên trực tiếp)
      │      │
      │      ▼ (Sự kiện s3:ObjectCreated tự động kích hoạt)
      ▼
[ AWS Lambda / Compute Engine ]
      │
      ├── 2. Đọc cấu hình & API Keys ──> [ AWS SSM Parameter Store (SecureString) ]
      │
      ├── 3. Tầng 1: Bóc tách cấu trúc nhanh (Fast-Path bằng PyMuPDF, 0.1s - 0.3s)
      │
      ├── 4. Tầng 2: OCR chọn lọc (Chỉ xử lý trang scan):
      │      ├── [Mặc định 1: Kaggle GPU/TPU qua Cloudflare Tunnel]
      │      ├── [Mặc định 2: Google Gemini 3.6 Flash Failover]
      │      └── [Tùy chọn phụ AWS Native: Amazon Bedrock / Amazon Textract]
      │
      ├── 5. Động cơ Dịch thuật Kỹ thuật:
      │      ├── [Mặc định: Gemini Flash Translator]
      │      └── [Tùy chọn phụ AWS Native: Amazon Bedrock / Amazon Translate]
      │
      ├── 6. Xuất bản đa định dạng ────> [ Amazon S3: /outputs/ ] (.md, .docx, .pdf)
      │
      └── 7. Ghi nhận siêu dữ liệu ─────> [ Amazon DynamoDB (document_processing_jobs) ]
                                          [ Amazon CloudWatch (Logs & Metrics) ]
```

---

# 4. Danh mục dịch vụ AWS sử dụng

| Dịch vụ AWS | Vai trò trong hệ thống | Lý do lựa chọn |
| :--- | :--- | :--- |
| **Amazon S3** | Lưu trữ tài liệu gốc (`/uploads/`), tệp kết quả (`/outputs/`) và Web tĩnh | Độ bền 99.999999999%, tích hợp Presigned URL và Event Notification |
| **Amazon DynamoDB** | Lưu trữ trạng thái xử lý, siêu dữ liệu tài liệu và độ trễ thực thi | Tốc độ mili-giây, NoSQL linh hoạt, chế độ On-Demand chi phí 0 USD khi nhàn rỗi |
| **AWS Systems Manager** | Quản lý cấu hình chế độ và khóa API an toàn (Parameter Store) | Lưu trữ tham số mã hóa KMS, cho phép đổi cấu hình mà không cần sửa mã nguồn |
| **AWS Lambda** | Trung tâm điều phối bóc tách và tạo Presigned URL (Compute) | Phi máy chủ, tự động kích hoạt theo sự kiện S3, nằm trong hạn ngạch Free Tier |
| **Amazon API Gateway** | Tiếp nhận yêu cầu tải tệp và truy vấn kết quả | Cung cấp REST API an toàn, hỗ trợ CORS và sinh URL có chữ ký SigV4 |
| **Amazon CloudFront** | Mạng phân phối nội dung (CDN) toàn cầu | Tăng tốc độ tải trang giao diện Web Studio, chứng chỉ HTTPS miễn phí từ ACM |
| **Amazon CloudWatch** | Giám sát, ghi nhật ký và đo lường hiệu năng | Theo dõi thời gian thực thi Fast-Path và thời gian gọi OCR / Dịch thuật |
| **Amazon Bedrock / Textract** | Tùy chọn mở rộng: Xử lý AI nội bộ khép kín trên AWS (Optional) | Cung cấp mô hình nền tảng cấp doanh nghiệp khi người dùng chủ động kích hoạt |

---

# 5. Kế hoạch triển khai 12 tuần (Implementation Roadmap)

- **Tuần 1 - 4**: Nền tảng hạ tầng đám mây AWS (IAM, VPC, EC2, S3, Systems Manager).
- **Tuần 5 - 8**: Giám sát, cân bằng tải tự động, hạ tầng dạng mã (CloudFormation) và điều phối Container (ECR, ECS Fargate).
- **Tuần 9**: Thiết kế kiến trúc giải pháp Serverless Microservices và phân tầng xử lý tài liệu.
- **Tuần 10**: Triển khai hạ tầng lưu trữ S3, cơ sở dữ liệu DynamoDB và quản trị cấu hình SSM Parameter Store trên AWS.
- **Tuần 11**: Tích hợp luồng bóc tách OCR, dịch thuật kỹ thuật, xuất bản đa định dạng và đo kiểm hiệu năng thực tế.
- **Tuần 12**: Báo cáo kiểm toán chi phí FinOps (0 USD), quay video demo hoàn chỉnh và bảo vệ đồ án tốt nghiệp.