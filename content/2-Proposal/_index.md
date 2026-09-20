---
title: "Proposal"
date: 2026-09-18
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Serverless Hybrid Document OCR & Parsing Platform on AWS

## High-Performance Hybrid Document Parsing & Selective OCR on AWS

---

# 1. Executive Summary

**Serverless Hybrid Document OCR & Parsing Platform on AWS** is an advanced document processing and digitization platform combining fast native document parsing with selective vision-language artificial intelligence (Selective Vision OCR). The system empowers organizations to automatically parse and recognize complex multilingual documents (contracts, invoices, financial statements in PDF or scanned image formats) into standardized structured formats such as Markdown (`.md`), Microsoft Word (`.docx`), and searchable PDFs while preserving 100% of the original document layout, headings, and numerical tables.

The platform is engineered with a Serverless and Event-Driven architecture on AWS, seamlessly integrating with external accelerated computing environments (Kaggle GPU/TPU) to minimize operating costs (approaching 0 USD in practical testing):
- **Two-Stage Hybrid Parsing Pipeline**:
  - *Stage 1 (Fast-Path)*: Automatically extracts native digital text streams and table structures directly on **AWS Lambda** at 0.1 - 0.3 seconds per page for standard office PDFs with zero inference cost.
  - *Stage 2 (Slow-Path)*: Automatically classifies and routes only scanned images or complex rasterized tables to vision AI models.
- **Dual-Mode Architectural Flexibility**:
  - *Kaggle Accelerated Mode*: Leverages free Kaggle GPU/TPU infrastructure (hosting Qwen2.5-VL or GOT-OCR exposed via Cloudflare Tunnel) to handle heavy scanned pages.
  - *Standalone Fallback Mode*: Automatically fails over to **Google Gemini 1.5 Flash Vision API** if the external Kaggle endpoint becomes unavailable, ensuring 24/7 high availability on AWS.
- **Configuration & Credential Security Layer**: Centrally managed via **AWS Systems Manager (SSM) Parameter Store (SecureString)** encrypted with **AWS KMS**, eliminating hardcoded secret risks.
- **Knowledge & Storage Layer**: Raw and parsed artifacts persisted in **Amazon S3**; pipeline metadata and performance telemetry stored in **Amazon DynamoDB**.
- **Delivery & API Layer**: Ingestion managed via **Amazon API Gateway** (providing S3 Presigned URLs for direct secure uploads) and fronted globally with a static web dashboard hosted on **Amazon S3 + Amazon CloudFront** with SSL/TLS certificates provided by **AWS Certificate Manager (ACM)**.

---

# 2. Problem Statement & Solution

## 2.1. The Problem
1. **Limitations of Traditional OCR**: Conventional OCR tools extract flat, unstructured text, destroying table relationships, scrambling multi-column reading orders, and omitting hierarchical headers.
2. **Computational Bottlenecks of Pure Vision Models**: Feeding entire 50 - 100 page documents into heavy Vision LLMs introduces severe latency and high GPU inference overhead, even though 80-90% of pages already possess digital text streams.
3. **External Endpoint Volatility**: Relying solely on external transient endpoints risks system failures when sessions expire or tunnel URLs change.

## 2.2. Proposed Solution
The **Serverless Hybrid Document OCR & Parsing Platform** solves these challenges through:
- **Hybrid Performance Optimization**: Digital pages are parsed immediately in Stage 1 at zero cost; Stage 2 is selectively invoked only for scanned pages.
- **Strict Layout & Table Fidelity**: Tables are accurately reconstructed into clean Markdown Tables (`| Col 1 | Col 2 |`), headers are preserved hierarchically, and users can export to both `.md` and `.docx`.
- **Resilient Fallback Design**: Dynamic configuration via SSM Parameter Store with automated failover ensures uninterrupted document processing.

---

# 3. Architecture Diagram

```text
[ Web Browser / User ]
            │
            ▼ HTTPS (SSL/TLS)
[ Amazon CloudFront + Amazon S3 Static Hosting ] (Management Dashboard & Result Viewer)
            │
            ▼ REST API Request (Request Presigned URL or Fetch Status)
[ Amazon API Gateway ]
            │
            ├──> 1. Returns secure S3 Presigned URL
            │
[ Amazon S3 Bucket ]
     │
     ├── /uploads/ (Direct client document upload)
     │      │
     │      ▼ (s3:ObjectCreated event automatically triggers)
     ▼
[ AWS Lambda: Hybrid Document Engine ]
     │
     ├── 2. Retrieve config & credentials ─> [ AWS SSM Parameter Store (SecureString) ]
     │
     ├── 3. Stage 1: Fast-Path Native Parser (PyMuPDF)
     │      - Extracts digital text streams, fonts, tables
     │      - Classifies scanned pages based on text density
     │
     ├── 4. Stage 2: Selective Vision OCR (For scanned pages):
     │      ├── [Priority 1: Kaggle GPU/TPU via Cloudflare Tunnel] (Qwen2.5-VL / GOT-OCR)
     │      └── [Fallback: Gemini 1.5 Flash Vision API] (Automatic failover)
     │
     ├── 5. Assembly and multi-format export ─> [ Amazon S3: /outputs/ ]
     │                                           (.md, .docx, .pdf)
     │
     └── 6. Telemetry and job tracking ───────> [ Amazon DynamoDB (document_jobs) ]
                                                [ Amazon CloudWatch (Logs & Metrics) ]
```

---

# 4. AWS Services Utilized

| AWS Service | System Role | Selection Rationale |
| :--- | :--- | :--- |
| **AWS Lambda** | Hybrid Processing & Compute Core | Serverless, event-driven execution on S3 upload, sub-second Stage 1 parsing |
| **Amazon S3** | Raw Document & Parsed Output Storage | 11 9s durability, native Presigned URL support and event notifications |
| **Amazon DynamoDB** | Job Tracking & Telemetry Store | Single-digit millisecond latency, flexible NoSQL schema, 25 GB free tier |
| **AWS Systems Manager** | Secure Parameter & Endpoint Storage | KMS encrypted parameters, runtime configuration switching without redeployment |
| **Amazon API Gateway** | Managed REST API Gateway | Ingestion management, built-in CORS, throttling, and routing |
| **Amazon CloudFront** | Global Content Delivery Network | Accelerates static asset delivery, free ACM HTTPS certification |
| **Amazon CloudWatch** | Observability & Latency Monitoring | Detailed execution telemetry, error tracking, and performance metrics |

---

# 5. Implementation Roadmap

- **Weeks 1 - 2**: AWS account setup, IAM security hardening, AWS Budgets, and AWS CLI configuration.
- **Weeks 3 - 4**: S3 document bucket setup, S3 Event Notifications, and DynamoDB job tracking schema design.
- **Weeks 5 - 6**: Develop Stage 1 (Fast-Path Native Parser) in Python for rapid digital text & table parsing.
- **Weeks 7 - 8**: Complete Stage 2 (Selective OCR Dispatcher) integrating Kaggle TPU/GPU via Cloudflare Tunnel and Gemini API fallback.
- **Weeks 9 - 10**: Build multi-format export modules (.md, .docx) and deploy side-by-side web viewer on S3 + CloudFront.
- **Weeks 11 - 12**: End-to-end testing across diverse document types, latency benchmark analysis, demo video, and final report submission.