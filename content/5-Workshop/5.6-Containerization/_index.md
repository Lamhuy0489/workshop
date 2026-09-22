---
title: "Containerization"
date: 2026-09-23
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Module Objective

Package the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** into a standardized OCI Container using Docker on `python:3.11-slim`, optimize container storage overhead, and publish the artifact to **Amazon Elastic Container Registry (Amazon ECR)**.

---

## 1. Containerization Architecture Overview

Containerizing the Web Studio yields substantial cloud operational benefits:
- **Environment Parity**: Guarantees identical execution behavior for C-extension parsing bindings (`PyMuPDF`), Word export libraries (`python-docx`), Gunicorn WSGI workers, and AWS Boto3 SDKs across local workstations and cloud hosts.
- **Security & Storage Optimization**: Employs lightweight base image `python:3.11-slim`, non-root user permissions, and strict `.dockerignore` rules to strip ephemeral caches.
- **Enterprise Registry on Amazon ECR**: Amazon Elastic Container Registry provides encrypted storage and automated vulnerability assessment governed by AWS IAM.

---

## 2. Hands-on Execution Steps

This module comprises two hands-on sections:

- **[5.6.1 Building OCI-Compliant Dockerfile](5.6.1-build-docker-image/)**: Drafting `Dockerfile` and `.dockerignore`, building, and testing container runtime locally on port 5000.
- **[5.6.2 Publishing Docker Image to Amazon ECR](5.6.2-push-image-to-ecr/)**: Provisioning repository `huylam-web-app`, authenticating Docker CLI via AWS STS tokens, and pushing container images.

---

## 3. Expected Outcomes

Upon completing this module, you have:
- Production-ready `Dockerfile` and `.dockerignore` specifications for Python 3.11.
- Validated local Docker image `huylam-ocr-web-studio:latest`.
- Published container artifact in Amazon ECR (`677994024390.dkr.ecr.ap-southeast-1.amazonaws.com/huylam-web-app:latest`).
- Container assets primed for deployment on EC2 compute or Amazon ECS Fargate.