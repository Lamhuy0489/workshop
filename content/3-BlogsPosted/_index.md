---
title: "Blogs Posted"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Throughout the **AWS First Cloud AI Journey (FCAJ) Bootcamp 2026**, I authored and published **3 deep-dive technical blogs** on **LinkedIn** — sharing production-grade lessons, architectural patterns, and experimental benchmarks directly derived from my capstone project: **"Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS"**.

### Summary of Published LinkedIn Technical Blogs:

| # | Technical Article Title | Engineering Domain (FCAJ Framework) | Publication Date | Direct Link |
| :---: | :--- | :--- | :---: | :---: |
| **Blog 1** | **Architecting Chained Security Groups & Eliminating SSH Port 22 on AWS** | Cloud Security & Zero Trust Operations (ALB, Chained SG, SSM Session Manager) | 2026-09-23 | [View on LinkedIn](https://lnkd.in/p/giyjwMkE) |
| **Blog 2** | **FinOps in Document AI: Fast-Path Native Parsing (0.1s/page) & Zero-Cost Selective OCR** | Hybrid Architecture & FinOps Optimization (PyMuPDF, Kaggle GPU Qwen2.5-VL, Bedrock Failover) | 2026-09-23 | [View on LinkedIn](https://lnkd.in/p/gp_MnmkQ) |
| **Blog 3** | **Engineering an Event-Driven Serverless Pipeline on AWS: S3 to DynamoDB in 214 ms** | Serverless Event-Driven Orchestration (S3, Lambda, DynamoDB, CloudWatch, Docker/ECR) | 2026-09-23 | [View on LinkedIn](https://lnkd.in/p/gBfaVCdj) |

---

### [3.1 Blog 1: Cloud Security & Zero Trust Architecture](3.1-Blog1/)

Deep-dive into Defense-in-Depth cloud networking: Implementing Security Group Chaining to completely isolate EC2 application hosts behind Application Load Balancers (restricting TCP port 5000 strictly to `huylam-alb-sg`), while eliminating SSH port 22 and bastion hosts through AWS Systems Manager (SSM Session Manager).
- **LinkedIn Post URL**: [https://lnkd.in/p/giyjwMkE](https://lnkd.in/p/giyjwMkE)

---

### [3.2 Blog 2: Hybrid Parsing Engine & FinOps Optimization](3.2-Blog2/)

Overcoming latency and cost bottlenecks caused by excessive Vision AI calls: Building a Two-Tier Hybrid Processing Engine with Fast-Path PyMuPDF parsing 80%+ of digital pages in 0.1s - 0.3s/page at $0.00 cost, paired with selective GPU OCR via Kaggle 2x NVIDIA T4 and automated failover to Gemini Flash / Amazon Bedrock.
- **LinkedIn Post URL**: [https://lnkd.in/p/gp_MnmkQ](https://lnkd.in/p/gp_MnmkQ)

---

### [3.3 Blog 3: Event-Driven Serverless Pipeline](3.3-Blog3/)

Deconstructing an automated, sub-second ingestion pipeline: Ingesting documents into Amazon S3, triggering AWS Lambda `huylam-ocr-processor` via `s3:ObjectCreated:*`, generating job metadata, and writing tracking records into Amazon DynamoDB in 214 ms without polling servers, integrated with CloudWatch telemetry and OCI containerization.
- **LinkedIn Post URL**: [https://lnkd.in/p/gBfaVCdj](https://lnkd.in/p/gBfaVCdj)