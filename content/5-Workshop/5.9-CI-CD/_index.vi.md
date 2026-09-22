---
title: "Tự động hóa hướng sự kiện"
date: 2026-09-23
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

### Mục tiêu chuyên đề

Thiết lập quy trình tự động hóa phi máy chủ hướng sự kiện (Event-Driven Serverless Pipeline) trên AWS, kết nối **Amazon S3 Event Notification** trực tiếp tới hàm **AWS Lambda** (`huylam-ocr-processor`) để tự động khởi tạo tiến trình xử lý tài liệu trong **Amazon DynamoDB** ngay khi có tệp mới được tải lên.

---

## 1. Tổng quan kiến trúc Event-Driven Serverless

Thay vì bắt máy chủ ứng dụng phải liên tục thăm dò (polling) kho lưu trữ S3 gây lãng phí tài nguyên và độ trễ cao, hệ thống áp dụng mô hình hướng sự kiện thời gian thực (Real-time Event-Driven):

```text
[ Tải tệp lên S3 uploads/ ] ──> [ Sự kiện s3:ObjectCreated:* ] ──> [ AWS Lambda (huylam-ocr-processor) ]
                                                                             │
                                                                             ├──> [ Ghi bản ghi vào DynamoDB ]
                                                                             │
                                                                             └──> [ Ghi nhật ký CloudWatch Logs ]
```

Ưu điểm nổi bật:
- **Phản hồi tức thì (Sub-second Ingestion)**: Thời gian từ khi tệp tải lên hoàn tất đến khi Lambda tiếp nhận và tạo bản ghi tiến trình chỉ từ **214 ms đến 257 ms**.
- **Tự động co giãn theo nhu cầu (Scale-to-Zero)**: Hệ thống tự động kích hoạt số lượng hàm Lambda tương ứng với số lượng tệp tải lên đồng thời và hoàn toàn không tốn tài nguyên khi không có yêu cầu.
- **Chi phí 0.00 USD**: Nằm trọn vẹn trong hạn mức 1,000,000 lượt gọi Lambda miễn phí mỗi tháng của AWS Free Tier.

---

## 2. Nội dung các bước thực hành

Chuyên đề này gồm phần thực hành chi tiết:

- **[5.9.1 Thiết lập S3 Event Notification kích hoạt AWS Lambda](5.9.1-configure-event-driven-pipeline/)**: Hướng dẫn tạo IAM Role cho Lambda, triển khai mã nguồn Python 3.11, cấp quyền gọi Resource-based Policy và cấu hình Event Notification trên S3 bucket.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn sẽ có:
- Hàm AWS Lambda `huylam-ocr-processor` triển khai thành công tại khu vực `ap-southeast-1`.
- Sự kiện `NewDocumentUploadTrigger` trên S3 bucket `huylam-ocr-documents-ap-southeast-1` tự động kích hoạt Lambda khi có tệp trong `uploads/`.
- Bản ghi tiến trình được tạo tự động trong bảng DynamoDB `document_processing_jobs` với trạng thái `RECEIVED_VIA_S3_EVENT`.
- Nhật ký thực thi được theo dõi minh bạch qua Amazon CloudWatch Logs.