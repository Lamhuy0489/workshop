---
title: "Workshop"
date: 2026-09-18
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building an Enterprise Agentic RAG Platform on AWS

#### Overview
In this workshop, we will build and deploy a comprehensive **Enterprise Agentic RAG** platform on AWS using a Serverless and Cloud-Native architecture.

The platform unifies advanced Retrieval-Augmented Generation (RAG) with autonomous AI Agents capable of multi-step reasoning and automated operational tool execution:
- **Agent Orchestration Core**: Deployed on **AWS Lambda** (Python) using the ReAct (Reasoning + Acting) loop.
- **Credential Security Layer**: Managed securely via **AWS Systems Manager Parameter Store (SecureString)**.
- **Knowledge & State Storage**: Raw files stored in **Amazon S3**, conversation memory stored in **Amazon DynamoDB** with TTL cleanup.
- **Communication & Delivery**: Managed REST API via **Amazon API Gateway** and client distribution via **Amazon S3 + CloudFront**.
- **Observability**: Real-time logging, latency measurement, and token tracking with **Amazon CloudWatch**.

#### Workshop Structure

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequisite/)
3. [Knowledge Base Storage with Amazon S3](5.3-Knowledge-Base-S3/)
4. [Session Memory Management with Amazon DynamoDB](5.4-DynamoDB-Memory/)
5. [Credential Management with AWS Systems Manager](5.5-SSM-Secrets/)
6. [Agent Controller Implementation on AWS Lambda](5.6-Lambda-Agent/)
7. [API Management with Amazon API Gateway](5.7-API-Gateway/)
8. [Frontend Delivery with CloudFront and S3](5.8-Frontend-CDN/)
9. [Observability & Monitoring with Amazon CloudWatch](5.9-Monitoring/)
10. [End-to-End Testing & Validation](5.10-Testing/)
11. [Resource Cleanup](5.11-Cleanup/)