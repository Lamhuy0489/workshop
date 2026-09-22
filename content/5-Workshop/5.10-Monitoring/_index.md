---
title: "Monitoring"
date: 2026-09-23
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

### Goal

Establish and operate a unified monitoring solution for the Hybrid OCR & Translation Platform using Amazon CloudWatch, covering health check observability for Application Load Balancer and Target Group, host resource metrics for the EC2 application instance, and serverless execution logs for AWS Lambda.

---

## 1. Monitoring Architecture Overview

In a hybrid architecture combining persistent compute instances (EC2) with event-driven serverless services (Amazon S3, AWS Lambda, Amazon DynamoDB), centralized observability is essential for high availability, early anomaly detection, and FinOps governance.

Amazon CloudWatch serves as the observability backbone:
- **Target Group Metrics**: Continuous tracking of `HealthyHostCount`, `UnHealthyHostCount`, `TargetResponseTime`, and HTTP request counts via Application Load Balancer.
- **Compute Instance Metrics**: Real-time evaluation of `CPUUtilization`, `NetworkIn`/`NetworkOut` volumes, and hypervisor-level `StatusCheckFailed` metrics on the EC2 instance.
- **Serverless Logging & Metrics**: Centralized logging via CloudWatch Log Groups for the `huylam-ocr-processor` Lambda function, tracking invocation counts (`Invocations`), latency execution duration (`Duration`), and execution failures (`Errors`).
- **CloudWatch Alarms**: Proactive threshold alerting configured to transition to ALARM if EC2 CPU usage exceeds 80% or unexpected error spikes occur.

---

## 2. Detailed Practice Content

Complete the following lab module:

- **5.10.1 Configure Amazon CloudWatch**: Inspect serverless Lambda execution logs, monitor Target Group health, evaluate EC2 system metrics, and configure a CloudWatch CPU utilization alarm.

---

## 3. Expected Result

After completing this chapter, you will have:

- A dedicated CloudWatch Log Group `/aws/lambda/huylam-ocr-processor` streaming event records from S3 uploads.
- Real-time ALB Health Check verification ensuring the EC2 host remains `Healthy` in the Target Group.
- An active CloudWatch Alarm configured to alert on compute resource bottlenecks.
- Complete operational visibility across both server-based and serverless AWS resources.