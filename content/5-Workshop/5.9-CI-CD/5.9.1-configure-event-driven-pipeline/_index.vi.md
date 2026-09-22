---
title: "Cấu hình S3 Event Notification và AWS Lambda"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.9.1. </b> "
---

### Mục tiêu thực hành

Xây dựng quy trình tự động hóa hoàn toàn phi máy chủ (Serverless Event-Driven Pipeline): Tạo IAM Execution Role, triển khai hàm AWS Lambda `huylam-ocr-processor` chạy Python 3.11 và cấu hình Amazon S3 Event Notification để tự động khởi tạo bản ghi tiến trình trong Amazon DynamoDB khi có tài liệu mới tải lên thư mục `uploads/`.

---

## 1. Tạo IAM Execution Role cho Lambda (huylam-ocr-lambda-role)

Để hàm Lambda có thể ghi nhật ký lên CloudWatch và ghi dữ liệu vào bảng DynamoDB:
1. Truy cập **IAM Console -> Roles -> Create role**.
2. **Trusted entity type**: Chọn **AWS service**, Use case: **Lambda**.
3. **Permissions policies**:
   * Đính kèm chính sách: **`AWSLambdaBasicExecutionRole`** (Cung cấp quyền tạo nhóm nhật ký và ghi log lên CloudWatch).
4. **Role name**: Nhập `huylam-ocr-lambda-role`.
5. Nhấp **Create role**.
6. Sau khi tạo, mở `huylam-ocr-lambda-role`, tại mục **Permissions policies** nhấp **Add permissions -> Create inline policy**:
   * Chuyển sang thẻ **JSON** và dán chính sách sau:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3AndDynamoDBAccess",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem"
            ],
            "Resource": [
                "arn:aws:s3:::huylam-ocr-documents-ap-southeast-1/*",
                "arn:aws:dynamodb:ap-southeast-1:677994024390:table/document_processing_jobs"
            ]
        }
    ]
}
```

7. Đặt tên chính sách: `LambdaS3DynamoDBAccess` và nhấp **Create policy**.

---

## 2. Khởi tạo và Triển khai AWS Lambda (huylam-ocr-processor)

1. Truy cập **Lambda Console -> Functions -> Create function**.
2. Chọn **Author from scratch**:
   * **Function name**: `huylam-ocr-processor`.
   * **Runtime**: Chọn **Python 3.11**.
   * **Architecture**: `x86_64`.
   * **Change default execution role**: Chọn **Use an existing role** và chọn `huylam-ocr-lambda-role`.
3. Nhấp **Create function**.

---

### Bước 2.2: Triển khai mã nguồn xử lý sự kiện
Tại thẻ **Code**, mở tệp `lambda_function.py` và dán toàn bộ mã nguồn xử lý:

```python
import json
import os
import uuid
import logging
import urllib.parse
from datetime import datetime, timezone
import boto3

logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s")
logger = logging.getLogger(__name__)

dynamodb = boto3.resource("dynamodb")
s3_client = boto3.client("s3")

DYNAMODB_TABLE = os.getenv("DYNAMODB_TABLE", "document_processing_jobs")

def lambda_handler(event, context):
    logger.info("Nhan su kien moi tu Amazon S3 Event Notification: %s", json.dumps(event))

    records = event.get("Records", [])
    if not records:
        logger.warning("Khong tim thay Records trong event payload.")
        return {"statusCode": 400, "body": json.dumps({"error": "No records found"})}

    results = []
    table = dynamodb.Table(DYNAMODB_TABLE)

    for record in records:
        s3_data = record.get("s3", {})
        bucket_name = s3_data.get("bucket", {}).get("name", "")
        raw_key = s3_data.get("object", {}).get("key", "")
        object_key = urllib.parse.unquote_plus(raw_key)
        object_size = s3_data.get("object", {}).get("size", 0)

        logger.info("Phat hien tep moi: s3://%s/%s (%d bytes)", bucket_name, object_key, object_size)

        parts = object_key.split("/")
        filename = parts[-1] if parts else "document.pdf"

        if len(parts) >= 3 and parts[0] == "uploads":
            job_id = parts[1]
        else:
            job_id = f"auto-{uuid.uuid4().hex[:8]}"

        now_iso = datetime.now(timezone.utc).isoformat()
        item = {
            "job_id": job_id,
            "created_at": now_iso,
            "filename": filename,
            "user_id": "s3-event-auto",
            "username": "automated-pipeline",
            "status": "RECEIVED_VIA_S3_EVENT",
            "s3_input_uri": f"s3://{bucket_name}/{object_key}",
            "s3_output_md_uri": f"s3://{bucket_name}/outputs/{job_id}/{filename}.md",
            "model_used": "AWS S3 Event Trigger (Serverless)",
            "file_size_bytes": object_size,
            "processing_time_seconds": "0.05"
        }

        try:
            table.put_item(Item=item)
            logger.info("Da luu tien trinh vao DynamoDB (job_id: %s)", job_id)
            results.append({"job_id": job_id, "status": "RECORDED", "key": object_key})
        except Exception as e:
            logger.error("Loi khi ghi vao DynamoDB: %s", str(e))
            results.append({"job_id": job_id, "status": "ERROR", "error": str(e)})

    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps({"message": "Xu ly su kien S3 hoan tat", "results": results})
    }
```

4. Nhấp nút **Deploy** để xuất bản phiên bản mã nguồn mới.

---

## 3. Cấu hình Amazon S3 Event Notification (NewDocumentUploadTrigger)

1. Truy cập **Amazon S3 -> Buckets -> huylam-ocr-documents-ap-southeast-1**.
2. Chuyển sang thẻ **Properties**, cuộn xuống mục **Event notifications** và nhấp **Create event notification**.
3. Cấu hình thông số:
   * **Event name**: `NewDocumentUploadTrigger`.
   * **Prefix**: `uploads/`.
   * **Event types**: Đánh dấu chọn **All object create events** (`s3:ObjectCreated:*`).
   * **Destination**: Chọn **Lambda function**.
   * **Specify Lambda function**: Chọn **`huylam-ocr-processor`**.
4. Nhấp **Save changes**.

AWS S3 sẽ tự động cấu hình chính sách quyền hạn (Resource-based Policy) cho phép S3 kích hoạt hàm Lambda.

---

## 4. Kiểm tra luồng tự động hóa thực tế

1. Tải một tệp PDF bất kỳ vào thư mục `uploads/` trên giao diện S3 Console.
2. Kiểm tra **Amazon DynamoDB -> Tables -> document_processing_jobs -> Explore items**:
   * Một bản ghi mới xuất hiện ngay lập tức với trạng thái `RECEIVED_VIA_S3_EVENT`.
3. Kiểm tra **Amazon CloudWatch -> Log groups -> /aws/lambda/huylam-ocr-processor**:
   * Nhật ký thực thi ghi nhận thời gian chạy chỉ **214 ms** và bộ nhớ tiêu thụ chỉ **88 MB**.

---

## 5. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Kiến trúc Serverless Event-Driven hoàn toàn tự động hóa.
- Mỗi tệp tải lên S3 được tiếp nhận phi đồng bộ và ghi nhận tiến trình trong chưa đầy 0.3 giây.
- Tiết kiệm 100% chi phí tính toán cho tác vụ tiếp nhận ban đầu.