---
title: "Đề xuất dự án"
date: 2026-09-18
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Serverless Hybrid Document OCR & Parsing Platform on AWS

## Hệ Thống Bóc Tách & OCR Tài Liệu Lai Tối Ưu Hiệu Năng Trên Nền Tảng Đám Mây AWS

---

# 1. Tóm tắt dự án (Executive Summary)

**Serverless Hybrid Document OCR & Parsing Platform on AWS** là nền tảng xử lý và số hóa tài liệu thông minh thế hệ mới, kết hợp giữa kỹ thuật trích xuất cấu trúc văn bản nhanh (Native Document Parsing) và mô hình thị giác nhân tạo chọn lọc (Selective Vision OCR). Hệ thống cho phép doanh nghiệp tự động bóc tách, nhận dạng các tệp tài liệu phức tạp (hợp đồng, hóa đơn, báo cáo tài chính dạng PDF hoặc ảnh scan đa ngôn ngữ) thành định dạng có cấu trúc chuẩn như Markdown (`.md`), Microsoft Word (`.docx`) và PDF có khả năng tìm kiếm mà vẫn giữ nguyên vẹn 100% bố cục, tiêu đề và bảng biểu số liệu.

Hệ thống được thiết kế theo kiến trúc phi máy chủ (Serverless) và hướng sự kiện (Event-Driven) trên AWS, tích hợp linh hoạt với môi trường tính toán gia tốc bên ngoài (Kaggle GPU/TPU) nhằm tối ưu hóa triệt để chi phí vận hành (0 USD trong giai đoạn thử nghiệm):
- **Cơ chế xử lý hai tầng (Two-Stage Hybrid Parsing)**:
  - *Tầng 1 (Fast-Path)*: Tự động bóc tách trực tiếp luồng văn bản số và cấu trúc bảng trên **AWS Lambda** với tốc độ 0.1 - 0.3 giây/trang cho các tài liệu PDF văn phòng thông thường.
  - *Tầng 2 (Slow-Path)*: Tự động phân loại và chỉ gửi các trang là ảnh scan hoặc chứa biểu mẫu phức tạp sang mô hình thị giác AI để nhận dạng.
- **Hỗ trợ hai chế độ linh hoạt (Dual-Mode)**:
  - *Chế độ Kaggle Accelerated*: Tận dụng tài nguyên GPU/TPU miễn phí trên Kaggle (chạy mô hình Qwen2.5-VL hoặc GOT-OCR qua Cloudflare Tunnel) để xử lý các trang scan phức tạp.
  - *Chế độ Standalone Fallback*: Tự động chuyển tiếp mượt mà sang **Google Gemini 1.5 Flash Vision API** nếu endpoint Kaggle ngoại vi bị ngắt kết nối, bảo đảm hệ thống trên AWS luôn sẵn sàng hoạt động 24/7.
- **Tầng Quản lý Cấu hình & Khóa bảo mật**: Quản lý tập trung qua **AWS Systems Manager (SSM) Parameter Store (SecureString)** được mã hóa bởi **AWS KMS**, giải quyết triệt để rủi ro lộ khóa API.
- **Tầng Lưu trữ & Dữ liệu**: Tệp gốc và tệp kết quả sau xử lý lưu trữ trên **Amazon S3**; Siêu dữ liệu tiến trình (Metadata) và thời gian thực thi lưu trên **Amazon DynamoDB**.
- **Tầng Giao tiếp & Phân phối**: Cung cấp API tiếp nhận qua **Amazon API Gateway** (hỗ trợ cấp S3 Presigned URL để tải tệp lớn an toàn) và giao diện web phân phối toàn cầu qua **Amazon S3 + Amazon CloudFront** có chứng chỉ SSL/TLS từ **AWS Certificate Manager (ACM)**.

---

# 2. Vấn đề và Giải pháp (Problem Statement & Solution)

## 2.1. Vấn đề thực tế
1. **Hạn chế của OCR truyền thống**: Các công cụ OCR thông thường chỉ bóc tách văn bản thô dạng phẳng (flat text), làm vỡ cấu trúc bảng biểu, xáo trộn thứ tự đọc của văn bản nhiều cột, và làm mất hoàn toàn các định dạng phân cấp tiêu đề.
2. **Chi phí và thời gian xử lý khi lạm dụng mô hình Vision AI**: Việc nạp toàn bộ tài liệu 50 - 100 trang vào các mô hình AI thị giác nặng gây nghẽn cổ chai, thời gian chờ đợi kéo dài và phát sinh chi phí tính toán GPU rất lớn, trong khi đa số trang tài liệu văn phòng đã có sẵn văn bản số.
3. **Sự phụ thuộc và rủi ro gián đoạn dịch vụ**: Việc kết nối các endpoint AI bên ngoài tiềm ẩn nguy cơ gián đoạn dịch vụ khi máy chủ ngoại vi hết thời gian làm việc hoặc thay đổi URL.

## 2.2. Giải pháp đề xuất
Dự án xây dựng nền tảng **Serverless Hybrid Document OCR & Parsing Platform** giải quyết trọn vẹn các thách thức trên:
- **Tối ưu hóa tốc độ và chi phí bằng cơ chế Hybrid**: 80-90% các trang PDF số được bóc tách tức thì ở Tầng 1 với chi phí 0 đồng; chỉ 10-20% các trang scan mới được kích hoạt Tầng 2.
- **Giữ nguyên vẹn bố cục và bảng biểu**: Tái lập chính xác bảng biểu thành Markdown Table (`| Cột 1 | Cột 2 |`), phân định rõ ràng các cấp tiêu đề và đoạn văn, hỗ trợ xuất sang cả định dạng `.md` lẫn `.docx`.
- **Kiến trúc bền bỉ có dự phòng (Resilient Architecture)**: Cấu hình động qua AWS SSM Parameter Store kết hợp cơ chế Failover tự động đảm bảo hệ thống không bao giờ bị ngừng trệ.

---

# 3. Sơ đồ kiến trúc giải pháp (Architecture Diagram)

```text
[ Người Dùng / Trình Duyệt ]
             │
             ▼ HTTPS (SSL/TLS)
[ Amazon CloudFront + Amazon S3 Static Hosting ] (Giao diện Quản lý & Xem Kết Quả)
             │
             ▼ REST API Request (Xin Presigned URL hoặc Tra Cứu Trạng Thái)
[ Amazon API Gateway ]
             │
             ├──> 1. Trả về S3 Presigned URL an toàn
             │
[ Amazon S3 Bucket ]
     │
     ├── /uploads/ (File PDF / Ảnh gốc tải lên trực tiếp)
     │      │
     │      ▼ (Sự kiện s3:ObjectCreated tự động kích hoạt)
     ▼
[ AWS Lambda: Hybrid Document Engine ]
     │
     ├── 2. Đọc cấu hình chế độ & Keys ──> [ AWS SSM Parameter Store (SecureString) ]
     │
     ├── 3. Tầng 1: Bóc tách cấu trúc nhanh (Fast-Path bằng PyMuPDF)
     │      - Bóc tách văn bản số, font chữ, bảng biểu
     │      - Phân loại trang scan dựa trên mật độ ký tự
     │
     ├── 4. Tầng 2: OCR chọn lọc (Selective Vision OCR cho trang scan):
     │      ├── [Ưu tiên 1: Kaggle GPU/TPU qua Cloudflare Tunnel] (Qwen2.5-VL / GOT-OCR)
     │      └── [Dự phòng: Gemini 1.5 Flash Vision API] (Tự động kích hoạt khi lỗi)
     │
     ├── 5. Ghép nối và xuất tệp ─────────> [ Amazon S3: /outputs/ ]
     │                                       (.md, .docx, .pdf)
     │
     └── 6. Ghi nhận nhật ký & siêu dữ liệu ─> [ Amazon DynamoDB (document_jobs) ]
                                               [ Amazon CloudWatch (Logs & Metrics) ]
```

---

# 4. Danh mục dịch vụ AWS sử dụng

| Dịch vụ AWS | Vai trò trong hệ thống | Lý do lựa chọn |
| :--- | :--- | :--- |
| **AWS Lambda** | Trung tâm điều phối bóc tách tài liệu (Compute Engine) | Phi máy chủ, tự động kích hoạt theo sự kiện S3, xử lý nhanh Tầng 1 |
| **Amazon S3** | Lưu trữ tài liệu gốc, tệp kết quả (.md, .docx) và Web tĩnh | Độ bền 99.999999999%, tích hợp Presigned URL và Event Notification |
| **Amazon DynamoDB** | Lưu trữ trạng thái xử lý, siêu dữ liệu và thời gian thực thi | Tốc độ mili-giây, cơ chế NoSQL linh hoạt, miễn phí 25 GB vĩnh viễn |
| **AWS Systems Manager** | Quản lý cấu hình chế độ và khóa API an toàn (Parameter Store) | Lưu trữ tham số mã hóa KMS, cho phép đổi cấu hình không cần sửa code |
| **Amazon API Gateway** | Tiếp nhận yêu cầu tải tệp và truy vấn kết quả | Cung cấp REST API an toàn, hỗ trợ CORS và giới hạn lưu lượng gọi |
| **Amazon CloudFront** | Mạng phân phối nội dung (CDN) toàn cầu | Tăng tốc độ tải trang giao diện, chứng chỉ HTTPS miễn phí từ ACM |
| **Amazon CloudWatch** | Giám sát, ghi nhật ký và đo lường hiệu năng xử lý | Theo dõi thời gian thực thi Fast-Path và thời gian gọi Vision OCR |

---

# 5. Kế hoạch triển khai (Implementation Roadmap)

- **Tuần 1 - 2**: Thiết lập môi trường AWS, cấu hình IAM User, AWS Budgets và AWS CLI.
- **Tuần 3 - 4**: Xây dựng kho lưu trữ S3, cấu hình sự kiện S3 Event Notification và tạo bảng DynamoDB lưu siêu dữ liệu tài liệu.
- **Tuần 5 - 6**: Phát triển Tầng 1 (Fast-Path Native Parser) trên Python để đọc cấu trúc PDF và nhận diện trang scan.
- **Tuần 7 - 8**: Hoàn thiện Tầng 2 (Selective OCR Dispatcher) kết nối Kaggle GPU/TPU qua Cloudflare Tunnel và tích hợp Gemini API dự phòng.
- **Tuần 9 - 10**: Phát triển module xuất tệp Markdown / Word (.docx), triển khai giao diện web tải và xem tài liệu song song lên S3 + CloudFront.
- **Tuần 11 - 12**: Kiểm thử toàn diện các định dạng tài liệu thực tế, đo lường tốc độ xử lý, hoàn thiện video demo và viết báo cáo thực tập.