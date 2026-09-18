---
title: "Đề xuất dự án"
date: 2026-09-18
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Enterprise Agentic RAG Platform on AWS

## Hệ Thống Trợ Lý Tri Thức Thông Minh Và Tự Động Hóa Nghiệp Vụ Trên Nền Tảng AWS

---

# 1. Tóm tắt dự án (Executive Summary)

**Enterprise Agentic RAG Platform on AWS** là giải pháp nền tảng trí tuệ nhân tạo thế hệ mới (Agentic AI) kết hợp giữa kỹ thuật tăng cường truy xuất thông tin (Retrieval-Augmented Generation - RAG) và cơ chế tác nhân tự suy luận (AI Agents). Nền tảng cho phép doanh nghiệp tra cứu tài liệu nội bộ, tự động điều phối các công cụ nghiệp vụ (truy vấn cơ sở dữ liệu, kiểm tra trạng thái vé hỗ trợ, gửi thông báo) thông qua ngôn ngữ tự nhiên.

Hệ thống được thiết kế theo kiến trúc phi máy chủ (Serverless & Cloud-Native) trên AWS nhằm tối ưu hóa chi phí vận hành (tiếp cận mức 0 USD trong giai đoạn thử nghiệm), khả năng tự động co giãn theo tải thực tế và bảo mật tối đa:
- **Tầng Điều phối Agent**: Triển khai trên **AWS Lambda** (Python) sử dụng mô hình ReAct (Reasoning + Acting) kết hợp LangChain / LangGraph.
- **Tầng Bảo mật API Key**: Sử dụng **AWS Systems Manager Parameter Store (SecureString)** được mã hóa bởi **AWS KMS**, giải quyết triệt để rủi ro lộ khóa API.
- **Tầng Lưu trữ Tri thức & Dữ liệu**: Tài liệu gốc lưu trữ trên **Amazon S3**; Lịch sử phiên hội thoại (Conversation Memory) lưu trên **Amazon DynamoDB** có cơ chế tự động dọn dẹp bằng Time-to-Live (TTL).
- **Tầng Giao tiếp & Phân phối**: Cung cấp API qua **Amazon API Gateway** (hỗ trợ CORS và Rate Limiting) và giao diện web tĩnh phân phối toàn cầu qua **Amazon S3 + Amazon CloudFront** có chứng chỉ SSL/TLS từ **AWS Certificate Manager (ACM)**.
- **Tầng Mô hình Ngôn ngữ**: Tích hợp linh hoạt với Google Gemini 1.5 Flash hoặc Groq thông qua kiến trúc Provider-Agnostic, đáp ứng tiêu chuẩn Well-Architected về tối ưu chi phí (Cost Optimization).

---

# 2. Vấn đề và Giải pháp (Problem Statement & Solution)

## 2.1. Vấn đề thực tế
1. **Hạn chế của RAG truyền thống (Naive RAG)**: Các hệ thống RAG cơ bản chỉ đơn thuần tìm kiếm đoạn văn bản tương đồng (similarity search) rồi nạp vào prompt, dẫn tới hiện tượng ảo giác (hallucination), không thể trả lời các câu hỏi phức tạp đòi hỏi suy luận nhiều bước, và hoàn toàn không thể thực thi hành động nghiệp vụ.
2. **Chi phí và rào cản triển khai trên Cloud**: Việc tự host các mô hình LLM lớn hoặc sử dụng các cụm máy chủ chuyên dụng tốn kém chi phí cố định rất lớn, không phù hợp cho các doanh nghiệp vừa và nhỏ hoặc các dự án thử nghiệm ban đầu.
3. **Nguy cơ rò rỉ dữ liệu và thông tin xác thực**: Nhiều ứng dụng AI lưu trữ API key trực tiếp trong biến môi trường hoặc code cục bộ, vi phạm nguyên tắc bảo mật thông tin đám mây.

## 2.2. Giải pháp đề xuất
Dự án xây dựng một hệ thống **Agentic RAG** hoàn chỉnh trên AWS:
- **Khả năng tự suy luận**: Agent tự phân tích câu hỏi người dùng, quyết định xem câu hỏi cần tra cứu tài liệu S3 hay cần kích hoạt tool truy vấn DynamoDB.
- **Bảo mật chuẩn Doanh nghiệp**: API key bên ngoài được mã hóa tập trung trong SSM Parameter Store; mọi truy cập giữa các dịch vụ AWS đều tuân thủ nguyên tắc quyền tối thiểu (IAM Least Privilege).
- **Tối ưu chi phí tuyệt đối**: Tận dụng 100% các dịch vụ Serverless nằm trong hạn mức Free Tier vĩnh viễn của AWS kết hợp API ngoài miễn phí, đảm bảo chi phí gần như 0 đồng.

---

# 3. Sơ đồ kiến trúc giải pháp (Architecture Diagram)

```text
[ Trình Duyệt / Người Dùng ]
             │
             ▼ HTTPS (SSL/TLS)
[ Amazon CloudFront + Amazon S3 Static Hosting ] (Giao diện Web Chat)
             │
             ▼ REST API Request
[ Amazon API Gateway ] (Cổng tiếp nhận API, xác thực và điều tiết lưu lượng)
             │
             ▼ Kích hoạt xử lý
[ AWS Lambda: Agent Controller Core ]
     │
     ├── 1. Lấy API Key an toàn ──────> [ AWS SSM Parameter Store (SecureString) ]
     │
     ├── 2. Quản lý bộ nhớ phiên ────> [ Amazon DynamoDB: Chat History & Sessions ]
     │
     ├── 3. Điều phối công cụ (Tools Execution):
     │      │
     │      ├── Tool 1: Tra cứu tri thức ──> [ Amazon S3 + Vector Store (FAISS) ]
     │      └── Tool 2: Tra cứu vé/đơn hàng ─> [ Amazon DynamoDB (Business Table) ]
     │
     └── 4. Gửi Prompt & Ngữ cảnh ────> [ External LLM: Google Gemini / Groq ]
             │
             ▼
[ Amazon CloudWatch ] (Ghi log toàn bộ phiên xử lý, đo lường độ trễ và số lượng token)
```

---

# 4. Danh mục dịch vụ AWS sử dụng

| Dịch vụ AWS | Vai trò trong hệ thống | Lý do lựa chọn |
| :--- | :--- | :--- |
| **AWS Lambda** | Trung tâm điều phối logic Agent (Compute Engine) | Phi máy chủ, tự động co giãn, miễn phí 1 triệu request/tháng |
| **Amazon S3** | Lưu trữ tài liệu tri thức (Raw Documents) & Web tĩnh | Bền vững 99.999999999%, tích hợp dễ dàng với Vector Index |
| **Amazon DynamoDB** | Lưu trữ lịch sử hội thoại và bảng dữ liệu nghiệp vụ | Tốc độ mili-giây, hỗ trợ tính năng TTL tự động dọn dẹp bộ nhớ |
| **AWS Systems Manager** | Quản lý API Key bảo mật (Parameter Store) | Lưu trữ tham số mã hóa an toàn, không tốn chi phí |
| **Amazon API Gateway** | Tiếp nhận và điều phối request từ frontend | Quản lý endpoint an toàn, hỗ trợ CORS và giới hạn tốc độ gọi |
| **Amazon CloudFront** | Mạng phân phối nội dung (CDN) toàn cầu | Tăng tốc độ tải trang, tích hợp chứng chỉ HTTPS miễn phí từ ACM |
| **Amazon CloudWatch** | Giám sát, ghi log và cảnh báo hệ thống | Theo dõi lỗi 4xx/5xx và đo lường hiệu năng hoạt động của Lambda |

---

# 5. Kế hoạch triển khai (Implementation Roadmap)

- **Tuần 1 - 2**: Thiết lập môi trường AWS, cấu hình IAM User, AWS Budgets và AWS CLI.
- **Tuần 3 - 4**: Xây dựng kho lưu trữ tri thức S3, tạo bảng DynamoDB cho session chat và bảng nghiệp vụ mẫu.
- **Tuần 5 - 6**: Cấu hình AWS Systems Manager Parameter Store và phát triển các hàm Lambda đóng vai trò Tools.
- **Tuần 7 - 8**: Hoàn thiện Engine điều phối ReAct Agent trên Lambda, kết nối API Gateway.
- **Tuần 9 - 10**: Xây dựng giao diện Web Chat, triển khai lên S3 + CloudFront và tích hợp CloudWatch Monitoring.
- **Tuần 11 - 12**: Kiểm thử toàn diện các kịch bản thực tế, viết tài liệu hướng dẫn và hoàn thiện báo cáo thực tập.