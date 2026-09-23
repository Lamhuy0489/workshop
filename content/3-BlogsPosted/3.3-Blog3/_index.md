---
title: "Blog 3: Event-Driven Serverless Pipeline Engineering"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Engineering an Event-Driven Serverless Pipeline on AWS: S3 to DynamoDB in 214 ms

> [!NOTE] Published Live on LinkedIn
> * **Author**: Lam Quang Huy (Student ID: `0212267` - Hanoi University of Civil Engineering)
> * **Program**: AWS First Cloud AI Journey (FCAJ) Bootcamp 2026
> * **LinkedIn Post Link**: [https://lnkd.in/p/gBfaVCdj](https://lnkd.in/p/gBfaVCdj)
> * **Project Repository**: [Lamhuy0489/aws](https://github.com/Lamhuy0489/aws)

---

## 1. Context & The Shift Toward Event-Driven Architectures

In contemporary cloud system design, transitioning from synchronous request-response models to **event-driven serverless architectures** is the definitive strategy for achieving near-instantaneous horizontal elasticity while eliminating idle compute expenses.

For large document uploads, holding HTTP socket connections open until full processing completes risks server exhaustion and client-side Gateway Timeout (`HTTP 504`) failures. The platform mitigates this through asynchronous decoupling: Users immediately receive a tracking `job_id`, while the backend ingestion and state registration execute autonomously across AWS managed services.

---

## 2. End-to-End Serverless Pipeline Walkthrough

The pipeline seamlessly links foundational AWS serverless primitives:

![Event-Driven Serverless Pipeline Architecture on AWS](/images/architecture/aws-serverless-event-pipeline.png?width=100%&classes=border,shadow)

> [!NOTE] Architecture Diagram File Formats
> * **High-Resolution Render**: `/images/architecture/aws-serverless-event-pipeline.png` (Retina 1360x780)
> * **Scalable Vector Graphic**: `/images/architecture/aws-serverless-event-pipeline.svg`
> * **Source Diagram File**: `/images/architecture/aws-serverless-event-pipeline.drawio`

### 2.1. Secure Storage Ingestion via Amazon S3
- Web Studio uploads files to the `uploads/` prefix of S3 bucket `huylam-ocr-documents-ap-southeast-1` via STS temporary credentials issued to the EC2 host IAM role.
- All stored assets are automatically protected at rest using Server-Side Encryption (SSE-S3 AES-256).

### 2.2. Zero-Latency Event Dispatch via S3 Event Notifications
- S3 is configured with an active event notification rule:
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
- Instantaneously upon object creation, S3 directly triggers AWS Lambda function `huylam-ocr-processor`.
- **Core Benefit**: Completely eliminates polling loops and background cron workers, conserving server compute.

### 2.3. Autonomous Orchestration & DynamoDB Persistence (214 ms)
- The Python 3.11 serverless Lambda function extracts event metadata, generates a canonical `job_id`, and writes an item into Amazon DynamoDB table `document_processing_jobs`:
  * **Partition Key (PK)**: `job_id` (UUIDv4)
  * **Sort Key (SK)**: `created_at` (ISO 8601 Timestamp)
  * **Status**: `RECEIVED_VIA_S3_EVENT`
  * **File Metadata**: File size, original filename, and S3 object URI
- **Execution Performance**: Total duration from S3 notification dispatch to DynamoDB record persistence averages **214 milliseconds**.

### 2.4. Centralized Observability with Amazon CloudWatch
- Execution logs and telemetry stream directly into CloudWatch Log Group `/aws/lambda/huylam-ocr-processor`.
- Configured metrics trace execution durations, concurrent invocations, and trigger immediate alarms upon any uncaught errors (`Errors > 0`).

---

## 3. Containerization & Publishing to Amazon ECR

Complementing the serverless event layer, the Web Studio frontend and API server are containerized:
- Built a streamlined OCI container using `python:3.11-slim`.
- Authenticated and pushed to private **Amazon Elastic Container Registry (Amazon ECR)** repository `huylam-web-app`.
- EC2 hosts run the containerized workload managed via a persistent systemd daemon `huylam-ocr.service`.

---

## 4. Operational Takeaways & Scale-to-Zero Capabilities

1. **True Scale-to-Zero Economics**: During idle periods, the ingestion pipeline incurs exactly **$0.00** in compute charges.
2. **Elastic Scalability**: Simultaneous uploads automatically scale Lambda execution environments horizontally without server-side thread saturation.
3. **Key Lesson**: Event-driven decoupling fundamentally isolates components, maximizing system resilience and fault tolerance.