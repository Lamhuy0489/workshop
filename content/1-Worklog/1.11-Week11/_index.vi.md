---
title: "Worklog Tuần 11"
date: 2026-10-18
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

> [!NOTE] Thời gian thực hiện
> **Từ ngày 12/10/2026 đến ngày 18/10/2026**

### Mục tiêu tuần 11:
* Đo kiểm hiệu năng thực tế (End-to-End Benchmark) và kiểm thử tích hợp toàn diện trên nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
  * **Tốc độ bóc tách Tầng 1 Fast-Path**: Đo kiểm khả năng trích xuất trực tiếp luồng văn bản số của PDF bằng PyMuPDF, xác thực mục tiêu độ trễ cực thấp (0.1s - 0.3s/trang) với chi phí 0 USD.
  * **Tùy chọn mô hình AWS Native độc lập**: Triển khai và tích hợp tùy chọn mô hình **AWS Native (Amazon Bedrock / Nova)** vào danh sách bộ chọn mô hình của Web Studio dưới dạng một tùy chọn độc lập (không chạy song song) nhằm tối ưu chi phí token đám mây và giảm tải tài nguyên hệ thống.
  * **Đo kiểm Dịch thuật kỹ thuật**: Xác thực chất lượng chuyển ngữ tài liệu sang Tiếng Việt với yêu cầu bảo toàn 100% cấu trúc Markdown, tiêu đề, danh sách và bảng biểu kỹ thuật.
  * **Xuất bản đa định dạng**: Kiểm thử tính năng kết xuất văn bản ra tệp Microsoft Word (`.docx`) và PDF chuẩn in ấn, xác thực độ tương thích thực tế trên phần mềm Microsoft Word macOS.
  * **Kiểm chứng đồng bộ tự động lên AWS Cloud**: Xác thực việc tự động đẩy tệp gốc vào Amazon S3 `uploads/`, đẩy kết quả bóc tách vào S3 `outputs/`, và ghi nhật ký tiến trình xử lý vào bảng Amazon DynamoDB `document_processing_jobs`.
  * **Đóng gói ứng dụng Web Studio bằng Docker Container**: Xây dựng `Dockerfile` và `.dockerignore` chuẩn OCI Container cho ứng dụng Web Studio trên nền `python:3.11-slim`, chuẩn bị sẵn sàng cho việc triển khai lên AWS ECS Fargate và Amazon ECR.
  * **Xây dựng kiến trúc xử lý tài liệu hướng sự kiện (Event-Driven Architecture)**: Người dùng trực tiếp cấu hình luồng **Amazon S3 Event Notification -> AWS Lambda `huylam-ocr-processor` -> Amazon DynamoDB `document_processing_jobs` -> Amazon CloudWatch Logs** để tự động tiếp nhận và xử lý tài liệu phi đồng bộ theo thời gian thực.

---

### Các công việc đã triển khai trong tuần 11:

| Thứ | Công việc triển khai | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2 (12/10/2026)** | - Thiết kế và xây dựng bộ kiểm thử hiệu năng bóc tách đa tầng (Benchmark Suite).<br>- Thử nghiệm bóc tách trên các tệp tài liệu số mẫu (`cv.pdf`, `28_Bai_Bao_Hoi nghi KH_Khoa_CNTT_2026.pdf`, văn bản thông tư).<br>- Đo lường độ trễ trích xuất văn bản số Tầng 1 Fast-Path. | Tệp `cv.pdf` bóc tách hoàn tất trong 0.31 giây; tệp bài báo khoa học 11 trang hoàn tất trong 3.07 giây (~0.28s/trang), đạt 100% chỉ tiêu thiết kế. | [PyMuPDF High-Performance Text Extraction](https://pymupdf.readthedocs.io/en/latest/) |
| **Thứ 3 (13/10/2026)** | - Nâng cấp giao diện Web Studio: Thêm tùy chọn mô hình **AWS Native Model (Amazon Bedrock / Nova)** vào menu chọn mô hình `#modelChoice`.<br>- Cập nhật từ điển đa ngôn ngữ `i18n.js` cho cả giao diện Tiếng Việt và Tiếng Anh.<br>- Cập nhật cấu hình hệ thống `AppSettings.ocr_mode` chấp nhận chế độ `"AWS_NATIVE"`. | Giao diện Web Studio có thêm tùy chọn mô hình AWS Native độc lập, người dùng toàn quyền chủ động chọn mô hình mong muốn mà không bị chạy song song gây lãng phí tài nguyên. | [AWS Bedrock Model Access](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html) |
| **Thứ 4 (14/10/2026)** | - Nâng cấp tầng SDK Boto3 trong `AWSStorageService`: bổ sung tham số `image_bytes` và `image_format` vào hàm `invoke_bedrock_converse` để hỗ trợ gọi Converse API đa phương thức (Multimodal).<br>- Tích hợp cơ chế xử lý chuyển đổi dự phòng an toàn (graceful fallback) trong `OCRDispatcher`: tự động chuyển sang Gemini Flash nếu gặp giới hạn chính sách tài khoản. | Hệ thống sẵn sàng phục vụ OCR qua Amazon Bedrock Converse API với đầy đủ cơ chế bảo vệ dự phòng. | [Amazon Bedrock Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| **Thứ 5 (15/10/2026)** | - Thực hiện đo kiểm chất lượng dịch thuật kỹ thuật trực tiếp trên Web Studio.<br>- Dịch toàn bộ nội dung tệp `cv.pdf` sang Tiếng Việt chuẩn mực kỹ thuật.<br>- Kiểm tra độ nguyên vẹn của định dạng Markdown, các mục thông tin cá nhân, kỹ năng chuyên môn, kinh nghiệm dự án và học máy. | Bản dịch đạt độ dài 3.480 ký tự, cấu trúc Markdown và các trường thông tin được giữ nguyên 100% không suy hao ngữ nghĩa. | [Gemini Technical Translation Prompting](https://ai.google.dev/gemini-api/docs/prompting-strategies) |
| **Thứ 6 (16/10/2026)** | - Kiểm thử luồng xuất bản đa định dạng từ Web Studio: tải về tệp Microsoft Word `cv.pdf.docx` dung lượng 38.2 KB.<br>- Mở tệp `.docx` trực tiếp trên ứng dụng Microsoft Word của macOS.<br>- Xác thực bố cục trang, phân đoạn, cấp độ tiêu đề và định dạng phông chữ hiển thị chuẩn xác.<br>- Xây dựng `Dockerfile` và `.dockerignore` đóng gói ứng dụng Web Studio chuẩn OCI Container cho AWS ECS Fargate. | Tệp Word xuất bản tương thích hoàn hảo với Microsoft Word macOS. Đóng gói mã nguồn thành công và commit lên GitHub repository. | [python-docx Documentation](https://python-docx.readthedocs.io/en/latest/)<br>[AWS ECS Fargate Container Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html) |
| **Thứ 7 (17/10/2026)** | - Thiết lập tự động hóa hướng sự kiện Event-Driven Serverless trên AWS Console:<br>  * Khởi tạo IAM Role `huylam-ocr-lambda-role` với inline policy `LambdaS3DynamoDBAccess`.<br>  * Tạo và triển khai AWS Lambda Function `huylam-ocr-processor` (Python 3.11, ap-southeast-1).<br>  * Cấu hình Amazon S3 Event Notification `NewDocumentUploadTrigger` trên tiền tố `uploads/` kết nối trực tiếp đến Lambda Function. | Hoàn thành hạ tầng Event-Driven Serverless, sẵn sàng tiếp nhận sự kiện khi có tệp tài liệu mới được tải lên S3 bucket. | [Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EventNotifications.html)<br>[AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) |
| **Chủ Nhật**| - Thử nghiệm tải tệp mẫu lên S3 Console: Lambda tự động kích hoạt xử lý trong 214 ms - 257 ms, lưu 3 bản ghi tiến trình vào DynamoDB `document_processing_jobs` (`RECEIVED_VIA_S3_EVENT`).<br>- Giám sát toàn diện trên Amazon CloudWatch Logs.<br>- Thu thập và gắn khung viền đỏ chuẩn xác cho bộ 15 ảnh minh chứng bao quanh Account Badge `huylam (677994024390)`.<br>- Soạn thảo tài liệu Worklog Tuần 11 song ngữ và biên dịch trên website Hugo. | Hoàn thành xuất sắc 100% mục tiêu đo kiểm hiệu năng, đóng gói Docker và tự động hóa hướng sự kiện Tuần 11. | [AWS Free Tier Dashboard](https://aws.amazon.com/free/) |

---

### Số liệu đo kiểm thực nghiệm (Benchmark Metrics):

| Tiêu chí đo kiểm | Tệp thử nghiệm | Mô hình xử lý | Thời gian xử lý | Chi phí FinOps ước tính | Kết quả đánh giá |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bóc tách văn bản số (Fast-Path)** | `cv.pdf` (1 trang) | Fast-Path Native (PyMuPDF) | **0.31 giây** | 0.00 USD | Xuất sắc, phản hồi tức thì |
| **Bóc tách văn bản số nhiều trang** | `28_Bai_Bao_...pdf` (11 trang) | Fast-Path Native (PyMuPDF) | **3.07 giây** (~0.28s/trang) | 0.00 USD | Xuất sắc, cấu trúc bài báo nguyên vẹn |
| **Bóc tách ảnh scan (Vision OCR)** | `Screen Shot ... .png` (1 ảnh) | Google Gemini Flash Lite | **2.54 giây** | 0.00 USD (Free Tier) | Nhận diện chữ tiếng Việt đầy đủ |
| **Bóc tách tùy chọn AWS Native** | `Screen Shot ... .png` (1 ảnh) | AWS Bedrock (Amazon Nova) | **4.61 - 6.99 giây** | Pay-as-you-go | Bóc tách chính xác bảng biểu số liệu |
| **Dịch thuật bảo toàn Markdown** | `cv.pdf` (Tiếng Anh -> Tiếng Việt) | Gemini Flash Translator | **2.80 giây** | 0.00 USD (Free Tier) | Bảo toàn 100% Markdown và tiêu đề |
| **Xuất bản Microsoft Word (.docx)** | `cv.pdf` -> `cv.pdf.docx` | DocxExporter Module | **0.15 giây** | 0.00 USD | Tệp 38.2 KB mở chuẩn trên Word macOS |
| **Kích hoạt tự động S3 Event -> Lambda** | Tệp PDF / PNG trong `uploads/` | AWS Lambda (Python 3.11) | **214 - 257 ms** | 0.00 USD (Lambda Free Tier) | Tự động tạo bản ghi DynamoDB tức thì |

---

### Kiến trúc luồng xử lý hướng sự kiện (Event-Driven Serverless Pipeline):

Luồng xử lý tự động hóa hướng sự kiện hoàn toàn Serverless vận hành theo trình tự sau:

1. **Client / Admin Upload**: Người dùng tải tài liệu (PDF, hình ảnh) lên thư mục `uploads/` của Amazon S3 Bucket `huylam-ocr-documents-ap-southeast-1`.
2. **S3 Event Notification**: Sự kiện `s3:ObjectCreated:*` trên tiền tố `uploads/` được kích hoạt tự động theo bộ lọc `NewDocumentUploadTrigger`.
3. **AWS Lambda Execution**: AWS Lambda Function `huylam-ocr-processor` nhận payload sự kiện S3, phân tích tên tệp, kích thước và sinh mã tiến trình `job_id`.
4. **DynamoDB State Ingestion**: Lambda tạo bản ghi mới trong bảng Amazon DynamoDB `document_processing_jobs` với trạng thái `RECEIVED_VIA_S3_EVENT`, lưu trữ đường dẫn `s3_input_uri` và dự kiến `s3_output_md_uri`.
5. **CloudWatch Monitoring**: Toàn bộ tiến trình thực thi, định danh `RequestId`, thời gian chạy (Duration), và lượng RAM sử dụng được ghi nhận chi tiết tại Amazon CloudWatch Logs `/aws/lambda/huylam-ocr-processor`.

![Sơ đồ kiến trúc tự động hóa hướng sự kiện Tuần 11](/images/architecture/aws-serverless-event-pipeline.png?width=100%&classes=border,shadow)

> [!NOTE] Định dạng tệp sơ đồ Serverless Pipeline Tuần 11
> * **Ảnh kết xuất độ nét cao**: `/images/architecture/aws-serverless-event-pipeline.png` (Chuẩn Retina 1380x720)
> * **Sơ đồ đồ họa Vector SVG**: `/images/architecture/aws-serverless-event-pipeline.svg`
> * **Tệp thiết kế nguồn Draw.io**: `/images/architecture/aws-serverless-event-pipeline.drawio` (Hỗ trợ mở và chỉnh sửa trực tiếp trên [diagrams.net](https://app.diagrams.net/) với các stencil AWS4 chính thức).

---

### Minh chứng thực tế có khung viền đỏ kiểm tra trên AWS Console & Web Studio:

> [!IMPORTANT]
> Toàn bộ các ảnh chụp màn hình minh chứng đều được gắn khung viền đỏ nổi bật bao quanh **AWS Account Badge `huylam (677994024390)`**, tên dịch vụ, vai trò IAM, cấu hình Lambda, sự kiện S3, bảng DynamoDB và nhật ký CloudWatch Logs.

#### 1. Giao diện Web Studio với tùy chọn bóc tách Tự Động (Fast-Path Native):
Giao diện bóc tách tài liệu trực quan với bộ chọn mô hình xử lý, hỗ trợ kéo thả tệp PDF và kích hoạt Tầng 1 Fast-Path Vector siêu tốc:
![Giao diện Web Studio Tự Động](/images/week11/01-studio-model-selection-auto.png)

---

#### 2. Giao diện Web Studio với tùy chọn độc lập AWS Native Model (Amazon Bedrock / Nova):
Tùy chọn mô hình AWS Native được tích hợp trực tiếp trong danh sách thả xuống của Web Studio, vận hành độc lập không chạy song song để tiết kiệm chi phí:
![Tùy chọn mô hình AWS Native trên Studio](/images/week11/02-studio-model-selection-aws-bedrock.png)

---

#### 3. Kết quả bóc tách và dịch thuật tài liệu cv.pdf sang Tiếng Việt trên Web Studio:
Văn bản sau khi trích xuất và chuyển ngữ được hiển thị song song với các nút xuất bản tệp Word (`.docx`) và PDF in ấn chuẩn:
![Kết quả bóc tách và dịch thuật CV trên Studio](/images/week11/03-studio-cv-extracted-translated.png)

---

#### 4. Xác thực tệp kết quả cv.pdf.docx mở trên ứng dụng Microsoft Word macOS:
Tệp Word được kết xuất từ Web Studio tải về máy và mở trực tiếp trên phần mềm Microsoft Word của macOS, bảo toàn đầy đủ phông chữ tiếng Việt, phân đoạn và cấp độ tiêu đề:
![Tệp Word CV mở trên Microsoft Word macOS](/images/week11/04-word-cv-exported-verification.png)

---

#### 5. Thư mục uploads/ trên Amazon S3 Bucket huylam-ocr-documents-ap-southeast-1:
Minh chứng thư mục `uploads/` tiếp nhận các tệp gốc được người dùng tải lên, kèm khung viền đỏ Account Badge `huylam (677994024390)`:
![Thư mục uploads trên Amazon S3](/images/week11/05-s3-bucket-uploads-folder.png)

---

#### 6. Thư mục outputs/ trên Amazon S3 Bucket huylam-ocr-documents-ap-southeast-1:
Minh chứng thư mục `outputs/` tự động lưu trữ các thư mục kết quả bóc tách theo từng mã tiến trình (`job_id`):
![Thư mục outputs trên Amazon S3](/images/week11/06-s3-bucket-outputs-folder.png)

---

#### 7. Chi tiết tệp kết quả cv.pdf.md trong outputs/1f8d7fe1/ trên Amazon S3:
Tệp Markdown kết quả `cv.pdf.md` dung lượng 3.0 KB được tạo tự động lúc 19:38:21 (UTC+07:00), lưu trữ chuẩn xác trên S3 với quyền riêng tư được bảo vệ:
![Chi tiết tệp kết quả Markdown trên Amazon S3](/images/week11/07-s3-output-cv-markdown-file.png)

---

#### 8. IAM Role huylam-ocr-lambda-role và chính sách LambdaS3DynamoDBAccess:
IAM Role dành riêng cho Lambda với chính sách inline policy cho phép đọc ghi dữ liệu trên S3 bucket và bảng DynamoDB:
![IAM Role và Policy cho Lambda](/images/week11/08-iam-role-lambda-policy-created.png)

---

#### 9. Khởi tạo AWS Lambda Function huylam-ocr-processor:
Lambda Function được tạo thành công với môi trường thực thi Python 3.11 tại khu vực ap-southeast-1, gắn với IAM Role `huylam-ocr-lambda-role`:
![Khởi tạo AWS Lambda Function](/images/week11/09-lambda-function-created-active.png)

---

#### 10. Triển khai mã nguồn lambda_function.py trên AWS Lambda:
Mã nguồn xử lý sự kiện S3 Event Notification được triển khai thành công trên Lambda Console, hỗ trợ tạo trạng thái `RECEIVED_VIA_S3_EVENT`:
![Triển khai mã nguồn Lambda thành công](/images/week11/10-lambda-code-deployed-success.png)

---

#### 11. Cấu hình Amazon S3 Event Notification NewDocumentUploadTrigger:
Sự kiện `NewDocumentUploadTrigger` được thiết lập trên prefix `uploads/` của bucket `huylam-ocr-documents-ap-southeast-1`, kích hoạt trực tiếp `huylam-ocr-processor`:
![Cấu hình S3 Event Notification](/images/week11/11-s3-event-notification-created.png)

---

#### 12. Tải tệp tài liệu kiểm thử vào thư mục uploads/ trên S3:
Thao tác tải tài liệu lên thư mục `uploads/` trên AWS Console thành công, sẵn sàng phát sự kiện kích hoạt Lambda:
![Tải tệp kiểm thử lên S3](/images/week11/12-s3-upload-test-document.png)

---

#### 13. Bản ghi tiến trình tự động tạo trong DynamoDB document_processing_jobs:
Bảng DynamoDB ghi nhận 3 bản ghi tiến trình mới (`auto-1e4a3832`, `auto-7c746a72`, `auto-162fdcba`) với trạng thái `RECEIVED_VIA_S3_EVENT` được tạo hoàn toàn tự động qua Lambda:
![Bản ghi DynamoDB tự động tạo qua S3 Event](/images/week11/13-dynamodb-items-received-s3-event.png)

---

#### 14. Nhóm nhật ký /aws/lambda/huylam-ocr-processor trên Amazon CloudWatch:
Nhóm nhật ký CloudWatch Logs ghi nhận luồng sự kiện thực thi của Lambda Function theo thời gian thực:
![Nhóm nhật ký CloudWatch Logs cho Lambda](/images/week11/14-cloudwatch-log-group-overview.png)

---

#### 15. Chi tiết nhật ký thực thi Lambda trên CloudWatch Logs:
Nhật ký chi tiết chứng minh Lambda nhận sự kiện S3, xử lý và ghi vào DynamoDB thành công với thời gian thực thi chỉ **214.37 ms** và bộ nhớ tiêu thụ **88 MB**:
![Chi tiết nhật ký thực thi Lambda trên CloudWatch](/images/week11/15-cloudwatch-log-events-execution.png)

---

### Tổng kết Tuần 11:
* Đã hoàn thành 100% mục tiêu đo kiểm hiệu năng thực tế (Benchmark) cho cả luồng bóc tách Fast-Path, mô hình Vision OCR và dịch thuật kỹ thuật.
* Tích hợp thành công tùy chọn mô hình độc lập **AWS Native Model (Amazon Bedrock / Nova)** trên Web Studio, đáp ứng chuẩn mực kiến trúc đám mây và tối ưu tài nguyên FinOps.
* Xác thực tính toàn vẹn của tệp xuất bản Microsoft Word (`.docx`) mở thực tế trên phần mềm Microsoft Word macOS.
* Chứng minh cơ chế đồng bộ tự động hóa dữ liệu đầu - cuối với Amazon S3 và Amazon DynamoDB vận hành ổn định và bảo mật.
* Đóng gói hoàn chỉnh ứng dụng Web Studio bằng Docker Container chuẩn OCI Container cho AWS ECS Fargate.
* Xây dựng và nghiệm thu thành công kiến trúc xử lý tài liệu hướng sự kiện hoàn toàn Serverless: **S3 Event Notification -> AWS Lambda -> DynamoDB -> CloudWatch Logs** với độ trễ phản hồi chỉ từ 214 ms đến 257 ms và chi phí 0.00 USD.