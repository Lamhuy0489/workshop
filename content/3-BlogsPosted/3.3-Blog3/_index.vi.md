---
title: "Blog 3: Tự động hóa phi máy chủ hướng sự kiện (Serverless Event-Driven)"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Xây dựng quy trình tự động hóa phi máy chủ hướng sự kiện trên AWS: Từ S3 Event đến DynamoDB trong 214 ms

> [!NOTE] Bài viết đã công bố trực tuyến trên LinkedIn
> * **Tác giả**: Lâm Quang Huy (MSSV: `0212267` - Trường Đại học Xây dựng Hà Nội)
> * **Chương trình**: AWS First Cloud AI Journey (FCAJ) Bootcamp 2026
> * **Liên kết bài đăng LinkedIn**: [https://lnkd.in/p/gBfaVCdj](https://lnkd.in/p/gBfaVCdj)
> * **Mã nguồn dự án**: [Lamhuy0489/aws](https://github.com/Lamhuy0489/aws)

---

## 1. Bối cảnh & Sự chuyển dịch sang kiến trúc hướng sự kiện (Event-Driven)

Trong thiết kế hệ thống hiện đại, việc chuyển đổi từ mô hình xử lý đồng bộ (Synchronous Request-Response) sang **kiến trúc hướng sự kiện phi máy chủ (Event-Driven Serverless)** là chìa khóa để đạt được khả năng mở rộng quy mô tức thì và loại bỏ hoàn toàn việc lãng phí tài nguyên nhàn rỗi.

Khi người dùng tải lên một tài liệu kỹ thuật lớn, việc giữ kết nối HTTP chờ đợi máy chủ xử lý sẽ gây nghẽn kết nối của Web Server và dễ dẫn tới lỗi Gateway Timeout (`504`). Để khắc phục, hệ thống đã xây dựng một đường ống tự động hóa phi máy chủ phi tập trung: Người dùng nhận phản hồi tức thì với mã theo dõi `job_id`, trong khi toàn bộ quy trình tiếp nhận, phân loại và ghi nhận trạng thái được điều phối tự động bởi các sự kiện trên AWS.

---

## 2. Mổ xẻ luồng tự động hóa phi máy chủ (End-to-End Pipeline)

Kiến trúc liên kết chặt chẽ giữa các dịch vụ phi máy chủ cốt lõi của AWS:

![Sơ đồ đường ống xử lý phi máy chủ hướng sự kiện trên AWS](/images/architecture/aws-serverless-event-pipeline.png?width=100%&classes=border,shadow)

> [!NOTE] Định dạng tệp sơ đồ kiến trúc
> * **Ảnh kết xuất độ nét cao**: `/images/architecture/aws-serverless-event-pipeline.png` (Chuẩn Retina 1360x780)
> * **Sơ đồ đồ họa Vector SVG**: `/images/architecture/aws-serverless-event-pipeline.svg`
> * **Tệp thiết kế nguồn Draw.io**: `/images/architecture/aws-serverless-event-pipeline.drawio`

### 2.1. Tiếp nhận tài liệu an toàn vào Amazon S3
- Khi người dùng tải tệp từ giao diện Web Studio, ứng dụng sử dụng quyền hạn của IAM Instance Profile đẩy tệp vào thư mục `uploads/` của S3 bucket `huylam-ocr-documents-ap-southeast-1`.
- Dữ liệu được mã hóa tự động ở tầng nghỉ bằng thuật toán Server-Side Encryption (SSE-S3 AES-256).

### 2.2. Kích hoạt sự kiện S3 Event Notification không độ trễ
- Bucket S3 được cấu hình bộ lọc sự kiện:
  ```json
  {
    "Events": ["s3:ObjectCreated:*"],
    "Filter": {
      "Key": {
        "FilterRules": [
          { "Name": "prefix", "Value": "uploads/" }
        ]
      }
    }
  }
  ```
- Ngay khi tệp được đẩy lên thành công, S3 phát tín hiệu kích hoạt hàm AWS Lambda `huylam-ocr-processor`.
- **Ưu điểm vượt trội**: Không cần duy trì bất kỳ tiến trình nền nào để quét đĩa (polling), giải phóng hoàn toàn tài nguyên tính toán của máy chủ EC2.

### 2.3. Khởi tạo tác vụ và ghi nhận vào Amazon DynamoDB (214 ms)
- Hàm AWS Lambda (viết bằng Python 3.11 Serverless) bóc tách metadata từ sự kiện, tạo mã định danh duy nhất `job_id`, và thực hiện lệnh `PutItem` vào bảng Amazon DynamoDB `document_processing_jobs`:
  * **Partition Key (PK)**: `job_id` (UUIDv4)
  * **Sort Key (SK)**: `created_at` (Dấu thời gian chuẩn ISO 8601)
  * **Status**: `RECEIVED_VIA_S3_EVENT`
  * **File Metadata**: Dung lượng, tên tệp gốc, đường dẫn S3 bucket
- **Thời gian thực thi**: Toàn bộ chu trình từ lúc phát sinh sự kiện đến khi hoàn tất ghi nhận vào cơ sở dữ liệu NoSQL chỉ mất **214 mili-giây**.

### 2.4. Khả năng quan sát tập trung với Amazon CloudWatch
- Toàn bộ nhật ký thực thi của hàm Lambda được tự động lưu trữ tại CloudWatch Log Group `/aws/lambda/huylam-ocr-processor`.
- Thiết lập số đo thống kê thời gian phản hồi, số lần kích hoạt và cảnh báo tức thì khi xuất hiện lỗi logic (Invocations Errors > 0).

---

## 3. Đóng gói Container OCI & Đẩy lên Amazon ECR

Song song với tầng xử lý phi máy chủ, ứng dụng Web Studio được đóng gói theo chuẩn OCI Container:
- Xây dựng Dockerfile tối ưu dung lượng trên nền tảng `python:3.11-slim`.
- Đăng nhập xác thực và đẩy ảnh container lên kho lưu trữ riêng tư **Amazon Elastic Container Registry (Amazon ECR)** tại `huylam-web-app`.
- Máy chủ EC2 kéo ảnh từ ECR và vận hành dưới dạng dịch vụ nền tảng `huylam-ocr.service` được quản lý bởi systemd daemon.

---

## 4. Sức mạnh của kiến trúc Scale-to-Zero & Bài học kinh nghiệm

1. **Tối ưu chi phí tuyệt đối (Zero Idle Cost)**: Khi không có tài liệu được tải lên, toàn bộ hạ tầng S3, Lambda và DynamoDB ở trạng thái ngủ hoàn toàn với chi phí đúng **0.00 USD**.
2. **Khả năng co giãn tức thì (Elastic Scalability)**: Khi có hàng trăm người dùng gửi tài liệu cùng lúc, AWS Lambda tự động nhân bản song song các phiên thực thi mà không gặp phải giới hạn tài nguyên của một máy chủ truyền thống.
3. **Bài học đúc kết**: Thiết kế hệ thống đám mây theo hướng sự kiện (Event-Driven) mang lại tính linh hoạt cao độ, giúp phân tách các tầng nghiệp vụ (decoupling) và tối đa hóa tính sẵn sàng của toàn hệ thống.