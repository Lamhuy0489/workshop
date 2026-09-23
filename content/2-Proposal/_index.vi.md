---
title: "Đề xuất dự án"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS

## Đề Án Tốt Nghiệp: Nền Tảng Bóc Tách, OCR Lai & Dịch Thuật Tài Liệu Kỹ Thuật Đa Tầng Trên Đám Mây AWS

---

### Thông Tin Định Danh Học Viên & Hệ Thống

* **Họ và tên sinh viên**: Lâm Quang Huy
* **Mã số sinh viên (MSSV)**: `0212267`
* **Lớp chuyên ngành**: 67CS - Khoa Công nghệ Thông tin
* **Cơ sở đào tạo**: Trường Đại học Xây dựng Hà Nội (HUCE)
* **Tài khoản AWS (Account ID)**: `677994024390` | **Account Name**: `huylam`
* **Khu vực triển khai (AWS Region)**: `ap-southeast-1` (Asia Pacific - Singapore)
* **Đường link hệ thống thực tế (Live Production URL)**: [http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com](http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com)
* **Kho mã nguồn ứng dụng & hạ tầng (GitHub Application Repo)**: [https://github.com/Lamhuy0489/aws](https://github.com/Lamhuy0489/aws)
* **Kho tài liệu báo cáo & Workshop (GitHub Docs Repo)**: [https://github.com/Lamhuy0489/workshop](https://github.com/Lamhuy0489/workshop)

---

## 1. Tóm Tắt Dự Án (Executive Summary)

**Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS** là nền tảng điện toán đám mây cấp doanh nghiệp, được thiết kế nhằm giải quyết bài toán số hóa, bóc tách cấu trúc và chuyển ngữ các tài liệu kỹ thuật phức tạp (hồ sơ thiết kế, hợp đồng, báo cáo tài chính, bài báo khoa học dạng PDF và ảnh scan đa ngôn ngữ) với độ chính xác cao, thời gian xử lý tức thì và chi phí vận hành tối ưu tuyệt đối (0.00 USD trong suốt kỳ thực tập).

Hệ thống kết hợp ba trụ cột công nghệ cốt lõi:
1. **Động cơ Bóc tách Cấu trúc Lai Đa tầng (Two-Stage Hybrid Parsing Engine)**:
   * **Tầng 1 (Fast-Path Native)**: Trích xuất trực tiếp luồng văn bản số và bảng biểu trong tệp PDF bằng thư viện chuyên dụng (`PyMuPDF`) với độ trễ cực thấp từ **0.1s - 0.3s/trang** mà không cần qua mô hình AI, giúp tiết kiệm 100% chi phí tính toán cho hơn 80% tài liệu văn phòng thông thường.
   * **Tầng 2 (Selective Vision OCR)**: Tự động phát hiện các trang là ảnh scan hoặc biểu mẫu phức tạp để định tuyến có chọn lọc sang mô hình thị giác AI (Kaggle Qwen2.5-VL qua Cloudflare Tunnel kết hợp cơ chế tự phục hồi Failover sang Google Gemini 3.6 Flash và tùy chọn AWS Native Bedrock Nova).
2. **Động cơ Dịch thuật Kỹ thuật Đa ngôn ngữ Bảo toàn Cấu trúc (Markdown-Preserving Technical Translation Engine)**:
   * Phân trang và chuyển ngữ văn bản chuyên ngành sang Tiếng Việt và các ngôn ngữ phổ biến (Tiếng Anh, Tiếng Nhật, Tiếng Hàn, Tiếng Trung, Tiếng Pháp, Tiếng Đức).
   * Bảo toàn 100% cú pháp cấu trúc Markdown, tiêu đề phân cấp, danh sách lồng nhau và các bảng biểu số liệu kỹ thuật phức tạp.
3. **Động cơ Xuất bản Đa định dạng (Multi-Format Export Engine)**:
   * Kết xuất tự động tài liệu sang Markdown (`.md`), Microsoft Word (`.docx` chuẩn tương thích macOS và Windows) và PDF in ấn chuẩn A4.

Hệ thống được triển khai trên nền tảng AWS theo kiến trúc **Mạng doanh nghiệp 3 tầng (Three-Tier Enterprise Cloud Architecture)** kết hợp với mô hình **Phi máy chủ hướng sự kiện (Event-Driven Serverless)**:
* **Tầng phân phối lưu lượng (Ingress Tier)**: Application Load Balancer `huylam-ocr-alb` đa vùng sẵn sàng Multi-AZ, tiếp nhận lưu lượng HTTP cổng 80 từ Internet và định tuyến thông minh.
* **Tầng máy chủ ứng dụng (Application Tier)**: Máy chủ ảo Amazon EC2 `huylam-ocr-web-server` (AL2023, t2.micro) đặt sau chuỗi bảo mật Security Group Chaining, vận hành dịch vụ daemon systemd Gunicorn trên cổng nội bộ 5000, quản trị an toàn từ xa qua AWS Systems Manager Session Manager mà không cần mở cổng SSH.
* **Tầng lưu trữ & cơ sở dữ liệu (Storage & Database Tier)**: Kho đối tượng Amazon S3 `huylam-ocr-documents-ap-southeast-1` lưu trữ tệp gốc (`uploads/`) và tệp kết quả (`outputs/`); Cơ sở dữ liệu NoSQL Amazon DynamoDB `document_processing_jobs` ghi nhận siêu dữ liệu tiến trình xử lý.
* **Tầng quản trị cấu hình bảo mật**: AWS Systems Manager Parameter Store `/huylam-ocr/config` (loại `SecureString` mã hóa bởi AWS KMS), loại bỏ hoàn toàn việc lưu trữ khóa API cứng trong mã nguồn.
* **Tầng tự động hóa hướng sự kiện (Event-Driven Pipeline)**: Tự động kích hoạt khi có tệp mới tải lên S3 (`s3:ObjectCreated:*`), kích hoạt AWS Lambda `huylam-ocr-processor` khởi tạo bản ghi tiến trình trong DynamoDB và giám sát qua Amazon CloudWatch Logs với thời gian phản hồi chỉ 214 ms.

---

## 2. Vấn Đề Thực Tế & Giải Pháp Đề Xuất (Problem Statement & Solution)

### 2.1. Thách thức trong xử lý tài liệu kỹ thuật hiện nay
1. **Làm vỡ cấu trúc và bảng biểu**: Các giải pháp OCR truyền thống (như Tesseract) chỉ nhận dạng ký tự rời rạc dạng phẳng, làm xáo trộn thứ tự đọc của bố cục nhiều cột và phá vỡ cấu trúc bảng biểu số liệu kỹ thuật.
2. **Chi phí và độ trễ khổng lồ khi lạm dụng Vision AI**: Nạp toàn bộ tài liệu 50 - 100 trang vào các API thị giác lớn (Vision LLM) gây nghẽn băng thông, thời gian phản hồi lên tới vài phút và phát sinh chi phí tính toán GPU đắt đỏ, trong khi thực tế phần lớn các trang tài liệu kỹ thuật văn phòng đã có sẵn luồng văn bản số.
3. **Mất định dạng khi dịch thuật tự động**: Các công cụ dịch thuật văn phòng thông thường làm gãy liên kết cú pháp Markdown, đảo lộn tiêu đề và làm hỏng bảng biểu khi chuyển ngữ.
4. **Bảo mật và cô lập hạ tầng đám mây**: Việc quản trị máy chủ qua cổng SSH 22 công khai tiềm ẩn nguy cơ tấn công brute-force; lưu trữ cứng thông tin xác thực trên máy chủ dễ dẫn đến rò rỉ bảo mật.

### 2.2. Giải pháp kỹ thuật của đề tài
Nền tảng của đồ án giải quyết trọn vẹn các bài toán trên thông qua các đột phá kiến trúc:
* **Cơ chế phân luồng Fast-Path + Selective OCR**: Phân loại tài liệu tại chỗ, bóc tách tức thì 80 - 90% các trang văn bản số chỉ trong 0.1s - 0.3s bằng PyMuPDF với chi phí 0 USD; chỉ kích hoạt mô hình thị giác cho các trang chứa ảnh quét scan.
* **Kỹ thuật dịch thuật Prompt Engineering định hướng cấu trúc**: Giữ nguyên vẹn 100% ma trận bảng biểu Markdown (`| Cột 1 | Cột 2 |`), danh sách gạch đầu dòng và tiêu đề kỹ thuật.
* **Chuỗi an ninh phân tầng Security Group Chaining**: Cô lập hoàn toàn máy chủ EC2 khỏi Internet; cổng ứng dụng 5000 chỉ mở duy nhất cho địa chỉ của Application Load Balancer.
* **Cơ chế xác thực không dùng khóa tĩnh (Zero Static Credentials)**: Ứng dụng EC2 và Lambda tương tác với S3 và DynamoDB thông qua IAM Instance Profile và STS Token tạm thời; cấu hình quản trị tập trung tại SSM Parameter Store mã hóa KMS.

---

## 3. Sơ Đồ Kiến Trúc Hệ Thống (Architecture Blueprint)

Hệ thống được thiết kế theo chuẩn **AWS Well-Architected Framework**, phối hợp giữa mô hình phân tầng **Three-Tier Enterprise Cloud Networking** và đường ống xử lý **Event-Driven Serverless**:

![Sơ đồ kiến trúc tổng thể AWS Serverless Hybrid OCR Platform](/images/architecture/aws-system-architecture.png?width=100%&classes=border,shadow)

> [!NOTE] Định dạng tệp sơ đồ kiến trúc
> * **Ảnh kết xuất độ nét cao**: `/images/architecture/aws-system-architecture.png` (Độ phân giải chuẩn Retina 1400x920)
> * **Sơ đồ đồ họa Vector SVG**: `/images/architecture/aws-system-architecture.svg`
> * **Tệp thiết kế nguồn Draw.io**: `/images/architecture/aws-system-architecture.drawio` (Có thể nhập trực tiếp vào [diagrams.net](https://app.diagrams.net/) để tùy biến kéo thả theo các stencil biểu tượng AWS chính thức).

### Bảng phân rã các thành phần trong kiến trúc giải pháp:

| Tầng kiến trúc | Dịch vụ & Công nghệ | Định danh tài nguyên | Vai trò và chức năng cốt lõi |
| :--- | :--- | :--- | :--- |
| **Tầng mạng & Cân bằng tải** | AWS Application Load Balancer (ALB) | `huylam-ocr-alb` | Tiếp nhận lưu lượng HTTP cổng 80 từ Internet, cân bằng tải Multi-AZ và chuyển tiếp vào Target Group `huylam-ocr-tg`. |
| **Chuỗi tường lửa phân tầng** | AWS Security Groups | `huylam-alb-sg` &rarr; `huylam-web-sg` | `huylam-alb-sg` mở cổng 80; `huylam-web-sg` chỉ mở cổng 5000 cho duy nhất ALB, loại bỏ triệt để nguy cơ tấn công trực diện. |
| **Tầng máy chủ ứng dụng** | Amazon EC2 (Amazon Linux 2023) | `huylam-ocr-web-server` | Máy chủ t2.micro (`10.0.8.15`) chạy daemon systemd Gunicorn WSGI phục vụ giao diện Web Studio đơn trang (SPA). |
| **Tầng bóc tách siêu tốc (T1)** | Fast-Path Native Parser | `src/backend/parsers/fast_parser.py` | Sử dụng PyMuPDF trích xuất văn bản số hóa chỉ mất 0.1s - 0.3s/trang với chi phí 0.00 USD (xử lý 80%+ tài liệu văn phòng). |
| **Tầng OCR chọn lọc (T2)** | Selective OCR Dispatcher | `src/backend/parsers/ocr_dispatcher.py` | Chỉ kích hoạt khi gặp trang scan/ảnh: điều phối tới cụm Kaggle GPU Qwen2.5-VL ($0) hoặc Amazon Bedrock (Nova / Claude). |
| **Động cơ chuyển ngữ kỹ thuật** | Technical Translation Engine | `src/backend/llm/translator.py` | Dịch thuật bảo toàn 100% cú pháp Markdown, công thức toán LaTeX và xoay vòng API Keys tự động (Key Tour Manager). |
| **Tầng lưu trữ tài liệu** | Amazon Simple Storage Service (S3) | `huylam-ocr-documents-ap-southeast-1` | Lưu trữ tệp gốc tại `uploads/` và tệp kết quả (.md, .docx, .pdf) tại `outputs/{job_id}/` với mã hóa SSE-S3. |
| **Tự động hóa phi máy chủ** | AWS Lambda | `huylam-ocr-processor` | Tiếp nhận sự kiện S3 Event `s3:ObjectCreated:*` trong 214 ms, sinh mã `job_id` và kích hoạt luồng xử lý phi máy chủ. |
| **Cơ sở dữ liệu trạng thái** | Amazon DynamoDB | `document_processing_jobs` | Bảng NoSQL chế độ On-Demand lưu trữ trạng thái tiến trình `RECEIVED_VIA_S3_EVENT`, nhật ký và đường dẫn tải tệp. |
| **Giám sát & Quản trị an toàn** | CloudWatch & Systems Manager | SSM Session Manager & CloudWatch Logs | Quản trị shell từ xa không cần SSH cổng 22; thu thập số liệu vận hành và nhật ký thực thi tập trung. |

---

## 4. Danh Mục Dịch Vụ AWS & Công Nghệ Cốt Lõi

| Dịch vụ / Công nghệ | Vai trò trong hệ thống | Lý do lựa chọn kỹ thuật |
| :--- | :--- | :--- |
| **Amazon VPC** (`huylam-vpc`) | Mạng ảo phân tầng doanh nghiệp | Dải mạng `10.0.0.0/16`, 2 Public Subnet Multi-AZ (`1a` và `1b`), Internet Gateway `huylam-igw` |
| **Security Groups** | Chuỗi tường lửa phân tầng (Chaining) | `huylam-alb-sg` mở HTTP 80 cho Internet; `huylam-web-sg` chỉ mở TCP 5000 từ ALB SG |
| **Application Load Balancer** (`huylam-ocr-alb`) | Cân bằng tải và cấp tên miền công khai | Phân phối lưu lượng Multi-AZ, tự động kiểm tra sức khỏe máy chủ qua đường dẫn `/login` |
| **Amazon EC2** (`huylam-ocr-web-server`) | Máy chủ ứng dụng Web Studio | Amazon Linux 2023, loại t2.micro (Free Tier), chạy daemon systemd Gunicorn |
| **AWS Systems Manager** | Quản trị từ xa và cấu hình bảo mật | Session Manager (không mở SSH 22); Parameter Store lưu cấu hình mã hóa KMS |
| **Amazon S3** (`huylam-ocr-documents-ap-southeast-1`) | Kho lưu trữ tài liệu gốc và kết quả | Tích hợp thư mục `uploads/`, `outputs/`, chính sách CORS và sự kiện Event Notification |
| **Amazon DynamoDB** (`document_processing_jobs`) | Quản lý tiến trình và trạng thái xử lý | Cơ sở dữ liệu NoSQL On-Demand (`PAY_PER_REQUEST`), truy xuất mili-giây, chi phí 0 USD nhàn rỗi |
| **AWS Lambda** (`huylam-ocr-processor`) | Xử lý sự kiện tự động Serverless | Khởi tạo bản ghi DynamoDB tức thì khi có tài liệu mới tải lên S3, thời gian chạy 214 ms |
| **Amazon CloudWatch** | Giám sát và ghi nhận nhật ký tập trung | Giám sát CloudWatch Logs cho Lambda, theo dõi tình trạng Target Group và EC2 |
| **PyMuPDF & Python 3.11** | Bóc tách văn bản số Tầng 1 (Fast-Path) | Trích xuất văn bản số trực tiếp trong 0.1s - 0.3s/trang với chi phí tính toán 0 USD |
| **Kaggle GPU & Gemini Flash** | Nhận diện thị giác Tầng 2 (Selective OCR) | Nhận diện trang scan đa ngôn ngữ, tự động chuyển đổi dự phòng (Failover) an toàn |
| **Amazon Bedrock (Nova)** | Tùy chọn AI khép kín nội bộ AWS | Cung cấp tùy chọn mô hình AWS Native độc lập trong Web Studio khi cần bảo mật khép kín |
| **Docker & Amazon ECR** | Đóng gói và lưu trữ Container | Image chuẩn OCI Container trên nền `python:3.11-slim`, sẵn sàng triển khai đám mây |

---

## 5. Bảng Số Liệu Đo Kiểm Thực Nghiệm (Benchmark Empirical Metrics)

| Hạng mục kiểm thử | Mẫu thử nghiệm | Động cơ thực thi | Thời gian xử lý | Chi phí ước tính | Đánh giá chất lượng |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bóc tách văn bản số (Fast-Path)** | `cv.pdf` (1 trang) | Fast-Path Native (PyMuPDF) | **0.31 giây** | 0.00 USD | Xuất sắc, phản hồi tức thì |
| **Bóc tách bài báo khoa học** | `28_Bai_Bao_...pdf` (11 trang) | Fast-Path Native (PyMuPDF) | **3.07 giây** (~0.28s/trang) | 0.00 USD | Xuất sắc, cấu trúc bài báo nguyên vẹn |
| **Bóc tách ảnh quét scan (OCR)** | Ảnh scan biểu mẫu tiếng Việt | Kaggle Qwen2.5-VL / Gemini | **2.54 giây** | 0.00 USD (Free Tier) | Nhận dạng tiếng Việt có dấu đầy đủ |
| **Bóc tách tùy chọn AWS Native** | Ảnh scan văn bản | Amazon Bedrock (Nova) | **4.61s - 6.99s** | Pay-as-you-go | Trích xuất chính xác bảng biểu số liệu |
| **Dịch thuật bảo toàn Markdown** | `cv.pdf` (Anh -> Việt) | Gemini Flash Translator | **2.80 giây** | 0.00 USD (Free Tier) | Bảo toàn 100% tiêu đề và bảng biểu |
| **Xuất bản Microsoft Word (.docx)** | `cv.pdf` -> `cv.pdf.docx` | DocxExporter Module | **0.15 giây** | 0.00 USD | Tệp 38.2 KB mở chuẩn trên Word macOS |
| **Kích hoạt tự động S3 -> Lambda** | Tệp tải lên `uploads/` | AWS Lambda (Python 3.11) | **214 - 257 ms** | 0.00 USD (Free Tier) | Tự động tạo bản ghi DynamoDB tức thì |
| **Độ trễ phản hồi ALB Production** | Truy cập `http://huylam-ocr-alb...` | Application Load Balancer | **15 - 25 ms** | 0.00 USD (Free Tier) | Chuyển hướng 302 sang `/login` mượt mà |

---

## 6. Lộ Trình Triển Khai 12 Tuần & Kết Quả Đạt Được

- **Tuần 1 - 4 (Nền tảng hạ tầng đám mây AWS)**: Quản trị danh tính IAM, thiết lập mạng VPC, máy chủ EC2 Linux, lưu trữ đối tượng Amazon S3 và quản lý hệ thống qua AWS Systems Manager.
- **Tuần 5 - 8 (Giám sát, Cân bằng tải & Containerization)**: Thiết lập giám sát CloudWatch Alarms, hạ tầng dạng mã CloudFormation, đóng gói Docker và lưu trữ image trên Amazon ECR.
- **Tuần 9 (Thiết kế Kiến trúc Serverless)**: Thiết kế kiến trúc giải pháp Serverless Microservices, lập lược đồ DynamoDB `document_processing_jobs`, xây dựng cơ chế cấp S3 Presigned URL.
- **Tuần 10 (Triển khai Hạ tầng Lưu trữ & Dữ liệu AWS)**: Tạo S3 Bucket `huylam-ocr-documents-ap-southeast-1` (`uploads/`, `outputs/`), bảng DynamoDB On-Demand, và tham số SSM Parameter Store `/huylam-ocr/config`.
- **Tuần 11 (Đo kiểm Hiệu năng & Tự động hóa Event-Driven)**: Đo kiểm bộ Benchmark thực nghiệm, đóng gói Dockerfile OCI Container, thiết lập kiến trúc tự động hóa S3 Event Notification -> AWS Lambda -> DynamoDB -> CloudWatch Logs.
- **Tuần 12 (Triển khai Đám mây 3 Tầng & Cấp Link Thật Công Khai)**:
  * Triển khai VPC Multi-AZ (`huylam-vpc`), Subnets, Internet Gateway và chuỗi Security Groups (`huylam-alb-sg`, `huylam-web-sg`).
  * Khởi chạy máy chủ EC2 `huylam-ocr-web-server` với IAM Role `huylam-ssm-role`, kết nối an toàn qua Session Manager, kích hoạt dịch vụ daemon systemd Gunicorn chạy Web Studio.
  * Cấu hình Target Group `huylam-ocr-tg` (Health check `/login`, trạng thái Healthy 1/1) và Application Load Balancer `huylam-ocr-alb` đa vùng sẵn sàng.
  * Cấp phát đường link công khai thật hoạt động trên toàn cầu: `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`.
  * Thu thập bộ 10 ảnh minh chứng có khung viền đỏ chuẩn xác bao quanh Account Badge `huylam (677994024390)`.

---

## 7. Quản Trị Tài Chính FinOps (0.00 USD Budget Optimization)

Dự án áp dụng chặt chẽ các nguyên tắc quản trị tài chính đám mây FinOps nhằm bảo đảm toàn bộ hệ thống vận hành bền vững trong hạn mức **0.00 USD**:
1. **Kiến trúc Serverless theo sự kiện (Pay-as-you-go)**: DynamoDB hoạt động ở chế độ On-Demand không tính cước khi nhàn rỗi; Lambda tận dụng 1 triệu lượt gọi miễn phí mỗi tháng của AWS Free Tier.
2. **Cơ chế bóc tách lai đa tầng**: Tiết kiệm hơn 80% chi phí gọi API mô hình thị giác nhờ xử lý Fast-Path cục bộ bằng PyMuPDF.
3. **Kế hoạch giải phóng tài nguyên (Teardown Governance)**: Toàn bộ ảnh chụp màn hình minh chứng đã được gắn viền đỏ và lưu trữ đầy đủ phục vụ bảo vệ đồ án tốt nghiệp; sinh viên có đầy đủ kịch bản xóa Application Load Balancer và dừng máy chủ EC2 khi hoàn tất phiên nghiệm thu để loại bỏ hoàn toàn chi phí phát sinh.