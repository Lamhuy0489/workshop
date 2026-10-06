---
title: "Báo cáo thực tập tốt nghiệp (HUCE)"
date: 2026-10-03
weight: 8
chapter: false
pre: " <b> 8. </b> "
---

### Hồ sơ Báo cáo Thực tập Tốt nghiệp HUCE (Mẫu TTTN-06)

Báo cáo thực tập tốt nghiệp chính thức được biên soạn theo đúng biểu mẫu quy chuẩn **TTTN-06** của **Trường Đại học Xây dựng Hà Nội (HUCE)**, hoàn thiện với độ dài 43 trang in A4, 12 bảng biểu số liệu, 16 sơ đồ kiến trúc kỹ thuật độ nét cao và 12 nguồn tài liệu tham khảo khoa học có liên kết trực tiếp.

---

### Tải về Tài liệu Báo cáo Bản gốc

Quý Thầy/Cô, Cán bộ hướng dẫn và độc giả có thể tải về trực tiếp toàn bộ hồ sơ báo cáo dưới hai định dạng chuẩn:

| Định dạng tài liệu | Tên tệp | Kích thước | Liên kết tải về trực tiếp |
| :--- | :--- | :--- | :--- |
| **Microsoft Word (.docx)** | `Baocao.docx` | ~8.7 MB | [Tải về bản Word (Baocao.docx)](/downloads/Baocao.docx) |
| **Adobe PDF (.pdf)** | `Baocao.pdf` | ~13.0 MB | [Tải về bản PDF (Baocao.pdf)](/downloads/Baocao.pdf) |
| **Bản lưu trữ định danh đầy đủ (.docx)** | `Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.docx` | ~8.7 MB | [Tải về bản Word lưu trữ](/downloads/Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.docx) |
| **Bản lưu trữ định danh đầy đủ (.pdf)** | `Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.pdf` | ~13.0 MB | [Tải về bản PDF lưu trữ](/downloads/Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.pdf) |

---

## 1. Thông tin chung về Báo cáo và Sinh viên

- **Tên sinh viên**: Lâm Quang Huy
- **Mã số sinh viên (MSSV)**: `0212267`
- **Lớp / Khóa học**: 67CS - Khóa 67
- **Ngành đào tạo**: Khoa học máy tính
- **Khoa**: Công nghệ thông tin
- **Trường đào tạo**: Trường Đại học Xây dựng Hà Nội (HUCE)
- **Giảng viên hướng dẫn**: ThS. Lê Văn Minh
- **Đơn vị thực tập (ĐVHD)**: CÔNG TY TNHH AMAZON WEB SERVICES VIỆT NAM
- **Cán bộ phụ trách hướng dẫn tại ĐVHD**: Nguyễn Gia Hưng (Email: `hunggia@amazon.com.vn`)
- **Chương trình thực tập**: First Cloud AI Journey (AWS FCAJ Workforce Bootcamp 2026)
- **Thời gian thực tập**: Từ ngày 03-08-2026 đến ngày 25-10-2026 (12 tuần)
- **Đề tài Capstone Project**: **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS**

---

## 2. Tóm tắt Cấu trúc 6 Phần của Báo cáo Thực tập Tốt nghiệp

Bản báo cáo dày 43 trang được chia thành 6 phần trọng tâm:

### PHẦN 1: GIỚI THIỆU CHUNG VỀ ĐƠN VỊ THỰC TẬP
- Tổng quan về Amazon Web Services (AWS) toàn cầu và Công ty TNHH Amazon Web Services Việt Nam.
- Quá trình hình thành, phát triển và hệ sinh thái hạ tầng đám mây dẫn đầu thế giới.
- Cơ cấu tổ chức, môi trường làm việc chuẩn mực quốc tế và văn hóa đổi mới sáng tạo (Amazon Leadership Principles: Customer Obsession, Ownership, Invent and Simplify).
- Tổng quan chương trình học bổng thực chiến AWS First Cloud AI Journey (FCAJ Bootcamp 2026).

### PHẦN 2: KẾ HOẠCH THỰC TẬP TỐT NGHIỆP
- Mục tiêu tổng quát và mục tiêu cụ thể (làm chủ hạ tầng AWS, phát triển nền tảng xử lý tài liệu thông minh, rèn luyện tác phong kỹ sư chuyên nghiệp).
- Kế hoạch công tác chi tiết 12 tuần (Tuần 1 đến Tuần 12) theo chuẩn Biểu mẫu TTTN-06.
- Bảng phân công nhiệm vụ và chỉ tiêu đánh giá định lượng cho từng tuần học tập, nghiên cứu và triển khai.

### PHẦN 3: NỘI DUNG VÀ KẾT QUẢ THỰC TẬP (TRỌNG TÂM KỸ THUẬT)
- **Kiến trúc hạ tầng mạng bảo mật**: Mạng ảo VPC chuẩn Three-Tier (Public Subnet, Private Subnet, Isolated Database Subnet) trải dài trên 2 Vùng sẵn sàng (Multi-AZ) tại khu vực `ap-southeast-1` (Singapore).
- **Động cơ bóc tách kép thông minh (Dual-Engine Hybrid Parser)**:
  - *Tầng 1 (Fast-Path Native Parser)*: Ứng dụng PyMuPDF trích xuất siêu tốc các tệp PDF văn bản số (0.28 giây/trang, chi phí 0.00 USD, bảo toàn 100% cấu trúc bảng biểu).
  - *Tầng 2 (Vision OCR & Fallback Parser)*: Tự động kích hoạt mô hình thị giác máy tính (Qwen-2.5-VL qua Kaggle API / Gemini 2.5 Flash) xử lý các trang scan mờ hoặc chứa chữ viết tay.
- **Hệ thống dịch thuật tài liệu kỹ thuật chuyên sâu**: Sử dụng Large Language Model (LLM) dịch thuật ngữ kỹ thuật tiếng Anh sang tiếng Việt chuẩn xác.
- **Bộ xuất bản đa định dạng (Multi-Format Exporters)**: Xuất dữ liệu đồng thời ra Markdown, Microsoft Word (.docx) và PDF với đầy đủ bảng biểu và kiểu dáng.
- **Đo kiểm thực nghiệm hiệu năng (Benchmark)**: Hệ thống vượt qua các kịch bản kiểm thử toàn trình với độ trễ thấp và độ chính xác cao.
- **Kỷ luật tài chính đám mây (FinOps Zero-Breach)**: Tối ưu hóa việc tận dụng gói miễn phí AWS Free Tier kết hợp cụm GPU ngoài, kiểm soát chi phí thực tế ở mức tuyệt đối **0.00 USD**.
- **Giám sát tập trung với Amazon CloudWatch**: Thiết lập CloudWatch Logs, Metrics, Alarms và SNS Notifications theo dõi liên tục trạng thái hệ thống.

### PHẦN 4: PHÂN TÍCH, ĐÁNH GIÁ KẾT QUẢ VÀ ĐỀ XUẤT GIẢI PHÁP
- Đánh giá mức độ hoàn thành nhiệm vụ theo mục tiêu đề ra (đạt 100% yêu cầu).
- Phân tích những khó khăn kỹ thuật phát sinh và giải pháp khắc phục triệt để.
- Đề xuất các giải pháp công nghệ nâng cao: mở rộng kiến trúc sang Amazon ECS Fargate, tích hợp Amazon Bedrock Knowledge Bases và tự động hóa CI/CD với AWS CodePipeline.

### PHẦN 5: TỰ NHẬN XÉT, ĐÁNH GIÁ VÀ ĐỊNH HƯỚNG NGHỀ NGHIỆP
- Tự đánh giá về tinh thần, thái độ, ý thức kỷ luật và việc tuân thủ nội quy trong suốt 12 tuần.
- Đánh giá kiến thức chuyên môn và kỹ năng thực hành thu nhận được.
- Bài học kinh nghiệm quý báu về tư duy thiết kế kiến trúc, quản trị rủi ro và làm chủ công nghệ mới.
- Định hướng nghề nghiệp cá nhân: Mục tiêu trở thành AWS Certified Solutions Architect & AI/ML Engineer chuyên nghiệp.

### PHẦN 6: KẾT LUẬN, KIẾN NGHỊ VÀ TÀI LIỆU THAM KHẢO
- Tổng kết toàn bộ kỳ thực tập tốt nghiệp.
- Kiến nghị với Nhà trường (HUCE) và Khoa CNTT về việc tăng cường đưa kiến thức Cloud Computing vào chương trình đào tạo chính khóa.
- Danh mục 12 tài liệu tham khảo khoa học chính thống.
- **Khung xác nhận hoàn thành thực tập** theo đúng mẫu quy chuẩn của HUCE (có chữ ký xác nhận của Cán bộ hướng dẫn tại ĐVHD và Giảng viên hướng dẫn).

---

## 3. Bảng Số liệu Đo kiểm Thực nghiệm Hiệu năng (Benchmark)

Bảng trích xuất số liệu thực tế đo kiểm trong Báo cáo tốt nghiệp (Bảng 3.4):

| Tác vụ thực nghiệm | Tài liệu mẫu kiểm thử | Động cơ thực thi | Thời gian xử lý | Chi phí vận hành |
| :--- | :--- | :--- | :--- | :--- |
| **Bóc tách PDF văn bản số** | `cv.pdf` (1 trang) | Fast-Path Native (PyMuPDF) | **0.31 giây** | **0.00 USD** |
| **Bóc tách bài báo khoa học** | `28_Bai_Bao.pdf` (11 trang) | Fast-Path Native (PyMuPDF) | **3.07 giây** (~0.28s/trang) | **0.00 USD** |
| **Bóc tách ảnh scan biểu mẫu** | Ảnh chụp scan hóa đơn | Vision OCR (Kaggle / Gemini) | **2.54 giây** | **0.00 USD (Free Tier)** |
| **Dịch thuật tài liệu kỹ thuật** | `cv.pdf` (Anh -> Việt) | DocumentTranslator (LLM) | **2.80 giây** | **0.00 USD (Free Tier)** |
| **Xuất bản Microsoft Word** | Tệp kết quả sau dịch | DocxExporter Module | **0.15 giây** | **0.00 USD** |

---

## 4. Bảng Ánh xạ Dịch vụ AWS trong Nền tảng (Bảng 3.3)

| Dịch vụ AWS | Vai trò kiến trúc | Lợi ích kỹ thuật mang lại |
| :--- | :--- | :--- |
| **Amazon VPC** | Hạ tầng mạng riêng cô lập | Phân tách mạng 3 tầng (Public, Private, Isolated DB), bảo đảm an ninh mạng tối đa |
| **Amazon EC2** | Máy chủ ứng dụng FastAPI | Vận hành Web Studio với chi phí tối ưu trong gói Free Tier (t2.micro) |
| **Application Load Balancer** | Cân bằng tải ứng dụng | Điều phối lưu lượng HTTP/HTTPS, kiểm tra tình trạng máy chủ (Health Checks) tự động |
| **AWS Systems Manager** | Quản trị máy chủ từ xa | Truy cập shell an toàn không cần mở port SSH 22, quản lý khóa bảo mật qua Parameter Store |
| **Amazon S3** | Lưu trữ đối tượng đám mây | Lưu trữ an toàn các tệp PDF gốc, ảnh scan và tệp Word xuất bản với độ bền 99.999999999% |
| **Amazon DynamoDB** | Cơ sở dữ liệu NoSQL | Quản lý trạng thái tác vụ xử lý theo thời gian thực với độ trễ phản hồi dưới 10ms |
| **AWS Lambda** | Điện toán không máy chủ | Tự động hóa xử lý sự kiện bóc tách tài liệu không cần duy trì máy chủ thường trực |
| **Amazon CloudWatch** | Giám sát và cảnh báo | Thu thập logs tập trung, theo dõi CPU/RAM và tự động gửi thông báo cảnh báo qua SNS |

---

## 5. Quy cách Định dạng Văn bản Học thuật
- **Phông chữ**: Times New Roman, cỡ chữ 13pt (thân bài), 15pt (H1 in đậm căn giữa), 13.5pt (H2 in đậm căn trái), 13pt (H3 in đậm nghiêng căn trái).
- **Giãn dòng**: Cố định đồng nhất 1.3 lines, giãn đoạn dưới 3pt, không ngắt dòng bất thường.
- **Bảng biểu**: 100% chữ đen nền trắng (`#FFFFFF`), đường viền đen mảnh (`#000000`), không sử dụng ô nền xám, tối ưu tuyệt đối cho in ấn đen trắng.
- **Điều hướng tương tác**: Toàn bộ 53 mục trong Mục lục, 16 Hình vẽ và 11 Bảng biểu đều có thể nhấp chuột trực tiếp (`w:hyperlink`) để nhảy đến đúng trang nội dung.
- **Trích dẫn**: 12 tài liệu tham khảo được đánh dấu chuẩn `[1]` đến `[12]` kèm đường dẫn mạng có thể nhấp trực tiếp để đọc tài liệu gốc.
