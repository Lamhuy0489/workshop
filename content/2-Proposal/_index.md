---
title: "Project Proposal"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS

## Capstone Project Proposal: Multi-Tiered Hybrid Document Extraction, Selective Vision OCR & Technical Translation on AWS Cloud

---

### Student Identity & System Information

* **Student Name**: Lam Quang Huy
* **Student ID (MSSV)**: `0212267`
* **Specialized Class**: 67CS - Department of Information Technology
* **Academic Institution**: Hanoi University of Civil Engineering (HUCE)
* **AWS Account ID**: `677994024390` | **Account Name**: `huylam`
* **Target AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
* **Live Production URL**: [http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com](http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com)
* **Application GitHub Repository**: [https://github.com/Lamhuy0489/aws](https://github.com/Lamhuy0489/aws)
* **Workshop Documentation GitHub Repository**: [https://github.com/Lamhuy0489/workshop](https://github.com/Lamhuy0489/workshop)

---

## 1. Executive Summary

**Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS** is an enterprise-grade cloud computing system engineered to tackle the complex challenges of document digitization, structural parsing, and multi-language technical translation. It seamlessly processes intricate technical assets (engineering specifications, legal agreements, financial statements, scientific research papers in PDF and scanned images) with ultra-low latency, high fidelity, and strictly managed zero-dollar cloud operational expenditure ($0.00 throughout the internship period).

The platform unifies three core technological pillars:
1. **Two-Stage Hybrid Parsing Engine**:
   * **Layer 1 (Fast-Path Native)**: Directly extracts raw digital text streams, tabular matrices, and font hierarchies using high-performance C-bindings (`PyMuPDF`) at **0.1s - 0.3s/page** without invoking external AI APIs, eliminating 100% of compute expenses for over 80% of standard digital PDF files.
   * **Layer 2 (Selective Vision OCR)**: Intelligently identifies scanned image pages or complex graphical forms, selectively routing them to Vision AI models (Kaggle Qwen2.5-VL via Cloudflare Tunnel, Google Gemini 3.6 Flash failover, and optional AWS Native Bedrock Nova).
2. **Markdown-Preserving Technical Translation Engine**:
   * Automatically segments and translates technical documentation into Vietnamese and international languages (English, Japanese, Korean, Chinese, French, German).
   * Guarantees 100% syntactic preservation of Markdown headings, nested bullet lists, code blocks, and complex financial/engineering tables.
3. **Multi-Format Export Engine**:
   * Exports processed documents automatically to Markdown (`.md`), Microsoft Word (`.docx` fully compliant with macOS and Windows formatting engines), and print-ready PDF formats.

The cloud architecture integrates an **Enterprise Three-Tier Networking Model** with an **Event-Driven Serverless Pipeline**:
* **Ingress & Load Distribution Tier**: Internet-facing Multi-AZ Application Load Balancer `huylam-ocr-alb` ingesting HTTP port 80 traffic with intelligent health check routing.
* **Application Compute Tier**: Amazon EC2 host `huylam-ocr-web-server` (AL2023, t2.micro) shielded by chained security groups, operating a systemd Gunicorn WSGI daemon on internal port 5000, managed securely via AWS Systems Manager Session Manager with zero open SSH ports.
* **Storage & Database Tier**: Amazon S3 bucket `huylam-ocr-documents-ap-southeast-1` managing `uploads/` and `outputs/`; Amazon DynamoDB NoSQL table `document_processing_jobs` auditing job states and metrics.
* **Centralized Configuration Management**: AWS Systems Manager Parameter Store `/huylam-ocr/config` (`SecureString` encrypted via AWS KMS), eliminating hardcoded API keys.
* **Event-Driven Serverless Pipeline**: Real-time asynchronous ingestion triggered via `s3:ObjectCreated:*` into AWS Lambda `huylam-ocr-processor` updating DynamoDB and CloudWatch Logs in 214 ms.

---

## 2. Problem Statement & Proposed Solution

### 2.1. Contemporary Technical Document Parsing Bottlenecks
1. **Structural and Tabular Degradation**: Traditional OCR engines (e.g., Tesseract) output flat text streams, scrambling multi-column reading flows and obliterating tabular matrix alignments.
2. **Excessive Latency and Cost of Vision AI Overuse**: Submitting multi-page (50 - 100 pages) documents entirely into massive Vision LLMs incurs severe network bandwidth contention, minute-long turnaround latencies, and high GPU costs, despite most digital documents containing extractable digital text.
3. **Formatting Loss in Machine Translation**: Off-the-shelf automated translators frequently mangle Markdown syntax, break header hierarchies, and corrupt tabular structures during technical domain translation.
4. **Cloud Infrastructure Security Exposures**: Publicly exposing SSH port 22 exposes virtual hosts to brute-force intrusion, while static credentials stored on instances risk credentials leakage.

### 2.2. Proposed Technical Solution
The project delivers an architectural breakthrough solving these limitations:
* **Hybrid Fast-Path + Selective Vision Routing**: Documents are analyzed page-by-page; 80 - 90% of digital pages are extracted in 0.1s - 0.3s via PyMuPDF at $0.00 cost, triggering Vision OCR only when rasterized scans are detected.
* **Structure-Preserving Translation Engineering**: Specialized prompt constraints enforce 100% preservation of Markdown table schemas (`| Header 1 | Header 2 |`) and technical lexicon.
* **Chained Security Group Perimeter**: Total isolation of application hosts from public ingress; internal port 5000 admits traffic exclusively originating from the Application Load Balancer security group.
* **Zero Static Credentials Model**: EC2 compute instances and Lambda functions interact with S3 and DynamoDB via IAM Instance Profiles and STS temporary sessions; sensitive configuration resides in KMS-encrypted SSM Parameter Store.

---

## 3. Architecture Blueprint

The platform adheres strictly to the **AWS Well-Architected Framework**, synthesizing an **Enterprise Three-Tier Cloud Networking Model** with an **Event-Driven Serverless Pipeline**:

![System Architecture Blueprint](/images/architecture/aws-system-architecture.png?width=100%&classes=border,shadow)

> [!NOTE] Architecture Diagram File Formats
> * **High-Resolution Render**: `/images/architecture/aws-system-architecture.png` (Retina 1400x920)
> * **Scalable Vector Graphic**: `/images/architecture/aws-system-architecture.svg`
> * **Editable Source Diagram**: `/images/architecture/aws-system-architecture.drawio` (Directly importable into [diagrams.net](https://app.diagrams.net/) with official AWS 2024 stencils).

```text
                                  [ Internet Web Client / User ]
                                                │
                                                ▼ HTTP : 80
                    ┌───────────────────────────────────────────────────────┐
                    │       AWS Application Load Balancer (Multi-AZ)        │
                    │         huylam-ocr-alb (Public DNS Endpoint)          │
                    │             Security Group: huylam-alb-sg             │
                    └───────────────────────────┬───────────────────────────┘
                                                │
                        Forward to Target Group │ Port 5000
                        Health Check: /login    │ (HTTP 200 OK)
                                                ▼
                    ┌───────────────────────────────────────────────────────┐
                    │          Amazon EC2 Application Host (AL2023)         │
                    │         huylam-ocr-web-server (t2.micro / 10.0.8.15)  │
                    │             Security Group: huylam-web-sg             │
                    │       (Inbound TCP 5000 only from huylam-alb-sg)      │
                    │                                                       │
                    │   ┌───────────────────────────────────────────────┐   │
                    │   │        Gunicorn WSGI (huylam-ocr.service)     │   │
                    │   │          Flask Web Studio SPA (Port 5000)     │   │
                    │   └───────────────────────┬───────────────────────┘   │
                    │                           │                           │
                    │       ┌───────────────────┴───────────────────┐       │
                    │       ▼                                       ▼       │
                    │   [ Layer 1: Fast-Path ]              [ Layer 2: OCR ]│
                    │    PyMuPDF (0.1s - 0.3s)               Kaggle GPU     │
                    │    Cost: $0.00                         Gemini Flash   │
                    │                                        AWS Bedrock    │
                    │       │                                       │       │
                    │       └───────────────────┬───────────────────┘       │
                    │                           ▼                           │
                    │             [ Technical Translation Engine ]          │
                    │             100% Markdown Structure Fidelity          │
                    │                           │                           │
                    │             [ Multi-Format Export Engine ]            │
                    │             Markdown (.md), Word (.docx), PDF         │
                    └───────────────┬───────────────────────┬───────────────┘
                                    │ IAM Role              │ IAM Role
                                    │ huylam-ssm-role       │ huylam-ssm-role
                                    ▼                       ▼
                    ┌─────────────────────────────┐  ┌──────────────────────┐
                    │     Amazon S3 Storage       │  │   Amazon DynamoDB    │
                    │     huylam-ocr-documents-   │  │   document_          │
                    │     ap-southeast-1          │  │   processing_jobs    │
                    │     - uploads/ (Raw Files)  │  │   (PK: job_id,       │
                    │     - outputs/ (Artifacts)  │  │    SK: created_at)   │
                    └───────────────┬─────────────┘  └──────────▲───────────┘
                                    │                           │
                                    │ Event: s3:ObjectCreated:* │ PutItem
                                    ▼                           │ (214 ms)
                    ┌─────────────────────────────┐             │
                    │    AWS Lambda Function      │─────────────┘
                    │    huylam-ocr-processor     │
                    │    (Python 3.11 Serverless) │
                    └───────────────┬─────────────┘
                                    │
                                    ▼ Logs & Telemetry
                    ┌─────────────────────────────┐
                    │    Amazon CloudWatch Logs   │
                    │    /aws/lambda/huylam-ocr-  │
                    │    processor                │
                    └─────────────────────────────┘
```

---

## 4. AWS Services & Core Technologies

| Service / Technology | Role in System | Architectural Rationale |
| :--- | :--- | :--- |
| **Amazon VPC** (`huylam-vpc`) | Enterprise virtual network isolation | Custom CIDR `10.0.0.0/16`, 2 Multi-AZ Public Subnets (`1a` and `1b`), Internet Gateway `huylam-igw` |
| **Security Groups** | Chained perimeter firewalls | `huylam-alb-sg` exposes HTTP 80; `huylam-web-sg` restricts port 5000 ingress strictly to ALB SG |
| **Application Load Balancer** (`huylam-ocr-alb`) | Traffic ingress and DNS resolution | Multi-AZ load distribution, automatic health monitoring targeting `/login` |
| **Amazon EC2** (`huylam-ocr-web-server`) | Web Studio application host | Amazon Linux 2023, t2.micro (Free Tier), running systemd Gunicorn daemon |
| **AWS Systems Manager** | Secure administration & secrets storage | Session Manager removes SSH port 22 exposure; Parameter Store manages KMS encrypted JSON configuration |
| **Amazon S3** (`huylam-ocr-documents-ap-southeast-1`) | Input document & export storage | Prefixes `uploads/`, `outputs/`, configured CORS policy, and real-time Event Notifications |
| **Amazon DynamoDB** (`document_processing_jobs`) | Processing audit trail & job state | On-Demand NoSQL mode (`PAY_PER_REQUEST`), sub-millisecond retrieval, $0.00 idle cost |
| **AWS Lambda** (`huylam-ocr-processor`) | Serverless event automation | Autonomous DynamoDB state ingestion on S3 upload events, executing in 214 ms |
| **Amazon CloudWatch** | Centralized observability & logging | Lambda invocation logs, Target Group health telemetry, and EC2 resource metrics |
| **PyMuPDF & Python 3.11** | Layer 1 Fast-Path extraction | Zero-cost digital PDF parsing completed in 0.1s - 0.3s per page |
| **Kaggle GPU & Gemini Flash** | Layer 2 Selective Vision OCR | High-accuracy scanned document OCR with seamless automated failover protection |
| **Amazon Bedrock (Nova)** | Enclosed enterprise AI option | Independent AWS Native model choice for environments with zero external data transfer policies |
| **Docker & Amazon ECR** | Containerization & distribution | OCI Container build on `python:3.11-slim`, optimized for cloud deployment |

---

## 5. Empirical Benchmark Metrics

| Benchmark Evaluation | Test Artifact | Execution Engine | Processing Time | Estimated Cost | Quality Assessment |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Digital Text Extraction (Fast-Path)** | `cv.pdf` (1 page) | Fast-Path Native (PyMuPDF) | **0.31s** | $0.00 | Instantaneous, high fidelity |
| **Multi-page Scientific Paper** | `28_Bai_Bao_...pdf` (11 pages) | Fast-Path Native (PyMuPDF) | **3.07s** (~0.28s/page) | $0.00 | Complete document structure retained |
| **Scanned Form Extraction (OCR)** | Scanned Vietnamese form | Kaggle Qwen2.5-VL / Gemini | **2.54s** | $0.00 (Free Tier) | Full Vietnamese diacritics recognized |
| **AWS Native Option Extraction** | Scanned document image | Amazon Bedrock (Nova) | **4.61s - 6.99s** | Pay-as-you-go | Precise extraction of tabular data |
| **Markdown-Preserving Translation** | `cv.pdf` (English -> Vietnamese) | Gemini Flash Translator | **2.80s** | $0.00 (Free Tier) | 100% preservation of Markdown headers/tables |
| **Microsoft Word (.docx) Export** | `cv.pdf` -> `cv.pdf.docx` | DocxExporter Module | **0.15s** | $0.00 | 38.2 KB file renders cleanly in Word macOS |
| **Automated S3 -> Lambda Trigger** | Upload to `uploads/` prefix | AWS Lambda (Python 3.11) | **214 - 257 ms** | $0.00 (Free Tier) | Immediate DynamoDB state provisioning |
| **Live Production ALB Response** | Access to `http://huylam-ocr-alb...` | Application Load Balancer | **15 - 25 ms** | $0.00 (Free Tier) | Seamless 302 redirection to `/login` |

---

## 6. 12-Week Implementation Roadmap & Accomplishments

- **Weeks 1 - 4 (AWS Cloud Infrastructure Foundations)**: IAM governance, VPC networking, EC2 Linux compute, Amazon S3 object storage, and AWS Systems Manager administration.
- **Weeks 5 - 8 (Observability, Load Balancing & Containerization)**: CloudWatch alarms, CloudFormation infrastructure-as-code, Docker image packaging, and Amazon ECR registry publishing.
- **Week 9 (Serverless Architecture Blueprinting)**: Formulated Serverless Microservices blueprint, DynamoDB `document_processing_jobs` schema, and S3 Presigned URL security flow.
- **Week 10 (Storage, Database & Configuration Deployment)**: Provisioned S3 bucket `huylam-ocr-documents-ap-southeast-1`, DynamoDB On-Demand table, and SSM Parameter Store `/huylam-ocr/config`.
- **Week 11 (Performance Benchmarking & Event-Driven Automation)**: Validated benchmark metrics, finalized OCI Dockerfile, and operationalized S3 Event Notification -> AWS Lambda -> DynamoDB pipeline.
- **Week 12 (Three-Tier Enterprise Cloud Deployment & Public URL Launch)**:
  * Deployed Multi-AZ VPC (`huylam-vpc`), subnets, Internet Gateway, and chained security groups (`huylam-alb-sg`, `huylam-web-sg`).
  * Launched EC2 instance `huylam-ocr-web-server` with IAM Role `huylam-ssm-role`, managed via Session Manager, activating systemd Gunicorn service.
  * Configured Target Group `huylam-ocr-tg` (Health check `/login`, Healthy 1/1 status) and Internet-facing Multi-AZ Application Load Balancer `huylam-ocr-alb`.
  * Launched live public DNS endpoint: `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`.
  * Compiled 10 proof screenshots with red bounding boxes surrounding Account Badge `huylam (677994024390)`.

---

## 7. FinOps Governance ($0.00 Budget Optimization)

The project adheres strictly to FinOps principles, ensuring zero accidental cloud expenditure:
1. **Serverless On-Demand Compute**: DynamoDB and Lambda scale dynamically to zero when idle, consuming $0.00 outside active invocations.
2. **Hybrid Multi-Tier Ingestion**: More than 80% of document processing costs are averted via local PyMuPDF Fast-Path execution.
3. **Teardown Governance**: All validation evidence is captured and documented for capstone defense; clear teardown procedures allow immediate deletion of Application Load Balancers and stopping of EC2 instances when evaluations conclude.