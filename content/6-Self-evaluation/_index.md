---
title: "Self-Evaluation"
date: 2026-09-23
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

### Intern Information

- **Full Name**: Lam Quang Huy
- **Student ID**: 0212267
- **Class**: 67CS (Faculty of Information Technology)
- **Institution**: Hanoi University of Civil Engineering (HUCE)
- **Internship Program**: AWS First Cloud AI Journey (FCAJ) Bootcamp 2026
- **Graduation Project**: Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS

---

## 1. Summary of the AWS FCAJ 2026 Journey

Across the 12-week intensive curriculum of the **AWS First Cloud AI Journey (FCAJ)**, I completed an end-to-end technical progression spanning foundational AWS cloud infrastructure services to enterprise-grade Generative AI integrations:
- **Weeks 1 to 4**: Provisioned AWS Identity and Access Management (IAM), managed Amazon EC2 instances running Amazon Linux 2023, constructed isolated network boundaries with Amazon Virtual Private Cloud (Amazon VPC), established Internet Gateway routing, and segmented Multi-AZ subnets.
- **Weeks 5 to 8**: Configured object storage via Amazon S3, set up Amazon DynamoDB in On-Demand mode, managed cloud instances adhering to Zero Trust standards using AWS Systems Manager (Fleet & Session Manager), authored production Dockerfiles, and pushed OCI containers to Amazon Elastic Container Registry (Amazon ECR).
- **Weeks 9 to 12**: Architected the complete capstone solution, built the Flask Single-Page Application Web Studio, deployed Application Load Balancers with Chained Security Groups, engineered an Event-Driven Serverless ingestion pipeline (S3 -> Lambda -> DynamoDB), and integrated Kaggle GPU computing clusters ($0.00 OCR) alongside closed-loop AWS Native AI options (Amazon Bedrock & Amazon Textract).

---

## 2. Learning Outcomes & Competency Evaluation Matrix

Below is a detailed self-evaluation matrix benchmarking technical competencies against internship learning goals:

| No. | Technical Competency Dimension | Excellent | Good | Satisfactory | Practical Evidence |
| :---: | :--- | :---: | :---: | :---: | :--- |
| 1 | AWS Cloud Architecture Design | [x] | [ ] | [ ] | Formulated an enterprise Three-Tier Multi-AZ model integrated with an Event-Driven Serverless pipeline adhering to the Well-Architected Framework. |
| 2 | Cloud Networking Administration (VPC / Subnet / SG) | [x] | [ ] | [ ] | Provisioned VPC `10.0.0.0/16`, 2 Multi-AZ public subnets, Internet Gateway, and Chained Security Groups isolating EC2 port 5000 from public ingress. |
| 3 | Host Administration & Zero Trust Security (SSM) | [x] | [ ] | [ ] | Eliminated SSH port 22 and bastion hosts; enforced Session Manager console access authenticated via IAM role `huylam-ssm-role`. |
| 4 | Containerization & Registry Management (Docker/ECR) | [x] | [ ] | [ ] | Authored lightweight OCI container image using `python:3.11-slim`, verified locally, and published to Amazon ECR repository `huylam-web-app`. |
| 5 | Event-Driven Serverless Pipeline Engineering | [x] | [ ] | [ ] | Connected S3 Event `s3:ObjectCreated:*` to AWS Lambda `huylam-ocr-processor`, demonstrating sub-second response times (214 ms). |
| 6 | FinOps & Cost Optimization | [x] | [ ] | [ ] | Operated cloud infrastructure with a strict $0.00 budget within AWS Free Tier; leveraged external Kaggle GPUs for zero-cost OCR vision inference. |
| 7 | Technical Documentation & Workshop Authoring | [x] | [ ] | [ ] | Authored 12 bilingual workshop modules in Hugo, augmented with precise red bounding box annotations on all console screenshots. |
| 8 | Independent Problem Solving & Algorithmic Design | [x] | [ ] | [ ] | Engineered Fast-Path PyMuPDF vector extraction (0.1s/page) and an automated Round-Robin API key failover manager. |
| 9 | Discipline, Accountability & Delivery Velocity | [x] | [ ] | [ ] | Delivered 12 weekly worklog submissions, published 3 deep-dive technical blogs, and fulfilled all project milestones ahead of schedule. |
| 10 | Professional Engineering Standards | [x] | [ ] | [ ] | Followed enterprise infrastructure practices, managed versioned Git repositories, and automated CI/CD deployment pipelines. |

---

## 3. Evaluation Across the 5 Pillars of AWS Well-Architected Framework

### 3.1. Operational Excellence
- **Automated Deployments**: The entire internship report site is version-controlled on GitHub (`Lamhuy0489/workshop`) and compiled automatically via GitHub Actions pipelines targeting GitHub Pages.
- **Unified Observability**: Established CloudWatch Log Group `/aws/lambda/huylam-ocr-processor`, collected Target Group HealthyHostCount telemetry, and configured CloudWatch alarms for real-time fault detection.
- **Operations as Code**: Web Studio runs as a systemd background service `huylam-ocr.service`, paired with automated deployment shell scripts (`deploy.sh`) supporting zero-downtime updates.

### 3.2. Security
- **Least Privilege Access**: Strictly separated host permissions (`huylam-ssm-role`) from serverless execution policies (`huylam-ocr-lambda-role`). Stored zero static credentials on instances, relying exclusively on AWS STS temporary tokens.
- **Chained Security Groups**: EC2 instances accept TCP port 5000 ingress strictly from the ALB Security Group (`huylam-alb-sg`), preventing port scanning and brute-force intrusion attempts from the public Internet.
- **Encryption at Rest**: Enforced Server-Side Encryption (SSE-S3 AES-256) on all documents stored in Amazon S3 buckets. Sensitive API configurations are stored as SecureString entries in AWS Systems Manager Parameter Store.

### 3.3. Reliability
- **Multi-AZ Availability Partitioning**: The Application Load Balancer spans across 2 distinct Availability Zones (`ap-southeast-1a` and `ap-southeast-1b`), ensuring automated failover if an individual data center encounters disruption.
- **Automated Fallback Mechanisms**: The OCR layer incorporates resilient failover from Kaggle GPU endpoints to Google Gemini Flash during connectivity degradation, ensuring uninterrupted document extraction.

### 3.4. Performance Efficiency
- **Intelligent Fast-Path Dispatching**: Over 80% of digital PDF documents are parsed in 0.1s to 0.3s per page using PyMuPDF vector extraction, bypassing expensive visual AI model inference entirely.
- **Serverless Scale-to-Zero**: Utilized AWS Lambda and Amazon DynamoDB On-Demand to achieve sub-second event ingestion while consuming zero compute resources when no upload activity is present.

### 3.5. Cost Optimization (FinOps)
- **Strict $0.00 Financial Discipline**: All storage, serverless compute, database, load balancing, and virtual machines operate strictly within AWS Free Tier entitlements.
- **Free High-End GPU Computing**: Connected Kaggle 2x NVIDIA T4 GPU clusters (32GB VRAM) via Cloudflare Tunnel, enabling state-of-the-art vision extraction for Qwen2.5-VL-7B without provisioning costly EC2 GPU instances (p3/g4dn).

---

## 4. Future Development & Career Roadmap

- **Professional Certifications**: Prepare for and achieve the **AWS Certified Solutions Architect – Associate (SAA-C03)** examination in the upcoming quarter, followed by the **AWS Certified Machine Learning – Specialty (MLS-C01)** certification.
- **Infrastructure as Code (IaC)**: Migrate all manual console provisioning procedures into declarative templates authored in **AWS CloudFormation** and **Terraform**.
- **Container Orchestration**: Transition the Web Studio container from standalone EC2 hosts to serverless container orchestration using **Amazon Elastic Container Service (Amazon ECS on AWS Fargate)**.
- **Community Engagement**: Actively contribute technical articles and presentations to the AWS User Group Vietnam community, focusing on Serverless architectures, FinOps strategies, and Generative AI integrations.

---

## 5. Conclusion

The **AWS First Cloud AI Journey 2026** internship served as a cornerstone experience in shaping my system architecture mindset, technical execution capabilities, and career path toward becoming a professional Cloud Solutions Architect. I extend my deepest gratitude to the AWS mentors, senior cloud engineers, and Hanoi University of Civil Engineering (HUCE) for their continuous guidance and dedicated support.