---
title: "Application Services"
date: 2026-09-23
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Module Objective

Configure core cloud storage and database services on AWS for the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**, including Amazon DynamoDB NoSQL database, Amazon S3 object storage, and AWS Systems Manager Parameter Store.

---

## 1. Storage & Database Architecture Overview

The platform leverages three AWS Cloud-Native services to guarantee secure, high-durability persistence while maintaining a strict $0.00 operational expenditure:

1. **Amazon DynamoDB (`document_processing_jobs`)**:
   * Serverless NoSQL table storing document processing states, file metadata (byte size, MIME type, page count), and processing latencies.
   * Operates in **On-Demand (`PAY_PER_REQUEST`)** billing mode, delivering single-digit millisecond latency with zero idle cost.
2. **Amazon S3 (`huylam-ocr-documents-ap-southeast-1`)**:
   * High-durability (99.999999999%) object storage partitioned into `uploads/` for raw user assets and `outputs/` for exported artifacts.
   * Configured with Cross-Origin Resource Sharing (CORS) rules for direct, secure uploads.
3. **AWS Systems Manager Parameter Store (`/huylam-ocr/config`)**:
   * Centralized configuration registry storing system operational modes and API keys as encrypted `SecureString` parameters backed by AWS KMS, eliminating hardcoded credentials.

---

## 2. Hands-on Execution Steps

This module comprises four step-by-step sections:

- **[5.5.1 Provisioning Amazon DynamoDB Table](5.5.1-configure-amazon-dynamodb/)**: Creating table `document_processing_jobs` with Partition Key `job_id` and Sort Key `created_at`.
- **[5.5.2 Provisioning Amazon S3 Storage Bucket](5.5.2-configure-amazon-s3/)**: Provisioning bucket `huylam-ocr-documents-ap-southeast-1`, establishing folder prefixes `uploads/`, `outputs/`, and applying CORS.
- **[5.5.3 Secure Parameter Management via AWS SSM Parameter Store](5.5.3-configure-ssm-parameter-store/)**: Storing KMS-encrypted configuration JSON at `/huylam-ocr/config`.
- **[5.5.4 Configuring Amazon Cognito & Google OAuth 2.0](5.5.4-configure-amazon-cognito/)**: Provisioning User Pool `huylam-ocr-user-pool`, integrating Google as a federated identity provider, and testing SSO authentication on Web Studio.

---

## 3. Expected Outcomes

Upon completing this module, you have:
- An active Amazon DynamoDB table `document_processing_jobs`.
- An Amazon S3 bucket with structured prefixes and secure CORS policies.
- A centralized `SecureString` parameter `/huylam-ocr/config` managed in AWS SSM.
- A production-grade Amazon Cognito User Pool federated with Google OAuth 2.0 operating at $0.00 cost.