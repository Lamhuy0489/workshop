---
title: "Workshop Overview"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### Module Objective

This module delivers a comprehensive technical overview of the real-world engineering challenge, system architecture, and operational workflows for the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** on AWS. Upon completion, you will thoroughly understand the principles of the enterprise three-tier networking model integrated with event-driven serverless computing, ready for hands-on infrastructure implementation.

---

## 1. Problem Statement & Architectural Solution

### 1.1. Enterprise Technical Document Processing Challenges
Digitizing and translating technical documentation (engineering blueprints, contracts, financial disclosures, scientific literature) faces critical industry bottlenecks:
1. **Structural and Tabular Degradation**: Conventional OCR engines extract unformatted flat text, destroying tabular numeric alignments, heading hierarchies, and multi-column reading flows.
2. **Excessive Latency and Cost from Vision AI Overuse**: Ingesting entire multi-page documents into heavy Vision LLMs introduces minute-long latency bottlenecks and severe token expenditures, even though over 80% of digital office documents contain extractable digital text streams.
3. **Loss of Technical Context during Translation**: Generic machine translation tools fail to preserve Markdown syntax, corrupting tables, code blocks, and domain-specific terminology.
4. **Cloud Security Vulnerabilities**: Traditional server management via exposed public SSH port 22 creates brute-force attack vectors, while hardcoded credentials risk severe leakage.

### 1.2. Architectural Breakthrough
The platform resolves these bottlenecks via:
- **Two-Stage Hybrid Parsing Pipeline**:
  - *Layer 1 (Fast-Path Native)*: Direct digital extraction of text and tables via `PyMuPDF` in **0.1s - 0.3s/page** at strictly $0.00 cost.
  - *Layer 2 (Selective Vision OCR)*: Automated classification routing scanned pages or complex diagrams to Vision AI (Kaggle GPU Qwen2.5-VL / Gemini Flash / AWS Bedrock Nova).
- **Structure-Preserving Technical Translation Engine**: Translates technical text into Vietnamese and global languages while preserving 100% of Markdown schema, headers, and tables.
- **Multi-Format Export Engine**: Exports parsed documents into Markdown (`.md`), print-ready Microsoft Word (`.docx`), and PDF.
- **Enterprise Three-Tier Cloud Architecture on AWS**: Chained security groups providing network isolation, combined with a real-time event-driven serverless pipeline.

---

## 2. System Architecture Blueprint

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

## 3. End-to-End Operational Workflow

1. **User Client Ingress**: Users access the platform via the Public DNS URL of Application Load Balancer `huylam-ocr-alb`.
2. **Load Balancing & Forwarding**: ALB routes requests into Target Group `huylam-ocr-tg`, forwarding traffic to internal port 5000 of EC2 host `huylam-ocr-web-server`.
3. **Security Chaining Isolation**: Kernel-level firewall `huylam-web-sg` restricts port 5000 access exclusively to ALB traffic, eliminating unauthorized direct connections.
4. **Web Studio Service Execution**: Systemd daemon `huylam-ocr.service` executes the Gunicorn WSGI server hosting the Single-Page Application (SPA).
5. **Secure Storage Ingestion**: Uploaded documents are saved directly into the `uploads/` prefix of S3 bucket `huylam-ocr-documents-ap-southeast-1` via STS temporary credentials issued to `huylam-ssm-role`.
6. **Serverless Event Trigger**: S3 `s3:ObjectCreated:*` triggers AWS Lambda `huylam-ocr-processor`, recording job metadata in DynamoDB table `document_processing_jobs` in 214 ms.
7. **Extraction, Translation & Export**: Layer 1 extracts digital text in 0.1s - 0.3s/page; Layer 2 executes selective OCR on scanned pages; technical translation preserves Markdown structure; exported Word/PDF files are written to S3 `outputs/`.

---

## 4. AWS Services Inventory

| AWS Service | Engineering Role |
| :--- | :--- |
| **Amazon VPC** | Enterprise virtual private cloud with 2 Multi-AZ Public Subnets (`ap-southeast-1a`, `ap-southeast-1b`) and Internet Gateway |
| **Security Groups** | Defense-in-depth security chaining: `huylam-alb-sg` (ALB) and `huylam-web-sg` (EC2 host) |
| **Application Load Balancer** | Multi-AZ traffic distribution, automated health checks targeting `/login`, and public DNS endpoint allocation |
| **Amazon EC2** | Application host running Amazon Linux 2023, t2.micro, managed via systemd Gunicorn daemon |
| **AWS Systems Manager** | Secure management via Session Manager (zero SSH exposure); KMS-encrypted Parameter Store |
| **Amazon S3** | Object storage for `uploads/` and `outputs/`, configured with CORS and S3 Event Notifications |
| **Amazon DynamoDB** | On-Demand NoSQL table `document_processing_jobs` auditing execution states and latencies |
| **AWS Lambda** | Asynchronous serverless event-driven processing of incoming document uploads |
| **Amazon CloudWatch** | Centralized telemetry aggregating logs, metrics, and health evaluation status |

---

## 5. Expected Learning Outcomes

By completing this hands-on workshop series, you will:
- Provision an enterprise three-tier cloud networking architecture on AWS.
- Implement security group chaining to isolate application hosts from external ingress.
- Administer EC2 instances securely via AWS Systems Manager Session Manager without open SSH port 22.
- Deploy an OCR & Translation Web Studio capable of 0.1s/page Fast-Path digital text extraction.
- Construct an event-driven serverless document processing pipeline with 214 ms execution latency.
- Master FinOps cloud cost optimization preserving a strict $0.00 budget.