---
title: "Event-Driven Automation"
date: 2026-09-23
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

### Module Objective

Establish an Event-Driven Serverless Pipeline on AWS by connecting **Amazon S3 Event Notifications** directly into an **AWS Lambda** function (`huylam-ocr-processor`), automatically provisioning document processing records in **Amazon DynamoDB** upon document upload.

---

## 1. Event-Driven Architecture Overview

Rather than requiring host servers to perform periodic polling loops against S3 buckets, the system employs an asynchronous real-time event pipeline:

```text
[ Document Upload to S3 uploads/ ] ──> [ Event s3:ObjectCreated:* ] ──> [ AWS Lambda (huylam-ocr-processor) ]
                                                                                   │
                                                                                   ├──> [ Persist Item to DynamoDB ]
                                                                                   │
                                                                                   └──> [ Telemetry to CloudWatch ]
```

Architectural Benefits:
- **Instantaneous Sub-Second Ingestion**: Elapsed duration from S3 upload completion to DynamoDB item persistence measures between **214 ms and 257 ms**.
- **Dynamic Scale-to-Zero**: Computes only when events fire, incurring zero idle infrastructure overhead.
- **Strict $0.00 Cost Alignment**: Consumes a negligible fraction of the 1,000,000 monthly free invocations granted under the AWS Free Tier.

---

## 2. Hands-on Execution Steps

This module comprises the following practical section:

- **[5.9.1 Establishing S3 Event Notification to AWS Lambda](5.9.1-configure-event-driven-pipeline/)**: Creating the Lambda IAM Role, deploying Python 3.11 logic, configuring resource-based invoke permissions, and activating S3 event notifications.

---

## 3. Expected Outcomes

Upon completing this module, you have:
- An active AWS Lambda function `huylam-ocr-processor` running Python 3.11 in `ap-southeast-1`.
- S3 Event Notification `NewDocumentUploadTrigger` bound to prefix `uploads/`.
- Automated state creation inside DynamoDB `document_processing_jobs` with status `RECEIVED_VIA_S3_EVENT`.
- Full telemetry streamed to Amazon CloudWatch Logs.