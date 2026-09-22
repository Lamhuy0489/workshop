---
title: "Week 11 Worklog"
date: 2026-09-22
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:
* Conduct end-to-end performance benchmarking and comprehensive integration validation on the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
  * **Layer 1 Fast-Path Extraction Latency**: Benchmark native digital PDF text stream parsing via PyMuPDF, validating ultra-low latency goals (0.1s - 0.3s/page) at strictly $0.00 cost.
  * **Independent AWS Native Model Option**: Integrate the **AWS Native Model (Amazon Bedrock / Nova)** into the Web Studio model selector dropdown as a standalone selectable option (non-parallel) to prevent redundant cloud token expenditure and compute overhead.
  * **Technical Translation Quality**: Validate Markdown-preserving document translation into Vietnamese, enforcing 100% fidelity on headers, nested lists, and technical data tables.
  * **Multi-Format Publication**: Benchmark export capabilities to Microsoft Word (`.docx`) and print-ready PDF formats, verifying real-world rendering compatibility on native macOS Microsoft Word.
  * **Automated Cloud Synchronization**: Validate automated data streaming to Amazon S3 (`uploads/` and `outputs/`) and metadata logging in the Amazon DynamoDB `document_processing_jobs` table.
  * **Web Studio Containerization via Docker**: Construct standard OCI Container specifications (`Dockerfile` and `.dockerignore`) on `python:3.11-slim` for enterprise cloud hosting on AWS ECS Fargate and Amazon ECR.
  * **Event-Driven Serverless Document Pipeline**: Architect and operationalize an automated real-time ingestion pipeline: **Amazon S3 Event Notification -> AWS Lambda `huylam-ocr-processor` -> Amazon DynamoDB `document_processing_jobs` -> Amazon CloudWatch Logs**.

---

### Tasks carried out this week:

| Day | Task | Achievement | Reference Material |
| :--- | :--- | :--- | :--- |
| **Monday** | - Design and establish multi-tiered document benchmarking suite.<br>- Execute parsing experiments across real-world digital documents (`cv.pdf`, `28_Bai_Bao_Hoi nghi KH_Khoa_CNTT_2026.pdf`, official circulars).<br>- Measure Layer 1 Fast-Path latency across single and multi-page documents. | `cv.pdf` parsed in 0.31s; 11-page scientific paper parsed in 3.07s (~0.28s/page), achieving 100% of performance targets. | [PyMuPDF High-Performance Text Extraction](https://pymupdf.readthedocs.io/en/latest/) |
| **Tuesday** | - Enhance Web Studio user interface: Add standalone **AWS Native Model (Amazon Bedrock / Nova)** entry to `#modelChoice` select menu.<br>- Update multilingual dictionaries `i18n.js` for Vietnamese and English locales.<br>- Update system configuration schema `AppSettings.ocr_mode` to support `"AWS_NATIVE"`. | Web Studio supports standalone AWS Native model execution without forced parallel runs, giving users complete operational control. | [AWS Bedrock Model Access](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html) |
| **Wednesday** | - Upgrade Boto3 SDK layer in `AWSStorageService`: add `image_bytes` and `image_format` arguments to `invoke_bedrock_converse` for Multimodal Converse API invocation.<br>- Implement graceful failover mechanism in `OCRDispatcher`: automatically route to Gemini Flash if AWS Bedrock policy restrictions occur. | System equipped for Amazon Bedrock Converse Vision OCR with seamless fallback resilience. | [Amazon Bedrock Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| **Thursday** | - Conduct technical translation quality tests directly on Web Studio.<br>- Translate full content of `cv.pdf` into technical Vietnamese.<br>- Verify integrity of Markdown formatting, profile summary, technical competencies, and project history. | Translated document reached 3,480 characters with 100% preservation of Markdown headers, bullet points, and semantics. | [Gemini Technical Translation Prompting](https://ai.google.dev/gemini-api/docs/prompting-strategies) |
| **Friday** | - Test multi-format publication pipeline from Web Studio: export Microsoft Word document `cv.pdf.docx` (38.2 KB).<br>- Open generated `.docx` artifact directly inside Microsoft Word on macOS.<br>- Inspect typography, line breaks, header hierarchy, and styling consistency.<br>- Construct `Dockerfile` and `.dockerignore` to containerize Web Studio application for AWS ECS Fargate. | Exported DOCX artifact renders cleanly with zero formatting anomalies on macOS Word. Containerization assets committed to GitHub repository. | [python-docx Documentation](https://python-docx.readthedocs.io/en/latest/)<br>[AWS ECS Fargate Container Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html) |
| **Saturday** | - Establish Event-Driven Serverless architecture on AWS Management Console:<br>  * Create IAM Role `huylam-ocr-lambda-role` with inline policy `LambdaS3DynamoDBAccess`.<br>  * Create and deploy AWS Lambda Function `huylam-ocr-processor` (Python 3.11, ap-southeast-1).<br>  * Configure Amazon S3 Event Notification `NewDocumentUploadTrigger` targeting `uploads/` prefix directly into Lambda Function. | Event-Driven Serverless foundation established, ready to capture and process upload events in real time. | [Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EventNotifications.html)<br>[AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) |
| **Sunday** | - Test upload triggers on S3 Console: Lambda triggered automatically within 214 ms - 257 ms, persisting 3 job records to DynamoDB `document_processing_jobs` (`RECEIVED_VIA_S3_EVENT`).<br>- Monitor end-to-end execution on Amazon CloudWatch Logs.<br>- Capture and annotate 15 proof screenshots with red bounding boxes surrounding Account Badge `huylam (677994024390)`.<br>- Author bilingual Week 11 worklog documentation and compile with Hugo site generator. | 100% completion of Week 11 performance benchmarking, Docker containerization, and Event-Driven automation. | [AWS Free Tier Dashboard](https://aws.amazon.com/free/) |

---

### Benchmark Empirical Metrics:

| Benchmark Criterion | Test Artifact | Processing Engine | Latency | Estimated FinOps Cost | Evaluation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Digital Text Extraction (Fast-Path)** | `cv.pdf` (1 page) | Fast-Path Native (PyMuPDF) | **0.31s** | $0.00 | Outstanding, instantaneous |
| **Multi-page Digital Text Extraction** | `28_Bai_Bao_...pdf` (11 pages) | Fast-Path Native (PyMuPDF) | **3.07s** (~0.28s/page) | $0.00 | Outstanding, structure preserved |
| **Scanned Image Extraction (Vision OCR)** | `Screen Shot ... .png` (1 image) | Google Gemini Flash Lite | **2.54s** | $0.00 (Free Tier) | Full Vietnamese diacritics captured |
| **AWS Native Option Extraction** | `Screen Shot ... .png` (1 image) | AWS Bedrock (Amazon Nova) | **4.61s - 6.99s** | Pay-as-you-go | Precise tabular data extraction |
| **Markdown-Preserving Translation** | `cv.pdf` (English -> Vietnamese) | Gemini Flash Translator | **2.80s** | $0.00 (Free Tier) | 100% Markdown layout fidelity |
| **Microsoft Word (.docx) Export** | `cv.pdf` -> `cv.pdf.docx` | DocxExporter Module | **0.15s** | $0.00 | 38.2 KB file opens cleanly in Word |
| **S3 Event -> Lambda Trigger Latency** | PDF / PNG files in `uploads/` | AWS Lambda (Python 3.11) | **214 - 257 ms** | $0.00 (Lambda Free Tier) | Autonomous DynamoDB state ingestion |

---

### Event-Driven Serverless Pipeline Architecture:

The automated event-driven processing workflow operates seamlessly through the following sequence:

1. **Client / Admin Upload**: Users or automated processes upload documents (PDF, images) into the `uploads/` prefix of Amazon S3 bucket `huylam-ocr-documents-ap-southeast-1`.
2. **S3 Event Notification**: The `s3:ObjectCreated:*` event filter triggers `NewDocumentUploadTrigger` immediately upon upload completion.
3. **AWS Lambda Execution**: AWS Lambda Function `huylam-ocr-processor` receives the S3 event payload, extracts object key and size, and provisions a unique `job_id`.
4. **DynamoDB State Ingestion**: Lambda inserts an audit item into Amazon DynamoDB table `document_processing_jobs` with status `RECEIVED_VIA_S3_EVENT`, recording `s3_input_uri` and computed `s3_output_md_uri`.
5. **CloudWatch Monitoring**: Full execution telemetry, including `RequestId`, Duration (214 ms), and Memory allocation (88 MB / 128 MB), is streamed to Amazon CloudWatch Logs `/aws/lambda/huylam-ocr-processor`.

---

### Empirical Proofs with Red Bounding Boxes (AWS Console & Web Studio):

> [!IMPORTANT]
> All proof screenshots feature prominent red bounding boxes highlighting the **AWS Account Badge `huylam (677994024390)`**, service identifiers, IAM roles, Lambda parameters, S3 events, DynamoDB items, and CloudWatch Logs.

#### 1. Web Studio Interface with Automatic (Fast-Path Native) Mode:
Intuitive document extraction interface featuring model selector dropdown, drag-and-drop ingestion, and Layer 1 Fast-Path toggle:
![Web Studio Automatic Mode Interface](/images/week11/01-studio-model-selection-auto.png)

---

#### 2. Web Studio Interface with Standalone AWS Native Model (Amazon Bedrock / Nova):
Integrated AWS Native model selection operating independently without forced parallel overhead, minimizing cloud token costs:
![AWS Native Model Selection in Studio](/images/week11/02-studio-model-selection-aws-bedrock.png)

---

#### 3. Extraction and Translation Results for cv.pdf on Web Studio:
Extracted and translated document text displayed side-by-side with Word (`.docx`) and PDF export actions:
![Studio CV Extraction and Translation Result](/images/week11/03-studio-cv-extracted-translated.png)

---

#### 4. Microsoft Word macOS Verification for Exported cv.pdf.docx:
Generated Word artifact downloaded and verified inside macOS Microsoft Word, preserving typography, headers, and document structure:
![Word CV Exported Verification](/images/week11/04-word-cv-exported-verification.png)

---

#### 5. uploads/ Ingestion Directory in Amazon S3 Bucket huylam-ocr-documents-ap-southeast-1:
Verification of raw input documents securely ingested into `uploads/` prefix, featuring Account Badge `huylam (677994024390)`:
![S3 uploads Directory](/images/week11/05-s3-bucket-uploads-folder.png)

---

#### 6. outputs/ Artifacts Directory in Amazon S3 Bucket huylam-ocr-documents-ap-southeast-1:
Verification of automated job artifact directories created in `outputs/` prefix per document processing job ID:
![S3 outputs Directory](/images/week11/06-s3-bucket-outputs-folder.png)

---

#### 7. cv.pdf.md Output Artifact in outputs/1f8d7fe1/ on Amazon S3:
Detailed view of generated Markdown file `cv.pdf.md` (3.0 KB) created at 19:38:21 (UTC+07:00), stored in private S3 bucket:
![S3 Output Markdown File Detail](/images/week11/07-s3-output-cv-markdown-file.png)

---

#### 8. IAM Role huylam-ocr-lambda-role and LambdaS3DynamoDBAccess Policy:
Dedicated execution role for AWS Lambda configured with inline policy granting access to S3 bucket and DynamoDB job table:
![IAM Role and Policy for Lambda](/images/week11/08-iam-role-lambda-policy-created.png)

---

#### 9. AWS Lambda Function huylam-ocr-processor Creation:
Lambda Function successfully provisioned with Python 3.11 runtime in ap-southeast-1 region and linked to `huylam-ocr-lambda-role`:
![AWS Lambda Function Creation](/images/week11/09-lambda-function-created-active.png)

---

#### 10. lambda_function.py Source Code Deployment on AWS Lambda:
S3 event notification ingestion script deployed in Lambda code editor, implementing `RECEIVED_VIA_S3_EVENT` status registration:
![Lambda Code Deployed Successfully](/images/week11/10-lambda-code-deployed-success.png)

---

#### 11. Amazon S3 Event Notification NewDocumentUploadTrigger Configuration:
Event notification trigger configured on `uploads/` prefix in `huylam-ocr-documents-ap-southeast-1` bucket routing to Lambda:
![S3 Event Notification Configuration](/images/week11/11-s3-event-notification-created.png)

---

#### 12. Test Document Ingestion into uploads/ Prefix on S3:
Successful file upload to `uploads/` prefix via AWS Console, triggering immediate asynchronous Lambda invocation:
![Test Document Upload on S3](/images/week11/12-s3-upload-test-document.png)

---

#### 13. Automated Processing Records in DynamoDB document_processing_jobs:
DynamoDB table verifying 3 items (`auto-1e4a3832`, `auto-7c746a72`, `auto-162fdcba`) created autonomously with `RECEIVED_VIA_S3_EVENT` status:
![DynamoDB Items Created via S3 Event](/images/week11/13-dynamodb-items-received-s3-event.png)

---

#### 14. CloudWatch Log Group /aws/lambda/huylam-ocr-processor Overview:
CloudWatch Logs overview validating log stream generation for the serverless OCR processor Lambda:
![CloudWatch Log Group Overview](/images/week11/14-cloudwatch-log-group-overview.png)

---

#### 15. CloudWatch Log Events Execution Telemetry:
Detailed log events validating event parsing, DynamoDB persistence, duration of **214.37 ms**, and memory consumption of **88 MB**:
![CloudWatch Log Events Execution](/images/week11/15-cloudwatch-log-events-execution.png)

---

### Week 11 Summary:
* Successfully achieved 100% of performance benchmarking objectives across Fast-Path digital parsing, Vision OCR, and technical translation.
* Integrated standalone **AWS Native Model (Amazon Bedrock / Nova)** option into Web Studio, adhering to enterprise cloud security and FinOps discipline.
* Validated artifact rendering fidelity with native Microsoft Word on macOS.
* Verified autonomous asynchronous cloud synchronization across Amazon S3 and Amazon DynamoDB.
* Containerized Web Studio application via Docker standard OCI image specifications for AWS ECS Fargate deployment.
* Designed, configured, and verified complete end-to-end Event-Driven Serverless architecture: **S3 Event Notification -> AWS Lambda -> DynamoDB -> CloudWatch Logs** with sub-300ms execution latency at 0.00 USD cost.