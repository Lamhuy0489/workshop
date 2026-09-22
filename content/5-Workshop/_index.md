---
title: "Workshop"
date: 2026-09-23
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Hands-On Workshop: Building & Deploying Serverless Hybrid Document OCR, Parsing & Technical Translation Platform on AWS

#### Workshop Series Overview
In this hands-on workshop series, you will build, configure, and operationalize a complete enterprise-grade **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** on Amazon Web Services (AWS).

The system seamlessly combines an **Enterprise Three-Tier Networking Architecture** with an **Event-Driven Serverless Pipeline**:
* **Traffic Ingress Layer**: Multi-AZ Application Load Balancer (ALB) ingesting HTTP port 80 traffic, providing a public live DNS endpoint for Internet users.
* **Application Compute Layer**: Amazon EC2 virtual host (Amazon Linux 2023, t2.micro) protected behind chained security groups, operating a systemd Gunicorn daemon on internal port 5000, managed via AWS Systems Manager Session Manager with zero open SSH ports.
* **Storage & Database Layer**: Amazon S3 object storage for raw input documents and exported artifacts; Amazon DynamoDB NoSQL table operating in On-Demand capacity mode for job audit logging.
* **Configuration & Secrets Management**: Centralized KMS-encrypted parameter storage via AWS Systems Manager Parameter Store (`SecureString`).
* **Event-Driven Serverless Pipeline**: Real-time asynchronous ingestion triggered via Amazon S3 Event Notification -> AWS Lambda -> Amazon DynamoDB -> Amazon CloudWatch Logs at strictly $0.00 operational cost.

---

#### 12 Hands-On Workshop Modules

1. [Module 5.1: Workshop Overview & Solution Architecture](5.1-Workshop-overview/)
2. [Module 5.2: Environment Prerequisites](5.2-Prerequiste/)
3. [Module 5.3: Project Foundation & Web Studio Architecture](5.3-Project-foundation/)
4. [Module 5.4: Multi-AZ VPC & Defense-in-Depth Security](5.4-VPC/)
   * [5.4.1 Provisioning Multi-AZ Amazon VPC](5.4-VPC/5.4.1-create-vpc/)
   * [5.4.2 Configuring Network Routing & Chained Security Groups](5.4-VPC/5.4.2-configure-network/)
5. [Module 5.5: Cloud Storage, Database & Parameter Services](5.5-Application-Services/)
   * [5.5.1 Provisioning Amazon DynamoDB Table](5.5-Application-Services/5.5.1-configure-amazon-dynamodb/)
   * [5.5.2 Provisioning Amazon S3 Storage Bucket](5.5-Application-Services/5.5.2-configure-amazon-s3/)
   * [5.5.3 Secure Parameter Management via AWS SSM Parameter Store](5.5-Application-Services/5.5.3-configure-ssm-parameter-store/)
6. [Module 5.6: Containerization with Docker & Amazon ECR](5.6-Containerization/)
   * [5.6.1 Building OCI-Compliant Dockerfile](5.6-Containerization/5.6.1-build-docker-image/)
   * [5.6.2 Publishing Docker Image to Amazon ECR](5.6-Containerization/5.6.2-push-image-to-ecr/)
7. [Module 5.7: Web Studio Deployment & Application Load Balancer](5.7-Deploy-Application/)
   * [5.7.1 Provisioning Target Group & Application Load Balancer](5.7-Deploy-Application/5.7.1-configure-load-balancer/)
   * [5.7.2 Deploying EC2 Application Server via SSM Session Manager](5.7-Deploy-Application/5.7.2-deploy-application-server/)
8. [Module 5.8: Public DNS Routing & Internet Ingress Verification](5.8-Domain-and-HTTPS/)
   * [5.8.1 DNS Resolution & Public Endpoint Validation](5.8-Domain-and-HTTPS/5.8.1-configure-public-dns/)
9. [Module 5.9: Event-Driven Serverless Automation Pipeline](5.9-CI-CD/)
   * [5.9.1 Establishing S3 Event Notification to AWS Lambda](5.9-CI-CD/5.9.1-configure-event-driven-pipeline/)
10. [Module 5.10: System Observability via Amazon CloudWatch](5.10-Monitoring/)
    * [5.10.1 Configuring CloudWatch Logs & Metrics](5.10-Monitoring/5.10.1-configure-cloudwatch/)
11. [Module 5.11: End-to-End System Testing & Benchmark Validation](5.11-Testing/)
12. [Module 5.12: FinOps Cost Governance & Safe Resource Teardown](5.12-Cleanup/)