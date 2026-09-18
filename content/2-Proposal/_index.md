---
title: "Proposal"
date: 2026-09-18
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Enterprise Agentic RAG Platform on AWS

## Intelligent Knowledge Assistant & Operational Tool Orchestration on AWS

---

# 1. Executive Summary

**Enterprise Agentic RAG Platform on AWS** is a next-generation generative AI solution that bridges Retrieval-Augmented Generation (RAG) with autonomous reasoning agents (AI Agents). The platform empowers organizations to seamlessly query internal documentation and automatically orchestrate enterprise operational tools (database lookups, ticket status tracking, automated alerts) using natural language.

The platform is designed following a Serverless and Cloud-Native architecture on AWS to achieve zero operating overhead during initial testing, seamless elastic scalability, and robust enterprise-grade security:
- **Agent Orchestration Layer**: Powered by **AWS Lambda** (Python) using the ReAct (Reasoning + Acting) paradigm with LangChain / LangGraph.
- **Credential Security Layer**: Managed centrally via **AWS Systems Manager Parameter Store (SecureString)** encrypted with **AWS KMS**, completely preventing API key exposure.
- **Knowledge & State Storage Layer**: Raw enterprise files stored in **Amazon S3**; conversation session memory persisted in **Amazon DynamoDB** with automated Time-to-Live (TTL) cleanup.
- **Delivery & API Layer**: Endpoints exposed via **Amazon API Gateway** (supporting CORS and rate limiting) and fronted globally with a static web client deployed on **Amazon S3 + Amazon CloudFront** with SSL/TLS certificates provided by **AWS Certificate Manager (ACM)**.
- **Model Integration Layer**: Decoupled integration with external LLMs such as Google Gemini 1.5 Flash or Groq via a provider-agnostic interface, strictly adhering to the AWS Well-Architected Cost Optimization pillar.

---

# 2. Problem Statement & Proposed Solution

## 2.1. The Problem
1. **Limitations of Naive RAG**: Traditional RAG architectures only perform similarity search and blindly inject retrieved chunks into prompts, leading to hallucinations, inability to handle multi-step queries, and zero ability to execute operational actions.
2. **Infrastructure Cost Barriers**: Self-hosting large language models or maintaining dedicated GPU instances incurs high fixed monthly costs, making it unviable for student labs or small enterprise pilots.
3. **Security Risks**: Inexperienced implementations frequently hard-code credentials in local configuration files or git repositories, violating cloud security standards.

## 2.2. The Solution
This project implements an end-to-end **Agentic RAG** system on AWS:
- **Autonomous Reasoning**: The agent dynamically parses incoming queries and decides whether to consult S3 internal documents or execute DynamoDB business database queries.
- **Enterprise Security Standards**: All credentials are encrypted in SSM Parameter Store, and internal communications strictly observe IAM Least Privilege principles.
- **Cost Efficiency**: Leveraging 100% Serverless services within the AWS Perpetual Free Tier alongside free-tier external model APIs to keep runtime costs at 0 USD.

---

# 3. Architecture Diagram

```text
[ Web Browser / User ]
           │
           ▼ HTTPS (SSL/TLS)
[ Amazon CloudFront + Amazon S3 Static Hosting ] (Web Client Interface)
           │
           ▼ REST API Request
[ Amazon API Gateway ] (Endpoint Management & Rate Limiting)
           │
           ▼ Invoke
[ AWS Lambda: Agent Controller Core ]
     │
     ├── 1. Retrieve API Key securely ─> [ AWS SSM Parameter Store (SecureString) ]
     │
     ├── 2. Persist session memory ────> [ Amazon DynamoDB: Chat History & Sessions ]
     │
     ├── 3. Execute Agent Tools:
     │      │
     │      ├── Tool 1: Knowledge Search ─> [ Amazon S3 + Vector Store (FAISS) ]
     │      └── Tool 2: Order/Ticket Lookup > [ Amazon DynamoDB (Business Table) ]
     │
     └── 4. Forward Prompt + Context ──> [ External LLM: Google Gemini / Groq ]
           │
           ▼
[ Amazon CloudWatch ] (Centralized Logging, Latency, and Token Metrics)
```

---

# 4. AWS Services Utilized

| AWS Service | System Role | Selection Rationale |
| :--- | :--- | :--- |
| **AWS Lambda** | Agent Reasoning & Compute Core | Serverless, auto-scaling, 1 million free invocations per month |
| **Amazon S3** | Knowledge Base & Static Web Hosting | 11 9s durability, seamless static website and vector index hosting |
| **Amazon DynamoDB** | Conversation Memory & Business Database | Single-digit millisecond latency, automated TTL cleanup |
| **AWS Systems Manager** | Secure Credential Storage | Encrypted parameter store at zero cost |
| **Amazon API Gateway** | Public REST API Gateway | Managed endpoint, built-in CORS, throttling, and routing |
| **Amazon CloudFront** | Global Content Delivery Network | Accelerates web asset delivery, free HTTPS via ACM |
| **Amazon CloudWatch** | Monitoring, Logging & Alarms | Detailed execution tracking, error alerts, and latency telemetry |

---

# 5. Implementation Roadmap

- **Weeks 1 - 2**: AWS account setup, IAM security hardening, AWS Budgets, and AWS CLI setup.
- **Weeks 3 - 4**: S3 document storage configuration, DynamoDB chat session schema design.
- **Weeks 5 - 6**: AWS Systems Manager integration and tool lambda creation.
- **Weeks 7 - 8**: ReAct Agent controller loop completion and API Gateway linkage.
- **Weeks 9 - 10**: Frontend web client deployment on S3 + CloudFront and CloudWatch logging integration.
- **Weeks 11 - 12**: Comprehensive scenario testing, technical documentation, and final report delivery.