---
title: "Week 9 Worklog"
date: 2026-10-04
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

> [!NOTE] Execution Timeline
> **From 28/09/2026 to 04/10/2026**

### Week 9 Objectives:
* **Strategic Milestone Transition**: Successfully completed 8 weeks of foundational cloud infrastructure training (**Cloud Infrastructure & Container Foundations**), officially commencing Phase 2 - Graduation Capstone Project: **Serverless Hybrid Document OCR & Parsing Platform on AWS**.
* Research and master **Serverless Architecture** and **Event-Driven Architecture** on AWS through the core serverless triad: **AWS Lambda**, **Amazon API Gateway**, and **Amazon DynamoDB**.
* Conduct an architectural comparison between container orchestration (**Amazon ECS Fargate** completed in Week 8) and serverless compute (**AWS Lambda** for the Capstone project): Evaluate trade-offs regarding cost efficiency ($0 during idle), instant horizontal auto-scaling per document, and zero infrastructure maintenance.
* Design the comprehensive Serverless Microservices Architecture for the Hybrid Document Parsing Platform:
  * **Presentation & Ingestion Layer**: Amazon S3 Static Website Hosting integrated with Amazon CloudFront Global CDN and SSL/TLS certificates.
  * **API Interface Layer**: Amazon API Gateway (REST API) providing secure endpoints for document ingestion requests and status polling.
  * **Serverless Compute Layer**: AWS Lambda functions handling fast-path native parsing, S3 Presigned URL generation, and selective OCR dispatching.
  * **Storage & Data Layer**: Amazon S3 for raw documents and exported outputs (`.md`, `.docx`); Amazon DynamoDB for tracking execution state.
  * **Configuration & Security Layer**: AWS Systems Manager (SSM) Parameter Store with AWS KMS encryption for centralized API key management and zero hardcoded secrets.
* Design an optimized NoSQL database schema on **Amazon DynamoDB** for the `document_processing_jobs` table:
  * Define the composite primary key: Partition Key (`job_id`) and Sort Key (`created_at`).
  * Specify tracking attributes: digital page count, scanned page count, per-stage processing latency, and S3 artifact locations.
  * Select On-Demand Capacity mode (`PAY_PER_REQUEST`) to guarantee zero cost during idle periods in compliance with FinOps principles.
* Design an **S3 Presigned URL** workflow: Overcome Amazon API Gateway's rigid 10 MB payload limit, enabling secure, direct-to-S3 uploads for large multi-page PDF documents and high-resolution scanned images.
* Define an IAM execution role (`huylam-lambda-ocr-execution-role`) strictly adhering to the principle of **Least Privilege**.
* Prepare local development tooling and evaluate packaging strategies for heavy Python dependencies (`PyMuPDF`, `boto3`, `pydantic`) compatible with the Amazon Linux 2023 x86_64 Lambda runtime.

---

### Tasks carried out this week:

| Day | Task | Achievement | Reference Material |
| :--- | :--- | :--- | :--- |
| **Monday (28/09/2026)** | - Research Serverless Computing concepts and the AWS Lambda execution model.<br>- Study the Lambda lifecycle (Initialization, Invocation, Shutdown), cold start vs warm start optimization.<br>- Compare cost and latency between Containers (ECS Fargate) and Serverless (Lambda) for event-driven document processing workloads. | Mastered event-driven invocation mechanisms and validated the $0 operational cost advantage of Serverless for asynchronous document parsing. | [AWS Lambda Operator Guide](https://docs.aws.amazon.com/lambda/latest/operatorguide/intro.html) |
| **Tuesday (29/09/2026)** | - Architect the overall Serverless Microservices blueprint for the Capstone project "Serverless Hybrid Document OCR & Parsing Platform on AWS".<br>- Establish the two-stage hybrid processing pipeline: Stage 1 Fast-Path Native Parser for digital text and Stage 2 Selective Vision OCR for scanned pages.<br>- Map service interactions between CloudFront, S3, API Gateway, Lambda, DynamoDB, SSM, and external AI endpoints. | Finalized standardized architectural blueprint, ready for infrastructure and code implementation. | [Serverless Multi-Tier Architecture](https://docs.aws.amazon.com/whitepapers/latest/serverless-multi-tier-architectures-api-gateway-lambda/welcome.html) |
| **Wednesday (30/09/2026)** | - Research Amazon DynamoDB NoSQL database patterns and Single-Table Design concepts.<br>- Design the schema for table `document_processing_jobs` using Partition Key `job_id` (String UUID) and Sort Key `created_at` (String ISO-8601).<br>- Configure On-Demand Capacity mode (`PAY_PER_REQUEST`) to leverage the permanent 25 GB AWS Free Tier tier. | Completed NoSQL schema definition capable of sub-10ms query latency for document progress tracking. | [Amazon DynamoDB Core Components](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html) |
| **Thursday (01/10/2026)** | - Design the IAM execution role `huylam-lambda-ocr-execution-role` for Lambda functions.<br>- Draft fine-grained IAM policy statements: read/write access to S3 (`huylam-ocr-documents-*`), DynamoDB items (`PutItem`, `UpdateItem`, `GetItem`), SSM Parameter Store (`GetParameter` with KMS Decryption), and CloudWatch Logs.<br>- Enforce the principle of Least Privilege without using wildcards or admin policies. | Completed IAM Role specifications, preventing credential leakage and minimizing cloud security risks. | [IAM Policies for Lambda](https://docs.aws.amazon.com/lambda/latest/dg/access-control-identity-based.html) |
| **Friday (02/10/2026)** | - Evaluate packaging and deployment strategies for heavy Python dependencies on AWS Lambda.<br>- Analyze Lambda resource quotas: zipped package limit (50 MB), unzipped limit (250 MB), and ephemeral `/tmp` storage (512 MB - 10 GB).<br>- Test `PyMuPDF` (fitz) integration on Amazon Linux 2023 environments.<br>- Implement character density threshold logic to differentiate digital vs scanned pages automatically. | Validated that the Fast-Path Parser processes digital PDF pages in 0.1 - 0.3s per page, meeting performance requirements. | [Packaging Lambda Functions](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html) |
| **Saturday (03/10/2026)** | - Design the direct upload workflow using Amazon API Gateway and S3 Presigned URLs.<br>- Analyze the 10 MB payload bottleneck of API Gateway when handling large PDF files (15 MB - 100 MB).<br>- Architect a 3-step solution: client requests upload URL from API Gateway -> Lambda returns SigV4 signed S3 Presigned PUT URL -> client uploads binary directly to S3. | Resolved the upload size bottleneck, improved throughput, and eliminated compute load on API Gateway. | [Uploading Objects Using Presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/PresignedUrlUploadObject.html) |
| **Sunday** | - Evaluate the system architecture against the 5 pillars of the AWS Well-Architected Framework (Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization).<br>- Perform FinOps cloud financial audit: ensured all planned services stay strictly within AWS Free Tier limits ($0 expenditure).<br>- Compile Week 9 technical documentation and update project repository. | Fully finalized architectural preparations, laying a solid foundation for infrastructure deployment in Week 10. | [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html) |

---

### Solution Architecture Diagram:

The system is designed following a fully decoupled, Event-Driven Serverless architecture on AWS:

![Solution Architecture Diagram: AWS Serverless Hybrid OCR Platform](/images/architecture/aws-system-architecture.png?width=100%&classes=border,shadow)

> [!NOTE] Architecture Diagram Formats & Resources
> * **High-Resolution PNG Render**: `/images/architecture/aws-system-architecture.png` (Retina 1400x920)
> * **Scalable Vector Graphic (SVG)**: `/images/architecture/aws-system-architecture.svg`
> * **Editable Draw.io Source**: `/images/architecture/aws-system-architecture.drawio` (Directly importable into [diagrams.net](https://app.diagrams.net/) with official AWS4 stencils).
> * **Dedicated Event Flow Diagram**: See also `/images/architecture/aws-serverless-event-pipeline.png` (Covers the asynchronous S3 Event Trigger to AWS Lambda and DynamoDB pipeline).

### Architectural Breakdown: Serverless Solution

| Architecture Layer | Component / Service | Resource Identifier | Core Role & Technical Responsibility |
| :--- | :--- | :--- | :--- |
| **Edge Distribution** | Amazon CloudFront & S3 Web | Global Edge Ingestion | Distributes the Web Studio Upload & Preview SPA with low latency and HTTPS termination. |
| **API Entry Point** | Amazon API Gateway | REST API Gateway | Provides RESTful endpoints for clients to request SigV4 presigned upload URLs. |
| **Secure Ingestion** | AWS Lambda (Presigned Generator) | `huylam-presigned-url-gen` | Generates short-lived (15-min TTL) S3 Presigned PUT URLs without burdening backend compute. |
| **Object Storage** | Amazon S3 Bucket | `huylam-ocr-documents-ap-southeast-1` | Stores raw uploaded files in `uploads/` and generated outputs (`.md`, `.docx`, `.pdf`) in `outputs/{job_id}/`. |
| **Event Orchestration** | S3 Event Notification | `s3:ObjectCreated:*` | Automatically publishes events on upload completion, asynchronously invoking the Lambda processing engine. |
| **Serverless Compute** | AWS Lambda (Hybrid Engine) | `huylam-ocr-processor` | Executes document extraction pipelines with instant concurrency scaling and zero idle cost (Scale-to-Zero). |
| **Fast-Path Parser (Stage 1)** | Fast-Path Native Parser | PyMuPDF / pdfplumber | Directly parses digital PDFs in memory at 0.1s - 0.3s/page with zero API cost. |
| **Vision AI (Stage 2)** | Selective Vision OCR | Kaggle GPU / Google Gemini API | Activated selectively on scanned pages, combining high precision with automated fallback resilience. |
| **State Tracking** | Amazon DynamoDB | `document_processing_jobs` | NoSQL table tracking job lifecycle, page metrics, elapsed latency, and output artifact paths. |
| **Security & Observability** | AWS SSM & CloudWatch | Parameter Store & CloudWatch Logs | Secures configurations/API keys with KMS encryption; centralizes execution logs and runtime alarms. |

---

### Technical Deep-Dive:

#### 1. Architectural Comparison: Container (Week 8) vs Serverless (Week 9):

Transitioning from container orchestration (**Amazon ECS Fargate**) to serverless execution (**AWS Lambda**) delivers substantial advantages for document ingestion and parsing workloads:

| Comparison Metric | Amazon ECS Fargate (Week 8) | AWS Lambda (Week 9 - Capstone Project) | Rationale for Serverless Selection |
| :--- | :--- | :--- | :--- |
| **Billing Model** | Continuous per-second billing based on allocated vCPU and RAM, regardless of actual incoming requests. | Per-millisecond billing only when functions execute. Zero requests = exactly $0 cost. | Maximizes cloud cost efficiency, operating completely within the AWS Free Tier (1 million free invocations/month). |
| **Elastic Scalability** | Step-based scaling via ECS Service Auto Scaling (takes 30-120 seconds to provision new tasks). | Immediate horizontal concurrency scaling per incoming document up to 1,000 concurrent executions. | Handles bursty, unpredictable document uploads without queue starvation. |
| **Operational Overhead** | Requires configuring Clusters, Task Definitions, Services, VPC Subnets, ENIs, and Security Groups. | Zero server management; AWS automatically handles infrastructure patching, OS updates, and capacity provisioning. | Minimal maintenance overhead, allowing full engineering focus on document parsing algorithms. |
| **Invocation Pattern** | Continuously listening HTTP daemon (Apache `httpd:latest` port 80). | Event-Driven: Automatically triggered by `s3:ObjectCreated` events or API Gateway calls. | Perfectly matches the asynchronous pipeline nature of the document processing platform. |

#### 2. Amazon DynamoDB NoSQL Schema Design:

The `document_processing_jobs` table manages the end-to-end lifecycle of uploaded documents with single-digit millisecond latency:

* **Table Name**: `document_processing_jobs`
* **Partition Key**: `job_id` (`String`, UUID v4, e.g., `doc-677994-a1b2c3d4`).
* **Sort Key**: `created_at` (`String`, ISO-8601 UTC timestamp, e.g., `2026-06-03T08:30:00Z`).
* **Billing Mode**: `PAY_PER_REQUEST` (On-Demand Capacity Mode) - No pre-provisioned RCU/WCU, zero idle costs.

##### Sample DynamoDB Item JSON:
```json
{
  "job_id": "doc-677994-a1b2c3d4",
  "created_at": "2026-06-03T08:30:00Z",
  "updated_at": "2026-06-03T08:30:04Z",
  "status": "COMPLETED",
  "original_filename": "financial-report-q1.pdf",
  "file_size_bytes": 2458920,
  "s3_input_bucket": "huylam-ocr-documents-ap-southeast-1",
  "s3_input_key": "uploads/doc-677994-a1b2c3d4/financial-report-q1.pdf",
  "total_pages": 12,
  "digital_pages_count": 10,
  "scanned_pages_count": 2,
  "fast_path_enabled": true,
  "fast_path_latency_ms": 1420,
  "ocr_latency_ms": 2580,
  "total_processing_time_seconds": 4.0,
  "ocr_engine_used": "KAGGLE_QWEN_2.5_VL",
  "s3_output_markdown_key": "outputs/doc-677994-a1b2c3d4/result.md",
  "s3_output_docx_key": "outputs/doc-677994-a1b2c3d4/result.docx",
  "student_id": "0212267",
  "student_name": "Lâm Quang Huy"
}
```

#### 3. Large File Ingestion with S3 Presigned URLs:

* **Problem Statement**: Amazon API Gateway enforces a non-configurable maximum payload quota of **10 MB**. Real-world PDF documents and scanned legal filings frequently range between 15 MB and 100 MB, causing `413 Payload Too Large` failures when uploaded through traditional API proxies.
* **Architectural Solution**: Utilizing **S3 Presigned PUT URLs**:
  1. The browser initiates a lightweight HTTP POST request (containing only file metadata) to `/api/v1/upload-url`.
  2. The `PresignedUrlHandler` Lambda function generates a temporary, cryptographically signed SigV4 URL with a short expiration window (15 minutes).
  3. The browser streams the binary payload directly to Amazon S3 via HTTPS.
  4. Upon completion, S3 emits an `s3:ObjectCreated:Put` event that automatically triggers the backend parsing pipeline.

#### 4. Centralized Configuration and Key Security with SSM Parameter Store:

Following Twelve-Factor App principles, secrets and runtime toggles are completely separated from the codebase:
* Configuration parameters under `/huylam-ocr/config` are stored in **AWS Systems Manager Parameter Store** as `SecureString` types, encrypted with AWS KMS.
* Configurable parameters include:
  * `ocr_mode`: Active processing mode (`HYBRID_KAGGLE`, `STANDALONE`, or `LOCAL_MOCK`).
  * `scan_threshold_chars`: Character density threshold for scan detection (default 50 characters).
  * `kaggle_endpoint`: Dynamic Cloudflare Tunnel URL connecting to external GPU servers.
  * `gemini_api_key`: Google Gemini API key used for automatic fallback.
* Lambda loads these parameters dynamically during cold-start initialization using Boto3, allowing immediate rotation of API keys or tunnel URLs without redeploying Lambda code.

---

### Week 9 Achievements:

* Successfully formulated the detailed architectural blueprint for the graduation capstone: **Serverless Hybrid Document OCR & Parsing Platform on AWS**.
* Mastered the operational lifecycle, execution model, and concurrency dynamics of **AWS Lambda**, **Amazon API Gateway**, and **Amazon DynamoDB**.
* Designed an optimized NoSQL schema for `document_processing_jobs` on DynamoDB using On-Demand billing.
* Established an S3 Presigned URL workflow bypassing API Gateway's 10 MB payload ceiling.
* Architected an IAM execution role adhering strictly to the principle of Least Privilege.
* Prepared technical specifications and data flow diagrams, paving the way for full infrastructure deployment in Week 10.
* Maintained cloud expenditure at **$0.00**, leveraging the AWS Free Tier extensively.

---

### Implementation Plan for Week 10:
* Provision the Amazon DynamoDB table `document_processing_jobs` and Amazon S3 document buckets.
* Configure secure parameters on AWS Systems Manager Parameter Store (`SecureString`).
* Package and deploy the Fast-Path Parser Lambda function bundled with PyMuPDF.
* Establish S3 Event Notifications to invoke Lambda upon object uploads.
* Conduct benchmark testing on digital PDF parsing speed and verify DynamoDB state persistence.