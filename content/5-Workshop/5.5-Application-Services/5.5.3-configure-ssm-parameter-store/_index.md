---
title: "Configure AWS SSM Parameter Store"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

### Hands-on Objective

Centralize and protect platform configuration parameters and sensitive API keys using **AWS Systems Manager Parameter Store** as a KMS-encrypted `SecureString` parameter at path `/huylam-ocr/config`, eliminating the risk of hardcoded secrets.

---

## 1. Overview of AWS Systems Manager Parameter Store

AWS Systems Manager Parameter Store offers secure, hierarchical configuration management with integrated versioning:
* **SecureString Parameter Type**: Automatically encrypts sensitive payloads using **AWS Key Management Service (KMS)** with the default key `alias/aws/ssm`.
* **Standard Tier**: Cost-free within the AWS Free Tier, storing up to 10,000 parameters with sizes up to 4 KB each.
* **Granular IAM Authorization**: Only authorized principals (such as the EC2 instance attached with `huylam-ssm-role` or AWS Lambda) can read and decrypt the stored payload.

---

## 2. AWS Management Console Step-by-Step Procedure

### Step 2.1: Access Parameter Store
1. Sign in to the AWS Console in region **ap-southeast-1 (Singapore)**.
2. Navigate to: **Systems Manager -> Parameter Store -> Create parameter**.

---

### Step 2.2: Configure Parameter Properties
Enter the following parameters:

| Property | Configured Value | Architectural Role |
| :--- | :--- | :--- |
| **Name** | `/huylam-ocr/config` | Hierarchical configuration namespace |
| **Description** | `Master Configuration for Huylam OCR Platform` | Usage description |
| **Tier** | Standard | Zero-cost standard tier |
| **Type** | **SecureString** | KMS-encrypted secret parameter |
| **KMS Key source** | My current account | Retains default account KMS key |
| **KMS Key ID** | `alias/aws/ssm` | AWS default managed KMS alias |
| **Data type** | text | Plaintext JSON string |

---

### Step 2.3: Set Value Content
Paste the configuration JSON into the **Value** textarea:

```json
{
  "ocr_mode": "AUTO",
  "scan_threshold_chars": 50,
  "kaggle_endpoint": "https://huylam-ocr.trycloudflare.com",
  "gemini_api_key": "your-gemini-api-key-here",
  "aws_native_mode_enabled": false
}
```

Click **Create parameter**.

---

## 3. Verifying Parameter & Application Ingestion Flow

Once created, the parameter ARN is:
```text
arn:aws:ssm:ap-southeast-1:677994024390:parameter/huylam-ocr/config
```

![AWS Systems Manager Parameter Store SecureString Configuration Details](/images/week10/05-ssm-parameter-details.png)

### Application Retrieval Logic:
Inside `src/backend/aws/storage_service.py`, the system fetches and decrypts settings dynamically via Boto3:

```python
import boto3
import json

def get_system_config():
    ssm = boto3.client('ssm', region_name='ap-southeast-1')
    response = ssm.get_parameter(
        Name='/huylam-ocr/config',
        WithDecryption=True
    )
    return json.loads(response['Parameter']['Value'])
```

**Checkpoint**: Compute instances retrieve configuration and keys in memory upon initialization without persisting credentials on disk.

---

## 4. Expected Outcomes

Upon completing this section:
- Parameter `/huylam-ocr/config` is established as a KMS-encrypted `SecureString`.
- Centralized configuration allows seamless runtime mode toggling (Fast-Path, Vision OCR, AWS Native) without code redeployment.
- Enforces a strict Zero Hardcoded Credentials posture.