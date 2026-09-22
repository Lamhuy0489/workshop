---
title: "Configure Amazon CloudWatch"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.10.1. </b> "
---

### Practical Objectives

Explore and implement a unified observability solution using Amazon CloudWatch for the Document OCR & Translation platform: Inspect execution logs in CloudWatch Logs for AWS Lambda, monitor real-time health check status of the Target Group, observe EC2 host compute metrics, and configure a CloudWatch Alarm for high CPU utilization.

---

## 1. Inspect CloudWatch Logs for AWS Lambda

The AWS Lambda function `huylam-ocr-processor` automatically sends standard output and execution diagnostics to CloudWatch Logs through its `AWSLambdaBasicExecutionRole` policy.

### Verification Steps:

1. Navigate to **AWS Console -> Amazon CloudWatch -> Log groups**.
2. Locate and select the log group: **`/aws/lambda/huylam-ocr-processor`**.

![CloudWatch Log Group Overview](/images/week11/14-cloudwatch-log-group-overview.png)

3. Under **Log streams**, select the latest stream generated upon document upload to Amazon S3.
4. Review the detailed log event records:
   - Event payload detection: `Nhan su kien moi tu Amazon S3 Event Notification`.
   - Object key resolution: `Phat hien tep moi: s3://huylam-ocr-documents-ap-southeast-1/uploads/...`.
   - Database item persistence: `Da luu tien trinh vao DynamoDB (job_id: auto-...)`.
   - Execution report summary (REPORT):
     - **Duration**: `214.28 ms`
     - **Billed Duration**: `215 ms`
     - **Memory Size**: `128 MB`
     - **Max Memory Used**: `88 MB`

![CloudWatch Log Events Execution](/images/week11/15-cloudwatch-log-events-execution.png)

An execution duration of just 214 ms confirms the responsiveness and cost-efficiency of the serverless event trigger design.

---

## 2. Monitor Target Group Health Status (huylam-ocr-tg)

The Application Load Balancer issues periodic health check HTTP requests to port 5000 of the EC2 instance using the path `/login`.

1. Navigate to **EC2 Console -> Load Balancing -> Target Groups**.
2. Select Target Group **`huylam-ocr-tg`**.
3. Under the **Targets** tab, verify the **Target health** metrics:
   - **Healthy**: `1`
   - **Unhealthy**: `0`
   - **Target ID**: `i-0566e1eedaacea52d:5000`
   - **Health status details**: `Target is healthy (Received response code: 200/302)`.

![Target Group Healthy Status](/images/week12/08-target-group-healthy-status.png)

With a Healthy state confirmed, the ALB transparently distributes inbound web requests to the running Gunicorn application server.

---

## 3. Observe EC2 Compute Instance Metrics

1. Navigate to **EC2 Console -> Instances -> huylam-ocr-ec2 (`i-0566e1eedaacea52d`)**.
2. Open the **Monitoring** tab to review CloudWatch hypervisor metrics:
   - **CPUUtilization**: Fluctuates below 5% at idle, with brief spikes to 15% - 25% during document rendering and artifact generation.
   - **NetworkIn / NetworkOut**: Corresponds directly to file upload ingress and HTTP response egress.
   - **StatusCheckFailed (Instance / System)**: Stays at `0` continuously, indicating hardware and hypervisor operational integrity.

---

## 4. Configure CloudWatch Alarm for High CPU Utilization

To automatically detect sustained high compute loads or performance bottlenecks on the EC2 host, configure a CloudWatch Alarm.

### Configuration Steps:

1. Navigate to **CloudWatch Console -> Alarms -> All alarms -> Create alarm**.
2. Click **Select metric -> EC2 -> Per-Instance Metrics**:
   - Choose metric: **`CPUUtilization`** for Instance ID `i-0566e1eedaacea52d`.
3. Configure alarm conditions:
   - **Statistic**: `Average`.
   - **Period**: `5 minutes`.
   - **Threshold type**: `Static`.
   - **Whenever CPUUtilization is**: `Greater/Equal` (`>=`).
   - **than**: `80`.
4. Configure notification actions:
   - Trigger condition: **In alarm**.
   - Optional: Attach an Amazon SNS topic for email dispatch.
5. Define alarm metadata:
   - **Alarm name**: `huylam-ocr-ec2-high-cpu`.
   - **Alarm description**: `Alert when EC2 instance CPU utilization exceeds 80% for 5 consecutive minutes`.
6. Review specifications and click **Create alarm**.

7. Once created, the alarm list displays status **OK**, verifying that host CPU utilization on instance `huylam-ocr-ec2` is well within expected operational parameters below 80%.

---

## 5. Expected Result

After completing this section, you have successfully:

- Verified serverless execution logging in CloudWatch Logs with detailed latency and memory analytics.
- Monitored application server availability through continuous ALB Target Group health checks.
- Created an automated CloudWatch Alarm guarding against compute saturation.
- Established enterprise-grade observability and proactive operational awareness across your AWS deployment.