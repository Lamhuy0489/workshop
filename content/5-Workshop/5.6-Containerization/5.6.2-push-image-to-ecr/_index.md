---
title: "Push Image to Amazon ECR"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

### Hands-on Objective

Provision an **Amazon Elastic Container Registry (Amazon ECR)** private repository named `huylam-web-app` in region `ap-southeast-1`, authenticate the local Docker CLI using an AWS STS authentication token, and publish the container image artifact to the AWS cloud.

---

## 1. Provisioning Amazon ECR Private Repository

Amazon ECR is a fully managed container registry providing built-in vulnerability scanning and integrated IAM authorization policies.

### AWS Management Console Procedure:
1. Sign in to the AWS Console in region **ap-southeast-1 (Singapore)**.
2. Navigate to: **Elastic Container Registry -> Repositories -> Create repository**.
3. Configure repository settings:

| Property | Configured Value | Architectural Rationale |
| :--- | :--- | :--- |
| **Visibility settings** | **Private** | Access restricted to authorized IAM entities |
| **Repository name** | `huylam-web-app` | Project container image repository identifier |
| **Tag immutability** | Disabled | Allows updating the `latest` tag across releases |
| **Scan on push** | Enabled | Automated vulnerability (CVE) scanning upon push |
| **KMS encryption** | AES-256 | Server-side encryption at rest |

4. Click **Create repository**.

![Amazon ECR Repositories Initial List](/images/week8/01-ecr-repositories-list-initial.png)

![Provision Private Amazon ECR Repository huylam-web-app](/images/week8/02-ecr-create-repository.png)

---

## 2. Authenticating Docker CLI with Amazon ECR

Docker requires an ephemeral authorization token granted by AWS STS before pushing images:

```bash
# Retrieve authentication token and login to Amazon ECR
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 677994024390.dkr.ecr.ap-southeast-1.amazonaws.com
```

**Expected Confirmation**:
```text
Login Succeeded
```

---

## 3. Tagging and Publishing Docker Image

### Step 3.1: Tag Local Image
Bind the Amazon ECR repository URI to the local image build:

```bash
docker tag huylam-ocr-web-studio:latest 677994024390.dkr.ecr.ap-southeast-1.amazonaws.com/huylam-web-app:latest
```

### Step 3.2: Push Image to Registry
Execute the image upload:

```bash
docker push 677994024390.dkr.ecr.ap-southeast-1.amazonaws.com/huylam-web-app:latest
```

Docker compresses and streams image layers to Amazon ECR. Due to the lightweight `python:3.11-slim` footprint, upload latency is minimal.

---

## 4. Verification on AWS Management Console

1. Navigate to **Amazon ECR -> Repositories -> huylam-web-app**.
2. Confirm the **`latest`** tag appears in the image list alongside its Image URI and compressed size.
3. Review **Vulnerabilities** report: confirms zero Critical security findings.

![Amazon ECR huylam-web-app Repository Details](/images/week8/03-ecr-repository-details-empty.png)

---

## 5. Expected Outcomes

Upon completing this section:
- Private Amazon ECR repository `huylam-web-app` is operational.
- Local Docker CLI authenticated successfully.
- Container image published to Amazon ECR, primed for deployment on Amazon ECS or Amazon EC2 compute instances.