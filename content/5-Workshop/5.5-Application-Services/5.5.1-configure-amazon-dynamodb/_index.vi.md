---
title: "Cấu hình Amazon DynamoDB"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

### Mục tiêu thực hành

Khởi tạo và cấu hình bảng cơ sở dữ liệu NoSQL Serverless **Amazon DynamoDB** mang tên `document_processing_jobs` tại khu vực `ap-southeast-1` (Singapore) ở chế độ cước On-Demand (`PAY_PER_REQUEST`) để quản lý tiến trình bóc tách và dịch thuật tài liệu.

---

## 1. Tổng quan bảng dữ liệu document_processing_jobs

Amazon DynamoDB là dịch vụ cơ sở dữ liệu khóa-giá trị (Key-Value) và tài liệu NoSQL được quản lý hoàn toàn, cung cấp hiệu năng dưới 10 mili-giây ở mọi quy mô:
* **Khóa chính kết hợp (Composite Primary Key)**:
  * **Khóa phân vùng (Partition Key)**: `job_id` (Kiểu String) - Mã định danh duy nhất của mỗi tác vụ xử lý tài liệu.
  * **Khóa sắp xếp (Sort Key)**: `created_at` (Kiểu String - chuẩn ISO 8601 UTC) - Dùng để truy vấn lịch sử tác vụ theo thời gian.
* **Chế độ tính cước On-Demand**: Tự động co giãn theo số lượng yêu cầu đọc/ghi thực tế, loại bỏ hoàn toàn chi phí cơ sở hạ tầng khi hệ thống ở trạng thái nhàn rỗi.

---

## 2. Các bước tạo bảng trên AWS Management Console

### Bước 2.1: Tạo bảng mới
1. Đăng nhập vào AWS Console, chuyển sang khu vực **ap-southeast-1 (Singapore)**.
2. Tìm kiếm và truy cập dịch vụ: **DynamoDB -> Tables -> Create table**.
3. Cấu hình các thông số cơ bản:

| Thuộc tính (Attribute) | Giá trị cấu hình (Configured Value) | Ý nghĩa kỹ thuật |
| :--- | :--- | :--- |
| **Table name** | `document_processing_jobs` | Tên bảng quản lý tiến trình xử lý tài liệu |
| **Partition key** | `job_id` (String) | Định danh duy nhất cho mỗi tài liệu được nạp vào hệ thống |
| **Sort key** | `created_at` (String) | Dấu thời gian khởi tạo tác vụ (ISO 8601) |
| **Table class** | DynamoDB Standard | Tối ưu hóa cho các tác vụ truy xuất dữ liệu thông thường |

---

### Bước 2.2: Cấu hình chế độ công suất (Capacity Mode)
1. Tại mục **Table settings**, chọn **Customize settings**.
2. Tại mục **Read/write capacity settings**, chọn **On-demand**:
   * Chế độ On-demand tự động thích ứng với lưu lượng tải biến thiên đột ngột mà không cần lập kế hoạch công suất trước.
   * Rất phù hợp với bài toán bóc tách tài liệu và tối ưu chi phí FinOps (0 USD khi không có yêu cầu xử lý).
3. Tại mục **Encryption at rest**, giữ mặc định: **Amazon DynamoDB owned key**.
4. Nhấp nút **Create table**.

---

## 3. Kiểm tra trạng thái và Lược đồ dữ liệu (Item Schema)

Đợi khoảng 10 - 20 giây cho đến khi bảng chuyển sang trạng thái **Active**.

ARN của bảng sẽ có định dạng:
```text
arn:aws:dynamodb:ap-southeast-1:677994024390:table/document_processing_jobs
```

![Bảng Amazon DynamoDB document_processing_jobs ở trạng thái Active](/images/week10/04-dynamodb-table-active-overview.png)

### Lược đồ dữ liệu bản ghi mẫu (Item Schema):
Mỗi khi tài liệu được bóc tách và dịch thuật, một bản ghi tiến trình sẽ được tự động lưu vào bảng với cấu trúc JSON:

```json
{
  "job_id": "auto-1e4a3832",
  "created_at": "2026-09-21T14:38:21Z",
  "status": "COMPLETED",
  "filename": "cv.pdf",
  "file_size": 182405,
  "page_count": 1,
  "ocr_mode": "FAST_PATH",
  "target_language": "vi",
  "s3_input_uri": "s3://huylam-ocr-documents-ap-southeast-1/uploads/test-event/cv.pdf",
  "s3_output_md_uri": "s3://huylam-ocr-documents-ap-southeast-1/outputs/auto-1e4a3832/cv.pdf.md",
  "duration_ms": 310
}
```

![Bảng DynamoDB document_processing_jobs lưu trữ bản ghi thực tế](/images/week11/13-dynamodb-items-received-s3-event.png)

---

## 4. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Bảng Amazon DynamoDB `document_processing_jobs` hoạt động ở trạng thái **Active**.
- Chế độ On-Demand đảm bảo chi phí 0.00 USD khi không phát sinh lưu lượng.
- Sẵn sàng tiếp nhận bản ghi từ máy chủ Web Studio EC2 và luồng sự kiện AWS Lambda.