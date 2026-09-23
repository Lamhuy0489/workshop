---
title: "Các bài blogs đã đăng"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Trong suốt hành trình thực tập tại chương trình **AWS First Cloud AI Journey (FCAJ) Bootcamp 2026**, mình đã biên soạn và công bố **3 bài blog kỹ thuật chuyên sâu** trên mạng xã hội nghề nghiệp **LinkedIn** — chia sẻ những bài học thực chiến, giải pháp kiến trúc và kết quả đo kiểm từ chính đề tài tốt nghiệp: **"Nền tảng bóc tách, nhận dạng ký tự quang học lai và dịch thuật tài liệu kỹ thuật trên đám mây AWS"** (Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS).

### Bảng tổng hợp các bài viết kỹ thuật trên LinkedIn:

| # | Tên bài viết chuyên sâu | Phạm trù kỹ thuật (theo chuẩn FCAJ) | Ngày đăng | Liên kết trực tiếp |
| :---: | :--- | :--- | :---: | :---: |
| **Blog 1** | **Thiết kế chuỗi an ninh Security Group Chaining và loại bỏ hoàn toàn cổng SSH 22 trên AWS: Bài học từ dự án thực tế** | An ninh mạng & Quản trị Zero Trust (ALB, Chained SG, SSM Session Manager) | 23/09/2026 | [Xem trên LinkedIn](https://lnkd.in/p/giyjwMkE) |
| **Blog 2** | **Tối ưu hóa FinOps trong xử lý tài liệu kỹ thuật: Chiến lược bóc tách lai Fast-Path (0.1s/trang) kết hợp Selective OCR với chi phí 0 USD** | Kiến trúc lai & Quản trị chi phí FinOps (Fast-Path PyMuPDF, Kaggle GPU Qwen2.5-VL, Bedrock Failover) | 23/09/2026 | [Xem trên LinkedIn](https://lnkd.in/p/gp_MnmkQ) |
| **Blog 3** | **Xây dựng quy trình tự động hóa phi máy chủ hướng sự kiện trên AWS: Từ S3 Event đến DynamoDB trong 214 ms** | Tự động hóa phi máy chủ (Event-Driven Serverless, S3, Lambda, DynamoDB, CloudWatch, Docker/ECR) | 23/09/2026 | [Xem trên LinkedIn](https://lnkd.in/p/gBfaVCdj) |

---

### [3.1 Blog 1: An ninh mạng & Quản trị Zero Trust](3.1-Blog1/)

Phân tích chuyên sâu về mô hình bảo mật phân tầng (Defense-in-Depth) trong môi trường điện toán đám mây doanh nghiệp: Cấu hình chuỗi Security Group Chaining cô lập máy chủ EC2 đằng sau Application Load Balancer (chỉ mở cổng TCP 5000 cho duy nhất định danh `huylam-alb-sg`), đồng thời loại bỏ hoàn toàn cổng SSH 22 và Bastion Host thông qua việc quản trị shell an toàn bằng AWS Systems Manager (SSM Session Manager).
- **Liên kết bài đăng LinkedIn**: [https://lnkd.in/p/giyjwMkE](https://lnkd.in/p/giyjwMkE)

---

### [3.2 Blog 2: Động cơ bóc tách lai & Tối ưu chi phí FinOps](3.2-Blog2/)

Chia sẻ giải pháp giải quyết điểm nghẽn chi phí và độ trễ khi lạm dụng Vision AI trong các hệ thống Document AI / RAG: Xây dựng động cơ bóc tách lai hai tầng (Hybrid Processing Engine) với Fast-Path PyMuPDF xử lý hơn 80% tài liệu số hóa chỉ mất 0.1s - 0.3s/trang với chi phí 0 USD, kết hợp cơ chế OCR chọn lọc qua cụm GPU Kaggle 2x NVIDIA T4 và chuyển mạch dự phòng Failover sang Gemini Flash / Amazon Bedrock.
- **Liên kết bài đăng LinkedIn**: [https://lnkd.in/p/gp_MnmkQ](https://lnkd.in/p/gp_MnmkQ)

---

### [3.3 Blog 3: Tự động hóa phi máy chủ hướng sự kiện (Serverless Event-Driven)](3.3-Blog3/)

Mổ xẻ kiến trúc xử lý tài liệu phi máy chủ tự động hóa toàn diện từ lúc người dùng tải tệp lên Web Studio: Sự kiện `s3:ObjectCreated:*` kích hoạt hàm AWS Lambda `huylam-ocr-processor`, khởi tạo mã tác vụ và lưu trạng thái tiến trình vào bảng Amazon DynamoDB On-Demand chỉ trong 214 ms mà không cần duy trì máy chủ thăm dò (polling), tích hợp thu thập dữ liệu quan sát trên CloudWatch Logs và đóng gói container chuẩn OCI.
- **Liên kết bài đăng LinkedIn**: [https://lnkd.in/p/gBfaVCdj](https://lnkd.in/p/gBfaVCdj)