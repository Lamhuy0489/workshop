---
title: "Kiểm thử tích hợp hệ thống"
date: 2026-09-23
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

### Mục tiêu thực hành

Thực hiện kiểm thử tích hợp toàn trình (End-to-End Testing) cho nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS**: Đo kiểm khả năng phục vụ lưu lượng truy cập qua Application Load Balancer, kiểm chứng hiệu năng bóc tách siêu tốc với Fast-Path PyMuPDF và mô hình đa phương thức, kiểm tra chất lượng dịch thuật kỹ thuật bảo toàn Markdown, xuất bản tệp Microsoft Word chuẩn hóa, và kiểm tra tính nhất quán dữ liệu giữa Amazon S3, DynamoDB và AWS Lambda.

---

## 1. Kịch bản 1: Truy cập và Xác thực qua Application Load Balancer

Hệ thống được kiểm thử truy cập trực tiếp từ mạng internet công cộng thông qua tên miền DNS của Application Load Balancer:
`http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`

### Quy trình kiểm thử:
1. Gửi yêu cầu HTTP GET tới URL của ALB:
   - ALB tiếp nhận trên cổng 80 và chuyển tiếp tới Target Group `huylam-ocr-tg`.
   - Ứng dụng Flask phản hồi HTTP 302 chuyển hướng tới giao diện xác thực `/login`.
2. Trình duyệt tải giao diện đăng nhập bảo mật với mã phản hồi HTTP 200 OK:

![Đăng nhập qua ALB Public DNS](/images/week12/09-browser-alb-public-dns-login.png)

3. Tiến hành đăng nhập vào không gian làm việc Studio:
   - Phiên đăng nhập được quản lý an toàn qua Flask Session (Secret Key lưu trên SSM Parameter Store).
   - Giao diện làm việc hiển thị trực tiếp và đầy đủ trên nền tảng đám mây:

![Giao diện Studio trực tiếp trên ALB](/images/week12/10-browser-alb-studio-live.png)

4. Lựa chọn mô hình thông minh (Smart Model Selector):
   - Hệ thống cho phép linh hoạt lựa chọn: Auto Hybrid (Tự động tối ưu chi phí), AWS Bedrock Titan Multimodal, Google Gemini 2.5 Flash, hoặc Claude 3.5 Sonnet:

![Tùy chọn mô hình thông minh](/images/week11/01-studio-model-selection-auto.png)

---

## 2. Kịch bản 2: Đo kiểm hiệu năng bóc tách, dịch thuật và xuất bản tài liệu

Tiến hành tải lên tài liệu kiểm thử kỹ thuật thực tế (`cv.pdf`) gồm nhiều trang, chứa bảng thông tin, tiêu đề phân cấp và văn bản chuyên ngành.

### Kết quả đo kiểm hiệu năng:

| Công đoạn xử lý | Công nghệ thực thi | Thời gian đo kiểm | Đánh giá chất lượng |
|-----------------|-------------------|-------------------|---------------------|
| Bóc tách Fast-Path | PyMuPDF (C++ Native Engine) | **0.31 giây** | Bóc tách 100% ký tự văn bản gốc, giữ nguyên bố cục |
| Bóc tách thị giác OCR | Vision Multimodal Fallback | **2.54 giây** | Nhận diện chính xác các khối ảnh quét |
| Dịch thuật kỹ thuật | LLM Domain-Specific Translation | **2.80 giây** | Giữ nguyên cú pháp Markdown, bảng biểu và thuật ngữ CNTT |
| Xuất bản Microsoft Word | python-docx Artifact Generator | **0.15 giây** | Tạo tệp Word định dạng chuẩn với dung lượng **38.2 KB** |

### Giao diện kết quả xử lý trong Studio:
Giao diện hiển thị trực quan song song giữa bản gốc đã bóc tách và bản dịch thuật tiếng Việt, kèm theo các tùy chọn tải xuống định dạng Markdown hoặc Word:

![Bóc tách và dịch thuật hoàn tất trong Studio](/images/week11/03-studio-cv-extracted-translated.png)

### Kiểm tra tệp Microsoft Word xuất bản:
Tệp `.docx` được tải về máy tính và mở trong Microsoft Word, bảo toàn hoàn hảo các bảng, danh sách gạch đầu dòng, màu sắc và kiểu chữ:

![Kiểm tra tệp Word xuất bản](/images/week11/04-word-cv-exported-verification.png)

---

## 3. Kịch bản 3: Kiểm tra lưu trữ Amazon S3 và Luồng Event-Driven Serverless

Mỗi chu trình xử lý tài liệu đồng thời kích hoạt các dịch vụ lưu trữ và điều phối phi máy chủ:

1. **Amazon S3 - Thư mục uploads/**:
   - Tài liệu gốc được lưu trữ an toàn với định danh duy nhất (UUID):

![Thư mục uploads trên S3](/images/week11/05-s3-bucket-uploads-folder.png)

2. **Amazon S3 - Thư mục outputs/**:
   - Các tệp Markdown và tài liệu đã dịch được lưu trữ độc lập để tải về:

![Thư mục outputs trên S3](/images/week11/06-s3-bucket-outputs-folder.png)

![Tệp kết quả Markdown trên S3](/images/week11/07-s3-output-cv-markdown-file.png)

3. **S3 Event Notification & AWS Lambda**:
   - Sự kiện `s3:ObjectCreated:*` kích hoạt hàm `huylam-ocr-processor`.
   - Hàm hoàn thành trong **214 ms** và tự động ghi bản ghi mới vào DynamoDB.

4. **Amazon DynamoDB**:
   - Bảng `document_processing_jobs` ghi nhận đầy đủ siêu dữ liệu của tác vụ:
     - `job_id`: Mã định danh duy nhất.
     - `filename`: `cv.pdf`.
     - `status`: `RECEIVED_VIA_S3_EVENT`.
     - `s3_input_uri` và `s3_output_md_uri`.
     - `processing_time_seconds`: `0.05`.

![Bản ghi DynamoDB đồng bộ](/images/week11/13-dynamodb-items-received-s3-event.png)

---

## 4. Tổng hợp tích hợp các dịch vụ AWS trong kiểm thử

| Dịch vụ AWS | Vai trò trong hệ thống | Kết quả kiểm thử |
|-------------|------------------------|------------------|
| Amazon VPC | Cô lập tài nguyên mạng Multi-AZ trên dải mạng 10.0.0.0/16 | Hoạt động ổn định, bảo mật cao |
| Application Load Balancer | Phân phối lưu lượng HTTP công cộng, kiểm tra sức khỏe Target | Target Health 1/1 Healthy, định tuyến thông suốt |
| Amazon EC2 | Chạy ứng dụng Python Flask trên nền Gunicorn và systemd | Xử lý yêu cầu với thời gian phản hồi dưới 0.4s |
| Amazon S3 | Lưu trữ tài liệu gốc và kết quả đầu ra | Tải lên và tải xuống nhanh chóng, kích hoạt sự kiện tự động |
| AWS Lambda | Xử lý phi máy chủ các sự kiện S3 | Thực thi trong 214 ms, tiêu thụ 88 MB RAM |
| Amazon DynamoDB | Quản lý trạng thái và tiến trình tài liệu | Ghi và truy vấn trạng thái gần như tức thời (< 10 ms) |
| AWS SSM Parameter Store | Lưu trữ cấu hình nhạy cảm tập trung mã hóa KMS | Nạp thông tin cấu hình an toàn lúc khởi động |
| Amazon CloudWatch | Giám sát trạng thái Target Group và thu thập nhật ký Lambda | Quan sát toàn diện mọi luồng dữ liệu |

---

## 5. Kết luận kiểm thử

Quá trình kiểm thử thực tế chứng minh nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation** vận hành hoàn hảo trên hạ tầng đám mây AWS. Kiến trúc kết hợp thành công tính ổn định của máy chủ EC2 với sự linh hoạt, tiết kiệm chi phí của kiến trúc Serverless Event-Driven, đáp ứng trọn vẹn cả tiêu chí về hiệu năng xử lý lẫn tối ưu hóa chi phí vận hành 0.00 USD.