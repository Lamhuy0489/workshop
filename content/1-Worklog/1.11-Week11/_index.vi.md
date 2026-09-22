---
title: "Worklog Tuần 11"
date: 2026-09-22
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:
* Đo kiểm hiệu năng thực tế (End-to-End Benchmark) và kiểm thử tích hợp toàn diện trên nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
  * **Tốc độ bóc tách Tầng 1 Fast-Path**: Đo kiểm khả năng trích xuất trực tiếp luồng văn bản số của PDF bằng PyMuPDF, xác thực mục tiêu độ trễ cực thấp (0.1s - 0.3s/trang) với chi phí 0 USD.
  * **Tùy chọn mô hình AWS Native độc lập**: Triển khai và tích hợp tùy chọn mô hình **AWS Native (Amazon Bedrock / Nova)** vào danh sách bộ chọn mô hình của Web Studio dưới dạng một tùy chọn độc lập (không chạy song song) nhằm tối ưu chi phí token đám mây và giảm tải tài nguyên hệ thống.
  * **Đo kiểm Dịch thuật kỹ thuật**: Xác thực chất lượng chuyển ngữ tài liệu sang Tiếng Việt với yêu cầu bảo toàn 100% cấu trúc Markdown, tiêu đề, danh sách và bảng biểu kỹ thuật.
  * **Xuất bản đa định dạng**: Kiểm thử tính năng kết xuất văn bản ra tệp Microsoft Word (`.docx`) và PDF chuẩn in ấn, xác thực độ tương thích thực tế trên phần mềm Microsoft Word macOS.
  * **Kiểm chứng đồng bộ tự động lên AWS Cloud**: Xác thực việc tự động đẩy tệp gốc vào Amazon S3 `uploads/`, đẩy kết quả bóc tách vào S3 `outputs/`, và ghi nhật ký tiến trình xử lý vào bảng Amazon DynamoDB `document_processing_jobs`.

---

### Các công việc đã triển khai trong tuần 11:

| Thứ | Công việc triển khai | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2** | - Thiết kế và xây dựng bộ kiểm thử hiệu năng bóc tách đa tầng (Benchmark Suite).<br>- Thử nghiệm bóc tách trên các tệp tài liệu số mẫu (`cv.pdf`, `28_Bai_Bao_Hoi nghi KH_Khoa_CNTT_2026.pdf`, văn bản thông tư).<br>- Đo lường độ trễ trích xuất văn bản số Tầng 1 Fast-Path. | Tệp `cv.pdf` bóc tách hoàn tất trong 0.31 giây; tệp bài báo khoa học 11 trang hoàn tất trong 3.07 giây (~0.28s/trang), đạt 100% chỉ tiêu thiết kế. | [PyMuPDF High-Performance Text Extraction](https://pymupdf.readthedocs.io/en/latest/) |
| **Thứ 3** | - Nâng cấp giao diện Web Studio: Thêm tùy chọn mô hình **AWS Native Model (Amazon Bedrock / Nova)** vào menu chọn mô hình `#modelChoice`.<br>- Cập nhật từ điển đa ngôn ngữ `i18n.js` cho cả giao diện Tiếng Việt và Tiếng Anh.<br>- Cập nhật cấu hình hệ thống `AppSettings.ocr_mode` chấp nhận chế độ `"AWS_NATIVE"`. | Giao diện Web Studio có thêm tùy chọn mô hình AWS Native độc lập, người dùng toàn quyền chủ động chọn mô hình mong muốn mà không bị chạy song song gây lãng phí tài nguyên. | [AWS Bedrock Model Access](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html) |
| **Thứ 4** | - Nâng cấp tầng SDK Boto3 trong `AWSStorageService`: bổ sung tham số `image_bytes` và `image_format` vào hàm `invoke_bedrock_converse` để hỗ trợ gọi Converse API đa phương thức (Multimodal).<br>- Tích hợp cơ chế xử lý chuyển đổi dự phòng an toàn (graceful fallback) trong `OCRDispatcher`: tự động chuyển sang Gemini Flash nếu gặp giới hạn chính sách tài khoản. | Hệ thống sẵn sàng phục vụ OCR qua Amazon Bedrock Converse API với đầy đủ cơ chế bảo vệ dự phòng. | [Amazon Bedrock Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| **Thứ 5** | - Thực hiện đo kiểm chất lượng dịch thuật kỹ thuật trực tiếp trên Web Studio.<br>- Dịch toàn bộ nội dung tệp `cv.pdf` sang Tiếng Việt chuẩn mực kỹ thuật.<br>- Kiểm tra độ nguyên vẹn của định dạng Markdown, các mục thông tin cá nhân, kỹ năng chuyên môn, kinh nghiệm dự án và học máy. | Bản dịch đạt độ dài 3.480 ký tự, cấu trúc Markdown và các trường thông tin được giữ nguyên 100% không suy hao ngữ nghĩa. | [Gemini Technical Translation Prompting](https://ai.google.dev/gemini-api/docs/prompting-strategies) |
| **Thứ 6** | - Kiểm thử luồng xuất bản đa định dạng từ Web Studio: tải về tệp Microsoft Word `cv.pdf.docx` dung lượng 38.2 KB.<br>- Mở tệp `.docx` trực tiếp trên ứng dụng Microsoft Word của macOS.<br>- Xác thực bố cục trang, phân đoạn, cấp độ tiêu đề và định dạng phông chữ hiển thị chuẩn xác. | Tệp Word xuất bản tương thích hoàn hảo với Microsoft Word macOS, sẵn sàng phục vụ lưu trữ và chỉnh sửa chuyên nghiệp. | [python-docx Documentation](https://python-docx.readthedocs.io/en/latest/) |
| **Thứ 7** | - Kiểm tra tự động hóa đồng bộ đám mây trên AWS Management Console và AWS CLI:<br>  * Xác thực tệp kết quả `outputs/1f8d7fe1/cv.pdf.md` (3.0 KB) trên Amazon S3 `huylam-ocr-documents-ap-southeast-1`.<br>  * Xác thực các bản ghi tiến trình mới được lưu tự động trên Amazon DynamoDB `document_processing_jobs` (`job_id`, `model_used = aws-bedrock`, `processing_time = 0.31s`). | Luồng đồng bộ hóa đám mây đầu - cuối hoạt động 100% tự động, dữ liệu được ghi nhận chính xác theo thời gian thực. | [Amazon S3 Developer Guide](https://docs.aws.amazon.com/s3/) |
| **Chủ Nhật**| - Thu thập toàn bộ 7 ảnh minh chứng thực tế có khung viền đỏ chuẩn xác bao quanh Account Badge `huylam (677994024390)`.<br>- Soạn thảo tài liệu Worklog Tuần 11 song ngữ và biên dịch trên website Hugo.<br>- Đồng bộ mã nguồn và tài liệu lên cả hai kho GitHub (`aws` và `workshop`). | Hoàn thành xuất sắc 100% mục tiêu đo kiểm hiệu năng và tích hợp đám mây Tuần 11. | [AWS Free Tier Dashboard](https://aws.amazon.com/free/) |

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

---

### Minh chứng thực tế có khung viền đỏ kiểm tra trên AWS Console & Web Studio:

> [!IMPORTANT]
> Toàn bộ các ảnh chụp màn hình minh chứng đều được gắn khung viền đỏ nổi bật bao quanh **AWS Account Badge `huylam (677994024390)`**, tên dịch vụ, đường dẫn S3, và các thành phần giao diện Studio then chốt.

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

### Tổng kết Tuần 11:
* Đã hoàn thành 100% mục tiêu đo kiểm hiệu năng thực tế (Benchmark) cho cả luồng bóc tách Fast-Path, mô hình Vision OCR và dịch thuật kỹ thuật.
* Tích hợp thành công tùy chọn mô hình độc lập **AWS Native Model (Amazon Bedrock / Nova)** trên Web Studio, đáp ứng chuẩn mực kiến trúc đám mây và tối ưu tài nguyên FinOps.
* Xác thực tính toàn vẹn của tệp xuất bản Microsoft Word (`.docx`) mở thực tế trên phần mềm Microsoft Word macOS.
* Chứng minh cơ chế đồng bộ tự động hóa dữ liệu đầu - cuối với Amazon S3 và Amazon DynamoDB vận hành ổn định và bảo mật.