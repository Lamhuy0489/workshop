---
title: "Tổng quan Workshop"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### Mục tiêu chuyên đề

Chuyên đề này cung cấp cái nhìn toàn diện về bài toán thực tế, giải pháp kiến trúc và quy trình vận hành của nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** trên nền tảng AWS. Sau khi hoàn thành, bạn sẽ nắm vững nguyên lý hoạt động của kiến trúc mạng 3 tầng kết hợp với xử lý phi máy chủ hướng sự kiện, sẵn sàng bắt tay vào các bước cấu hình thực tế.

> [!TIP] Thông tin kho mã nguồn và hệ thống Live
> * **Kho mã nguồn ứng dụng (GitHub Repo)**: [https://github.com/Lamhuy0489/aws](https://github.com/Lamhuy0489/aws)
> * **Kho tài liệu hướng dẫn (GitHub Docs Repo)**: [https://github.com/Lamhuy0489/workshop](https://github.com/Lamhuy0489/workshop)
> * **Hệ thống Web Studio Live (AWS ALB Endpoint)**: [http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com](http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com)

---

## 1. Giới thiệu bài toán và giải pháp kiến trúc

### 1.1. Thách thức trong xử lý tài liệu kỹ thuật doanh nghiệp
Trong môi trường doanh nghiệp và nghiên cứu, việc số hóa và xử lý các tệp tài liệu kỹ thuật (bản vẽ thiết kế, hồ sơ thầu, báo cáo tài chính, bài báo khoa học) gặp phải các rào cản lớn:
1. **Làm vỡ cấu trúc và định dạng**: Các công cụ OCR thông thường chỉ trích xuất chuỗi văn bản thô, làm mất hoàn toàn ma trận bảng biểu số liệu, tiêu đề phân cấp và thứ tự đọc của văn bản nhiều cột.
2. **Chi phí và thời gian xử lý khi lạm dụng AI thị giác**: Việc nạp toàn bộ tài liệu nhiều trang vào các mô hình Vision AI gây nghẽn băng thông, thời gian chờ đợi hàng phút và tiêu tốn chi phí token rất lớn, trong khi hơn 80% tài liệu văn phòng đã có sẵn luồng văn bản số.
3. **Mất ngữ cảnh kỹ thuật khi dịch thuật**: Các công cụ dịch văn bản thông thường không hiểu cấu trúc Markdown, làm hỏng bảng biểu và sai lệch thuật ngữ chuyên ngành.
4. **Yêu cầu bảo mật đám mây khép kín**: Nguy cơ rò rỉ dữ liệu khi mở cổng SSH công khai hoặc lưu trữ khóa bảo mật cứng trong mã nguồn.

### 1.2. Giải pháp kiến trúc của đề tài
Hệ thống **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** giải quyết triệt để các thách thức trên thông qua:
- **Cơ chế bóc tách lai hai tầng (Two-Stage Hybrid Parsing)**:
  - *Tầng 1 (Fast-Path)*: Tự động trích xuất trực tiếp văn bản số và cấu trúc bảng bằng thư viện `PyMuPDF` trong thời gian chỉ **0.1s - 0.3s/trang** với chi phí 0 USD.
  - *Tầng 2 (Selective Vision OCR)*: Tự động phát hiện các trang là ảnh quét scan hoặc biểu mẫu phức tạp để gửi sang mô hình thị giác AI (Kaggle GPU Qwen2.5-VL / Gemini Flash / AWS Bedrock Nova).
- **Động cơ Dịch thuật Kỹ thuật Đa ngôn ngữ**: Chuyển ngữ chính xác sang Tiếng Việt và các ngôn ngữ quốc tế mà vẫn giữ nguyên vẹn 100% cú pháp Markdown và bảng số liệu.
- **Động cơ Xuất bản Đa định dạng**: Kết xuất tự động ra tệp Markdown (`.md`), Microsoft Word (`.docx`) chuẩn in ấn và PDF.
- **Kiến trúc đám mây 3 tầng bảo mật cao trên AWS**: Phân tầng ranh giới mạng bằng chuỗi Security Groups (Chaining), kết hợp với quy trình phi máy chủ hướng sự kiện (Event-Driven) tự động hóa hoàn toàn.

---

## 2. Kiến trúc giải pháp tổng thể trên AWS

Hệ thống được thiết kế theo các tiêu chuẩn cao nhất của AWS Well-Architected Framework:

![Sơ đồ kiến trúc tổng thể AWS Serverless Hybrid OCR Platform](/images/architecture/aws-system-architecture.png?width=100%&classes=border,shadow)

> [!NOTE] Định dạng tệp sơ đồ kiến trúc
> * **Ảnh kết xuất độ nét cao**: `/images/architecture/aws-system-architecture.png` (Chuẩn Retina 1400x920)
> * **Sơ đồ đồ họa Vector SVG**: `/images/architecture/aws-system-architecture.svg`
> * **Tệp thiết kế nguồn Draw.io**: `/images/architecture/aws-system-architecture.drawio` (Có thể nhập trực tiếp vào [diagrams.net](https://app.diagrams.net/) để tùy biến kéo thả theo các stencil biểu tượng AWS chính thức).

```text
                                [ Internet Client / Trình Duyệt ]
                                                │
                                                ▼ HTTP : 80
                    ┌───────────────────────────────────────────────────────┐
                    │      AWS Application Load Balancer (Multi-AZ)         │
                    │        huylam-ocr-alb (Public DNS Endpoint)           │
                    │            Security Group: huylam-alb-sg              │
                    └───────────────────────────┬───────────────────────────┘
                                                │
                        Forward to Target Group │ Port 5000
                        Health Check: /login    │ (HTTP 200 OK)
                                                ▼
                    ┌───────────────────────────────────────────────────────┐
                    │          Amazon EC2 Application Host (AL2023)         │
                    │        huylam-ocr-web-server (t2.micro / 10.0.8.15)   │
                    │            Security Group: huylam-web-sg              │
                    │      (Chỉ cho phép TCP 5000 từ huylam-alb-sg)         │
                    │                                                       │
                    │   ┌───────────────────────────────────────────────┐   │
                    │   │        Gunicorn WSGI (huylam-ocr.service)     │   │
                    │   │          Flask Web Studio SPA (Port 5000)     │   │
                    │   └───────────────────────┬───────────────────────┘   │
                    │                           │                           │
                    │       ┌───────────────────┴───────────────────┐       │
                    │       ▼                                       ▼       │
                    │   [ Tầng 1: Fast-Path ]               [ Tầng 2: OCR ] │
                    │    PyMuPDF (0.1s - 0.3s)               Kaggle GPU     │
                    │    Chi phí: 0.00 USD                   Gemini Flash   │
                    │                                        AWS Bedrock    │
                    │       │                                       │       │
                    │       └───────────────────┬───────────────────┘       │
                    │                           ▼                           │
                    │             [ Động cơ Dịch thuật Kỹ thuật ]           │
                    │             Bảo toàn 100% Markdown syntax             │
                    │                           │                           │
                    │             [ Bộ Xuất bản Đa định dạng ]              │
                    │             Markdown (.md), Word (.docx), PDF         │
                    └───────────────┬───────────────────────┬───────────────┘
                                    │ IAM Role              │ IAM Role
                                    │ huylam-ssm-role       │ huylam-ssm-role
                                    ▼                       ▼
                    ┌─────────────────────────────┐  ┌──────────────────────┐
                    │     Amazon S3 Storage       │  │   Amazon DynamoDB    │
                    │     huylam-ocr-documents-   │  │   document_          │
                    │     ap-southeast-1          │  │   processing_jobs    │
                    │     - uploads/ (Tệp gốc)    │  │   (PK: job_id,       │
                    │     - outputs/ (Kết quả)    │  │    SK: created_at)   │
                    └───────────────┬─────────────┘  └──────────▲───────────┘
                                    │                           │
                                    │ Event: s3:ObjectCreated:* │ PutItem
                                    ▼                           │ (214 ms)
                    ┌─────────────────────────────┐             │
                    │    AWS Lambda Function      │─────────────┘
                    │    huylam-ocr-processor     │
                    │    (Python 3.11 Serverless) │
                    └───────────────┬─────────────┘
                                    │
                                    ▼ Logs & Metrics
                    ┌─────────────────────────────┐
                    │    Amazon CloudWatch Logs   │
                    │    /aws/lambda/huylam-ocr-  │
                    │    processor                │
                    └─────────────────────────────┘
```

---

## 3. Quy trình vận hành hệ thống (End-to-End Workflow)

1. **Truy cập người dùng**: Người dùng từ Internet truy cập qua Public DNS URL của Application Load Balancer `huylam-ocr-alb`.
2. **Cân bằng tải & Điều hướng**: ALB phân phối yêu cầu vào Target Group `huylam-ocr-tg`, chuyển tiếp lưu lượng vào cổng nội bộ 5000 của máy chủ EC2 `huylam-ocr-web-server`.
3. **Bảo mật phân tầng**: Tường lửa cấp hạt nhân của AWS (`huylam-web-sg`) chỉ mở cổng 5000 cho traffic từ ALB, loại bỏ 100% rủi ro truy cập trái phép trực tiếp vào máy chủ ứng dụng.
4. **Khởi chạy ứng dụng Web Studio**: Dịch vụ daemon systemd `huylam-ocr.service` chạy máy chủ WSGI Gunicorn phục vụ giao diện Web Studio đơn trang (SPA).
5. **Tiếp nhận & Lưu trữ tài liệu**: Khi người dùng tải tài liệu lên, ứng dụng sử dụng quyền IAM Instance Profile (`huylam-ssm-role`) đẩy tệp vào thư mục `uploads/` của Amazon S3 `huylam-ocr-documents-ap-southeast-1`.
6. **Kích hoạt tự động phi máy chủ (Event-Driven)**: Sự kiện `s3:ObjectCreated:*` kích hoạt AWS Lambda `huylam-ocr-processor` tự động tạo bản ghi tiến trình trong Amazon DynamoDB `document_processing_jobs` và ghi nhật ký lên CloudWatch Logs.
7. **Bóc tách, Dịch thuật & Xuất bản**: Ứng dụng thực hiện bóc tách Tầng 1 Fast-Path siêu tốc (0.1s - 0.3s/trang), gọi OCR chọn lọc Tầng 2 cho trang scan, dịch thuật bảo toàn Markdown và xuất tệp Word/PDF lưu vào thư mục S3 `outputs/`.

---

## 4. Danh mục dịch vụ AWS sử dụng

| Dịch vụ AWS | Vai trò kỹ thuật |
| :--- | :--- |
| **Amazon VPC** | Mạng ảo phân tầng doanh nghiệp với 2 Public Subnet Multi-AZ (`ap-southeast-1a`, `ap-southeast-1b`) và Internet Gateway |
| **Security Groups** | Chuỗi an ninh phân tầng (Security Group Chaining): `huylam-alb-sg` (ALB) và `huylam-web-sg` (EC2) |
| **Application Load Balancer** | Cân bằng tải phân tán Multi-AZ, kiểm tra sức khỏe tự động qua `/login` và cấp tên miền công khai |
| **Amazon EC2** | Máy chủ ứng dụng chạy Amazon Linux 2023, t2.micro, quản lý bằng systemd daemon Gunicorn |
| **AWS Systems Manager** | Quản trị máy chủ từ xa an toàn qua Session Manager; Parameter Store lưu cấu hình mã hóa KMS |
| **Amazon S3** | Lưu trữ tệp gốc (`uploads/`) và kết quả (`outputs/`), cấu hình CORS và S3 Event Notification |
| **Amazon DynamoDB** | Bảng NoSQL On-Demand `document_processing_jobs` lưu siêu dữ liệu và trạng thái tiến trình |
| **AWS Lambda** | Xử lý sự kiện tải tệp tự động theo kiến trúc phi máy chủ (Serverless Event-Driven) |
| **Amazon CloudWatch** | Giám sát tập trung toàn bộ nhật ký (Logs), chỉ số hiệu năng (Metrics) và sức khỏe hệ thống |

---

## 5. Kết quả mong đợi sau chuỗi Workshop

Sau khi hoàn thành 12 bài thực hành trong workshop này, bạn sẽ:
- Tự tay triển khai thành công mô hình mạng đám mây 3 tầng chuẩn doanh nghiệp trên AWS.
- Cấu hình chuỗi bảo mật an ninh phân tầng loại bỏ triệt để nguy cơ lộ cổng ứng dụng ra ngoài Internet.
- Vận hành máy chủ EC2 an toàn không cần duy trì SSH Key pair hay mở cổng 22.
- Triển khai ứng dụng Web Studio có khả năng bóc tách tài liệu số siêu tốc 0.1s/trang và dịch thuật bảo toàn 100% Markdown.
- Xây dựng kiến trúc xử lý sự kiện tự động Serverless với độ trễ phản hồi chỉ 214 ms.
- Nắm vững quy trình quản trị chi phí FinOps bảo toàn ngân sách 0.00 USD.