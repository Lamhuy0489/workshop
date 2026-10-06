---
title: "Graduation Internship Report (HUCE)"
date: 2026-10-03
weight: 8
chapter: false
pre: " <b> 8. </b> "
---

### HUCE Graduation Internship Report (Form TTTN-06)

The official graduation internship report was compiled in strict compliance with the **TTTN-06** regulatory guidelines of Hanoi University of Civil Engineering (**HUCE**). The document consists of 43 A4 print pages, 12 data tables, 16 high-definition technical architecture diagrams, and 12 cited academic references with direct hyperlinks.

---

### Download Original Report Documents

Mentors, faculty advisors, and readers can download the complete report directly in both standard formats:

| Document Format | File Name | Size | Direct Download Link |
| :--- | :--- | :--- | :--- |
| **Microsoft Word (.docx)** | `Baocao.docx` | ~8.7 MB | [Download Word Document (Baocao.docx)](/downloads/Baocao.docx) |
| **Adobe PDF (.pdf)** | `Baocao.pdf` | ~5.1 MB (< 10 MB) | [Download PDF Document (Baocao.pdf)](/downloads/Baocao.pdf) |
| **Archival Named Version (.docx)** | `Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.docx` | ~8.7 MB | [Download Archival Word File](/downloads/Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.docx) |
| **Archival Named Version (.pdf)** | `Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.pdf` | ~5.1 MB (< 10 MB) | [Download Archival PDF File](/downloads/Bao_Cao_Thuc_Tap_Tot_Nghiep_HUCE_0212267_Lam_Quang_Huy.pdf) |

---

## 1. General Student and Report Information

- **Student Name**: Lâm Quang Huy
- **Student ID (MSSV)**: `0212267`
- **Class / Cohort**: 67CS - Cohort 67
- **Major**: Computer Science
- **Faculty**: Information Technology
- **Institution**: Hanoi University of Civil Engineering (HUCE)
- **Academic Advisor**: MSc. Lê Văn Minh
- **Host Organization**: AMAZON WEB SERVICES VIETNAM COMPANY LIMITED
- **Host Organization Mentor**: Nguyễn Gia Hưng (Email: `hunggia@amazon.com.vn`)
- **Internship Program**: First Cloud AI Journey (AWS FCAJ Workforce Bootcamp 2026)
- **Internship Duration**: August 3, 2026 to October 25, 2026 (12 weeks)
- **Capstone Project**: **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS**

---

## 2. Executive Summary: 6 Core Sections of the Report

The 43-page graduation report comprises 6 comprehensive sections:

### PART 1: HOST ORGANIZATION OVERVIEW
- Global overview of Amazon Web Services (AWS) and Amazon Web Services Vietnam LLC.
- History, mission, and the global cloud infrastructure ecosystem.
- Corporate culture and Amazon Leadership Principles (Customer Obsession, Ownership, Invent and Simplify).
- Overview of the AWS First Cloud AI Journey (FCAJ Bootcamp 2026).

### PART 2: INTERNSHIP WORK PLAN & OBJECTIVES
- General and technical objectives (cloud infrastructure mastery, intelligent document platform development, professional engineering discipline).
- Detailed 12-week work schedule (Weeks 1 through 12) adhering to HUCE Form TTTN-06.
- Task assignment matrix with quantitative milestones for each week.

### PART 3: TECHNICAL IMPLEMENTATION & RESULTS (CORE FOCUS)
- **Secure Networking Infrastructure**: Three-tier Virtual Private Cloud (VPC) across Multi-AZ in `ap-southeast-1` (Singapore).
- **Dual-Engine Hybrid Document Parser**:
  - *Tier 1 (Fast-Path Native Parser)*: PyMuPDF for zero-cost, ultra-low latency digital PDF parsing (0.28s/page, $0.00 cost, 100% table retention).
  - *Tier 2 (Vision OCR & Fallback)*: Automatic routing to computer vision models (Qwen-2.5-VL via Kaggle API / Gemini 2.5 Flash) for scanned pages and handwritten text.
- **AI Document Translation Engine**: Large Language Model (LLM) powered technical translation from English to Vietnamese.
- **Multi-Format Exporters**: Automated export to structured Markdown, formatted Word (.docx), and PDF.
- **Empirical Performance Benchmark**: Rigorous end-to-end latency and accuracy validation under real-world loads.
- **Cloud Financial Discipline (FinOps Zero-Breach)**: Comprehensive Free Tier resource utilization achieving an absolute cumulative spend of **$0.00 USD**.
- **Centralized Operational Observability**: Amazon CloudWatch Logs, Metrics, Alarms, and SNS proactive notifications.

### PART 4: ANALYSIS, EVALUATION & PROPOSED ARCHITECTURAL ENHANCEMENTS
- Objective milestone completion review (100% requirements fulfilled).
- Technical challenges encountered and resolutions implemented.
- Strategic architectural proposals: Migration to Amazon ECS Fargate, Amazon Bedrock Knowledge Bases integration, and CI/CD automation via AWS CodePipeline.

### PART 5: SELF-EVALUATION & PROFESSIONAL CAREER ROADMAP
- Self-assessment of work ethic, organizational discipline, and compliance.
- Evaluation of acquired cloud computing knowledge and engineering skills.
- Lessons learned in system architecture, risk management, and cutting-edge AI adoption.
- Career roadmap: Pursuing professional certification as an AWS Certified Solutions Architect and AI/ML Engineer.

### PART 6: CONCLUSION, RECOMMENDATIONS & REFERENCES
- Overall internship synthesis and final remarks.
- Educational curriculum recommendations for HUCE Faculty of Information Technology.
- List of 12 official academic whitepapers and software documentations.
- **Formal Completion Confirmation Frame** according to HUCE Form TTTN-06 standards.

---

## 3. Empirical Performance Benchmark Data (Table 3.4)

| Experimental Task | Sample Document | Engine Executed | Processing Time | Operating Cost |
| :--- | :--- | :--- | :--- | :--- |
| **Digital PDF Parsing** | `cv.pdf` (1 page) | Fast-Path Native (PyMuPDF) | **0.31 seconds** | **$0.00 USD** |
| **Scientific Paper Extraction** | `28_Bai_Bao.pdf` (11 pages) | Fast-Path Native (PyMuPDF) | **3.07 seconds** (~0.28s/page) | **$0.00 USD** |
| **Scanned Form OCR** | Scanned invoice photo | Vision OCR (Kaggle / Gemini) | **2.54 seconds** | **$0.00 USD (Free Tier)** |
| **Technical Translation** | `cv.pdf` (EN -> VI) | DocumentTranslator (LLM) | **2.80 seconds** | **$0.00 USD (Free Tier)** |
| **Microsoft Word Export** | Post-translation result | DocxExporter Module | **0.15 seconds** | **$0.00 USD** |

---

## 4. AWS Services Mapping Matrix (Table 3.3)

| AWS Service | Architectural Role | Technical Advantage |
| :--- | :--- | :--- |
| **Amazon VPC** | Isolated private network | Three-tier subnet segregation (Public, Private, Isolated DB) guaranteeing network defense |
| **Amazon EC2** | FastAPI web application host | Production Web Studio runtime under AWS Free Tier (t2.micro) |
| **Application Load Balancer** | Traffic distribution | Elastic HTTP/HTTPS load balancing with proactive target group health checks |
| **AWS Systems Manager** | Secure remote management | Zero-open-port shell access eliminating Bastion hosts, secure secrets storage |
| **Amazon S3** | Cloud object storage | Durable document and asset persistence with 99.999999999% reliability |
| **Amazon DynamoDB** | Serverless NoSQL database | Real-time session state management with sub-10ms response latency |
| **AWS Lambda** | Event-driven compute | On-demand document processing workflows without persistent server overhead |
| **Amazon CloudWatch** | Monitoring and alerts | Centralized log ingestion, real-time CPU/RAM metric aggregation, and SNS alerting |
