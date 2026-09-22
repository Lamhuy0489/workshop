---
title: "Resource Cleanup & FinOps Governance"
date: 2026-09-23
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

### Practical Objectives

Provide a methodical, orderly, and secure resource teardown guide following the completion of project evaluation, preserving the cloud budget and maintaining zero recurring operational expense (0.00 USD) in accordance with FinOps principles.

> **CRITICAL NOTE:**
> The teardown instructions below should ONLY be executed after academic evaluation, grading, and demonstration are fully finished. If the system is currently undergoing active demonstration or live grading, maintain all services in their LIVE state so evaluators can access the application via the Application Load Balancer.

---

## 1. Recommended Teardown Hierarchy

To avoid dependency violation errors, delete AWS resources starting from the perimeter edge down to the core networking layer:

1. **Application Load Balancer & Target Group** (Traffic perimeter - Priority deletion because ALBs incur hourly runtime fees).
2. **Amazon EC2 Instance** (Application compute host).
3. **Amazon S3 Bucket & Objects** (Document storage layer).
4. **Amazon DynamoDB Table** (NoSQL metadata layer).
5. **AWS Lambda Function** (Serverless event handler).
6. **AWS Systems Manager Parameter Store** (Secret parameter management).
7. **Amazon CloudWatch Alarms & Log Groups** (Observability artifacts).
8. **IAM Roles & Instance Profiles** (Security access controls).
9. **Security Groups & VPC** (Base virtual network).

---

## 2. Step-by-Step Cleanup Procedure

### Step 2.1: Delete Application Load Balancer and Target Group

The Application Load Balancer incurs a fixed cost of approximately $0.0225/hour (~$16/month). It should be deleted first after live evaluation:

1. Navigate to **EC2 Console -> Load Balancing -> Load Balancers**.
2. Select `huylam-ocr-alb`, choose **Actions -> Delete load balancer**.
3. Type the confirmation phrase and click **Delete**.
4. Switch to **Target Groups**, select `huylam-ocr-tg`, choose **Actions -> Delete**.

---

### Step 2.2: Stop or Terminate the Amazon EC2 Instance

1. Navigate to **EC2 Console -> Instances**.
2. Select the instance `huylam-ocr-ec2` (`i-0566e1eedaacea52d`).
3. Click **Instance state**:
   - To pause without incurring compute charges: Select **Stop instance**.
   - To remove permanently: Select **Terminate instance**.
4. Confirm the action. Attached EBS root volumes will be released.

---

### Step 2.3: Empty and Delete the Amazon S3 Bucket

1. Navigate to **Amazon S3 -> Buckets**.
2. Select bucket **`huylam-ocr-documents-ap-southeast-1`**.
3. Click **Empty** to remove all objects in `uploads/` and `outputs/`.
4. Enter `permanently delete` to confirm.
5. Once emptied, click **Delete**, re-enter the bucket name, and confirm deletion.

---

### Step 2.4: Delete the Amazon DynamoDB Table

1. Navigate to **Amazon DynamoDB -> Tables**.
2. Select the table **`document_processing_jobs`**.
3. Click **Delete table**.
4. Uncheck CloudWatch alarm backup if not needed, type `confirm`, and click **Delete**.

---

### Step 2.5: Delete the AWS Lambda Function

1. Navigate to **AWS Lambda -> Functions**.
2. Select function **`huylam-ocr-processor`**.
3. Click **Actions -> Delete**.
4. Confirm deletion.

---

### Step 2.6: Delete the SSM Parameter Store Entry

1. Navigate to **AWS Systems Manager -> Parameter Store**.
2. Select parameter **`/huylam-ocr/config`**.
3. Click **Delete** and confirm.

---

### Step 2.7: Delete CloudWatch Alarms and Log Groups

1. Navigate to **Amazon CloudWatch -> Alarms**:
   - Select `huylam-ocr-ec2-high-cpu`, click **Actions -> Delete**.
2. Switch to **Log groups**:
   - Select `/aws/lambda/huylam-ocr-processor`, click **Actions -> Delete log group(s)**.

---

### Step 2.8: Delete IAM Roles and Security Groups

1. Navigate to **IAM Console -> Roles**:
   - Delete `huylam-ocr-ec2-role` and `huylam-ocr-lambda-role`.
2. Navigate to **VPC Console -> Security Groups**:
   - Delete `huylam-web-sg` first, then delete `huylam-alb-sg`.

---

### Step 2.9: Delete the Virtual Private Cloud (huylam-vpc)

1. Navigate to **VPC Console -> Your VPCs**.
2. Select **`huylam-vpc`**.
3. Click **Actions -> Delete VPC**.
4. The console displays all associated subnets, route tables, and internet gateways scheduled for deletion.
5. Type `delete` to finalize complete network decommissioning.

---

## 3. FinOps Verification and Zero-Spend Audit

After completing the cleanup:
1. Navigate to **AWS Billing and Cost Management -> Cost Explorer**:
   - Check the Daily Spend view to confirm no residual active billing curves.
2. Check **AWS Budgets**:
   - Verify that the $10.00 zero-breach budget remains at $0.00 actual cost.

---

## 4. Expected Result

After executing this teardown sequence:
- All experimental and workshop AWS resources are cleanly terminated.
- Eliminates any risk of unexpected billing from hourly infrastructure services.
- Demonstrates mastery of Cloud Lifecycle Management from provisioning to decommissioning following FinOps standards.