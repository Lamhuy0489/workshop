---
title: "Configure S3 Event Notification and AWS Lambda"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.9.1. </b> "
---

### Practical Objectives

Build a fully automated Serverless Event-Driven Pipeline: Create an IAM Execution Role, deploy AWS Lambda function `huylam-ocr-processor` running Python 3.11, and configure Amazon S3 Event Notification to automatically initiate job records in Amazon DynamoDB whenever a new document is uploaded to the `uploads/` prefix.

---

## 1. Create IAM Execution Role for Lambda (huylam-ocr-lambda-role)

To allow the Lambda function to stream execution logs to Amazon CloudWatch and persist job status items into Amazon DynamoDB:
1. Navigate to **IAM Console -> Roles -> Create role**.
2. **Trusted entity type**: Select **AWS service**, Use case: **Lambda**.
3. **Permissions policies**:
   * Attach policy: **`AWSLambdaBasicExecutionRole`** (Grants permissions to create log groups and write stream events to CloudWatch).
4. **Role name**: Enter `huylam-ocr-lambda-role`.
5. Click **Create role**.
6. After creation, open `huylam-ocr-lambda-role`, navigate to the **Permissions policies** section and click **Add permissions -> Create inline policy**:
   * Switch to the **JSON** tab and paste the following policy:

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

7. Name the policy: `LambdaS3DynamoDBAccess` and click **Create policy**.

![Create IAM Role for Lambda](/images/week11/08-iam-role-lambda-policy-created.png)

---

## 2. Initialize and Deploy AWS Lambda (huylam-ocr-processor)

1. Navigate to **Lambda Console -> Functions -> Create function**.
2. Choose **Author from scratch**:
   * **Function name**: `huylam-ocr-processor`.
   * **Runtime**: Select **Python 3.11**.
   * **Architecture**: `x86_64`.
   * **Change default execution role**: Select **Use an existing role** and select `huylam-ocr-lambda-role`.
3. Click **Create function**.

![Lambda Function Created Successfully](/images/week11/09-lambda-function-created-active.png)

---

### Step 2.2: Deploy Event Processing Logic
Under the **Code** tab, open `lambda_function.py` and insert the event handling implementation:

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
    logger.info("Received event from Amazon S3 Event Notification: %s", json.dumps(event))

    records = event.get("Records", [])
    if not records:
        logger.warning("No Records found in event payload.")
        return {"statusCode": 400, "body": json.dumps({"error": "No records found"})}

    results = []
    table = dynamodb.Table(DYNAMODB_TABLE)

    for record in records:
        s3_data = record.get("s3", {})
        bucket_name = s3_data.get("bucket", {}).get("name", "")
        raw_key = s3_data.get("object", {}).get("key", "")
        object_key = urllib.parse.unquote_plus(raw_key)
        object_size = s3_data.get("object", {}).get("size", 0)

        logger.info("Detected new object: s3://%s/%s (%d bytes)", bucket_name, object_key, object_size)

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
            logger.info("Persisted job metadata to DynamoDB (job_id: %s)", job_id)
            results.append({"job_id": job_id, "status": "RECORDED", "key": object_key})
        except Exception as e:
            logger.error("Error writing to DynamoDB: %s", str(e))
            results.append({"job_id": job_id, "status": "ERROR", "error": str(e)})

    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps({"message": "S3 Event processing complete", "results": results})
    }
```

4. Click **Deploy** to publish the revised function code.

![Lambda Code Deployed Successfully](/images/week11/10-lambda-code-deployed-success.png)

---

## 3. Configure Amazon S3 Event Notification (NewDocumentUploadTrigger)

1. Navigate to **Amazon S3 -> Buckets -> huylam-ocr-documents-ap-southeast-1**.
2. Switch to the **Properties** tab, scroll to **Event notifications**, and click **Create event notification**.
3. Configure the trigger properties:
   * **Event name**: `NewDocumentUploadTrigger`.
   * **Prefix**: `uploads/`.
   * **Event types**: Select **All object create events** (`s3:ObjectCreated:*`).
   * **Destination**: Select **Lambda function**.
   * **Specify Lambda function**: Select **`huylam-ocr-processor`**.
4. Click **Save changes**.

![Amazon S3 Event Notification Created](/images/week11/11-s3-event-notification-created.png)

AWS S3 automatically configures the resource-based invocation policy granting Amazon S3 permission to invoke the Lambda function.

---

## 4. Validate the Automated Pipeline

1. Upload any test PDF document into the `uploads/` prefix via S3 Console or AWS CLI.

![Upload Test Document to S3 uploads](/images/week11/12-s3-upload-test-document.png)

2. Verify in **Amazon DynamoDB -> Tables -> document_processing_jobs -> Explore items**:
   * A new job record appears immediately with status `RECEIVED_VIA_S3_EVENT`.

![DynamoDB Records Created from S3 Event](/images/week11/13-dynamodb-items-received-s3-event.png)

3. Verify in **Amazon CloudWatch -> Log groups -> /aws/lambda/huylam-ocr-processor**:
   * Execution log confirms execution duration of only **214 ms** and memory footprint of **88 MB**.

---

## 5. Expected Result

After completing this lab module:
- A fully automated, asynchronous Serverless Event-Driven pipeline is operational.
- Every document uploaded to S3 is captured and registered in DynamoDB in under 0.3 seconds.
- Compute costs for document ingress ingestion are 100% serverless and zero-idle.