---
title: "Deploy Application"
date: 2026-09-23
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### Module Objective

Deploy the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** onto AWS cloud infrastructure according to an enterprise three-tier architecture, combining an Application Load Balancer (ALB) with an Amazon EC2 application host administered securely via AWS Systems Manager Session Manager.

---

## 1. Deployment Architecture Overview

The Web Studio hosting architecture is designed for fault tolerance, security group chaining, and keyless administration:

1. **Application Load Balancer (`huylam-ocr-alb`)**:
   * Accepts incoming HTTP port 80 traffic from global Internet clients.
   * Balances traffic across multiple Availability Zones (Multi-AZ) while monitoring host health targeting `/login`.
2. **Target Group (`huylam-ocr-tg`)**:
   * Routes incoming requests from the load balancer to internal port 5000 on the application host.
3. **Application Host (Amazon EC2 `huylam-ocr-web-server`)**:
   * Executes a t2.micro instance running Amazon Linux 2023 inside `huylam-vpc`.
   * Bound to IAM Instance Profile `huylam-ssm-role`, enabling native S3 and DynamoDB integration without hardcoded credentials.
   * Administered exclusively through **AWS Systems Manager Session Manager**, eliminating public SSH port 22 exposure.
   * Operates the Gunicorn WSGI server as a managed **systemd background service** (`huylam-ocr.service`), ensuring automatic process respawning and boot persistence.

---

## 2. Hands-on Execution Steps

This module comprises three hands-on sections:

- **[5.7.1 Provisioning Target Group & Application Load Balancer](5.7.1-configure-load-balancer/)**: Creating Target Group on port 5000, fine-tuning health checks, and launching Multi-AZ ALB.
- **[5.7.2 Deploying EC2 Application Server via SSM Session Manager](5.7.2-deploy-application-server/)**: Creating IAM Role, launching EC2, connecting via Session Manager, setting up Python 3.11, and activating the systemd Gunicorn service.
- **[5.7.3 Configuring Admin & Kaggle GPU OCR](5.7.3-configure-admin-and-kaggle-ocr/)**: Launching Qwen2.5-VL vision inference on Kaggle GPU, establishing Cloudflare Tunnel, and registering the endpoint into Web Studio.

---

## 3. Expected Outcomes

Upon completing this module, you have:
- An operational Application Load Balancer `huylam-ocr-alb` in **Active** status.
- Target Group `huylam-ocr-tg` reporting **Healthy 1/1** status.
- EC2 host executing Web Studio reliably on internal port 5000.
- Infrastructure primed for global Internet traffic routing.