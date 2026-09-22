---
title: "Integrated System Testing"
date: 2026-09-23
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

### Practical Objectives

Perform comprehensive End-to-End System Testing for the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS**: Measure public traffic handling via Application Load Balancer, validate ultra-low latency document parsing with Fast-Path PyMuPDF and vision models, verify markdown-preserving technical translation, confirm Microsoft Word artifact generation, and evaluate data consistency across Amazon S3, DynamoDB, and AWS Lambda.

---

## 1. Scenario 1: Access and Authentication via Application Load Balancer

The system is tested directly from the public internet using the DNS endpoint of the Application Load Balancer:
`http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`

### Testing Workflow:
1. Issue an HTTP GET request to the ALB URL:
   - The ALB receives traffic on port 80 and forwards it to Target Group `huylam-ocr-tg`.
   - The Flask backend issues an HTTP 302 redirect to the authentication portal `/login`.
2. The browser renders the login interface with HTTP 200 OK:

![Login via ALB Public DNS](/images/week12/09-browser-alb-public-dns-login.png)

3. Authenticate and enter the main Studio workspace:
   - Session state is securely managed by Flask Session using a secret key retrieved from AWS Systems Manager Parameter Store.
   - The cloud studio workspace renders seamlessly in the browser:

![Live Studio Interface on ALB](/images/week12/10-browser-alb-studio-live.png)

4. Test the Smart Model Selector:
   - The platform allows dynamic switching between Auto Hybrid (cost-optimized default), AWS Bedrock Titan Multimodal, Google Gemini 2.5 Flash, or Claude 3.5 Sonnet:

![Smart Model Selection](/images/week11/01-studio-model-selection-auto.png)

---

## 2. Scenario 2: Benchmark Evaluation of Extraction, Translation, and Export

A real-world technical document (`cv.pdf`) containing tabular structures, hierarchical headers, and technical domain terminology was processed for benchmarking.

### Empirical Benchmark Results:

| Processing Phase | Engine / Implementation | Measured Latency | Quality Assessment |
|------------------|-------------------------|------------------|-------------------|
| Fast-Path Extraction | PyMuPDF (C++ Native Engine) | **0.31 seconds** | 100% text fidelity, exact layout and table retention |
| Vision OCR Parsing | Vision Multimodal Fallback | **2.54 seconds** | Accurate recognition of scanned graphical regions |
| Technical Translation | LLM Domain-Specific Translation | **2.80 seconds** | Preserves Markdown syntax, code tokens, and tables |
| Word Document Export | python-docx Artifact Generator | **0.15 seconds** | Formatted `.docx` document generated (**38.2 KB**) |

### Studio Results Workspace:
The interface displays a side-by-side comparison of the extracted source document and the translated Vietnamese version, alongside download actions for Markdown and Word:

![Studio Extraction and Translation View](/images/week11/03-studio-cv-extracted-translated.png)

### Exported Word Document Verification:
The generated `.docx` artifact was downloaded and inspected in Microsoft Word, confirming full preservation of tables, bullet points, font styles, and header structures:

![Word Artifact Verification](/images/week11/04-word-cv-exported-verification.png)

---

## 3. Scenario 3: Cloud Storage Synchronization and Serverless Event Pipeline

Every document processing lifecycle simultaneously exercises the cloud persistence and serverless pipeline:

1. **Amazon S3 - uploads/ Prefix**:
   - Source documents are safely archived with a unique UUID prefix:

![S3 Uploads Folder](/images/week11/05-s3-bucket-uploads-folder.png)

2. **Amazon S3 - outputs/ Prefix**:
   - Structured Markdown files and translated artifacts are stored independently for on-demand retrieval:

![S3 Outputs Folder](/images/week11/06-s3-bucket-outputs-folder.png)

![S3 Output Markdown Document](/images/week11/07-s3-output-cv-markdown-file.png)

3. **S3 Event Notification & AWS Lambda**:
   - The `s3:ObjectCreated:*` event triggers `huylam-ocr-processor`.
   - Execution finishes in **214 ms** and automatically inserts job records into DynamoDB.

4. **Amazon DynamoDB**:
   - The `document_processing_jobs` table logs the job lifecycle metadata:
     - `job_id`: Unique identifier.
     - `filename`: `cv.pdf`.
     - `status`: `RECEIVED_VIA_S3_EVENT`.
     - `s3_input_uri` and `s3_output_md_uri`.
     - `processing_time_seconds`: `0.05`.

![DynamoDB Item Sync](/images/week11/13-dynamodb-items-received-s3-event.png)

---

## 4. AWS Services Integration Summary

| AWS Service | Architectural Role | Verification Status |
|-------------|--------------------|---------------------|
| Amazon VPC | Multi-AZ network isolation on 10.0.0.0/16 | Stable, segmented, secure |
| Application Load Balancer | Public HTTP traffic distribution and health checks | Target Health 1/1 Healthy, smooth routing |
| Amazon EC2 | Hosts Python Flask application via Gunicorn and systemd | Sub-0.4s response time for web endpoints |
| Amazon S3 | Document input and output persistence | Fast uploads/downloads, reliable event triggers |
| AWS Lambda | Serverless event handler for document ingestion | 214 ms execution, 88 MB RAM usage |
| Amazon DynamoDB | Distributed document job state management | Sub-10ms read/write latency |
| AWS SSM Parameter Store | Centralized KMS-encrypted configuration store | Secure parameter injection at boot |
| Amazon CloudWatch | Target Group metrics and Lambda logging | End-to-end operational observability |

---

## 5. Testing Conclusion

Live system testing validates that the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** operates reliably on AWS. By combining a cost-effective EC2 compute host with serverless event-driven processing, the platform delivers high throughput, low latency, and zero ongoing operational cost within AWS Free Tier limits.