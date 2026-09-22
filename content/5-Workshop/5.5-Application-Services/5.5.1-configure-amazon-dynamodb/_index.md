---
title: "Configure Amazon DynamoDB"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

### Hands-on Objective

Provision and configure a serverless NoSQL database table on **Amazon DynamoDB** named `document_processing_jobs` in the `ap-southeast-1` (Singapore) region operating in On-Demand (`PAY_PER_REQUEST`) billing mode to record document parsing and translation job states.

---

## 1. Table Architecture Overview

Amazon DynamoDB is a fully managed, serverless key-value and document NoSQL database delivering consistent single-digit millisecond latency at any scale:
* **Composite Primary Key**:
  * **Partition Key**: `job_id` (String) - Unique identifier assigned to each document ingestion job.
  * **Sort Key**: `created_at` (String - ISO 8601 UTC timestamp) - Enables chronological querying and auditing.
* **On-Demand Capacity Mode**: Dynamically scales to accommodate unpredictable traffic spikes with zero capacity pre-provisioning, incurring $0.00 infrastructure costs during idle periods.

---

## 2. AWS Management Console Step-by-Step Procedure

### Step 2.1: Provision Table
1. Sign in to the AWS Console in region **ap-southeast-1 (Singapore)**.
2. Navigate to: **DynamoDB -> Tables -> Create table**.
3. Configure the primary table properties:

| Property | Configured Value | Architectural Role |
| :--- | :--- | :--- |
| **Table name** | `document_processing_jobs` | Document processing jobs audit registry |
| **Partition key** | `job_id` (String) | Unique identifier for each ingested document |
| **Sort key** | `created_at` (String) | Creation timestamp (ISO 8601 format) |
| **Table class** | DynamoDB Standard | Optimized for general-purpose transactional workloads |

---

### Step 2.2: Configure Capacity Mode
1. Under **Table settings**, select **Customize settings**.
2. Under **Read/write capacity settings**, select **On-demand**:
   * Dynamically handles instantaneous burst traffic without rate-limiting.
   * Aligns with FinOps cost optimization principles (zero cost when idle).
3. Under **Encryption at rest**, retain default: **Amazon DynamoDB owned key**.
4. Click **Create table**.

---

## 3. Verifying Table Status & Document Schema

Allow 10 to 20 seconds for the table state to become **Active**.

Table ARN conforms to:
```text
arn:aws:dynamodb:ap-southeast-1:677994024390:table/document_processing_jobs
```

![Amazon DynamoDB document_processing_jobs Table in Active State](/images/week10/04-dynamodb-table-active-overview.png)

### Document Schema Example:
When a document is parsed and translated, a state record is written automatically:

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

![Amazon DynamoDB document_processing_jobs Table Items](/images/week11/13-dynamodb-items-received-s3-event.png)

---

## 4. Expected Outcomes

Upon completing this section:
- Amazon DynamoDB table `document_processing_jobs` is **Active**.
- On-Demand billing ensures $0.00 cost during idle intervals.
- The table is ready to receive state payloads from EC2 and AWS Lambda.