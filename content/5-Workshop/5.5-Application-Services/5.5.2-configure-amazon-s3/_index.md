---
title: "Configure Amazon S3"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

### Hands-on Objective

Provision and configure an **Amazon S3 Bucket** named `huylam-ocr-documents-ap-southeast-1` in region `ap-southeast-1`, establish functional prefixes `uploads/` and `outputs/`, and apply secure CORS policies for the Web Studio platform.

---

## 1. Provisioning Amazon S3 Bucket

Amazon Simple Storage Service (Amazon S3) provides 99.999999999% (11 9's) data durability and virtually unlimited scalability:

### Step-by-Step Procedure:
1. Navigate to: **Amazon S3 -> Buckets -> Create bucket**.
2. Configure bucket settings:

| Property | Configured Value | Architectural Rationale |
| :--- | :--- | :--- |
| **Bucket name** | `huylam-ocr-documents-ap-southeast-1` | Globally unique bucket identifier |
| **AWS Region** | `ap-southeast-1` (Singapore) | Synchronized with VPC infrastructure |
| **Object Ownership** | ACLs disabled (recommended) | Enforces unified IAM policy authorization |
| **Block Public Access** | **Block all public access = ON** | Complete public ingress blocking to safeguard document privacy |
| **Bucket Versioning** | Disable | Minimizes storage consumption under FinOps guidelines |
| **Default encryption** | Server-side encryption with Amazon S3 managed keys (SSE-S3) | Automatic encryption at rest |

3. Click **Create bucket**.

![Amazon S3 Bucket Configuration](/images/week10/01-s3-create-bucket-config.png)

---

## 2. Provisioning uploads/ and outputs/ Folder Prefixes

1. In the Buckets table, select `huylam-ocr-documents-ap-southeast-1`.
2. Create `uploads/` prefix:
   * Click **Create folder**, enter folder name: `uploads`.
   * Ingests raw input documents (digital PDFs, scanned images) uploaded by users.
   * Click **Create folder**.

![S3 Bucket Uploads Folder Prefix](/images/week11/05-s3-bucket-uploads-folder.png)

3. Create `outputs/` prefix:
   * Click **Create folder**, enter folder name: `outputs`.
   * Automatically isolates job artifacts organized by job ID (`outputs/<job_id>/filename.md`, `filename.docx`, `filename.pdf`).
   * Click **Create folder**.

![S3 Bucket Outputs Folder Prefix](/images/week11/06-s3-bucket-outputs-folder.png)

---

## 3. Configuring Cross-Origin Resource Sharing (CORS)

To enable client-side browsers running Web Studio to upload files directly via S3 Presigned URLs:

1. Switch to the **Permissions** tab of the bucket.
2. Scroll down to **Cross-origin resource sharing (CORS)** and click **Edit**.
3. Insert the JSON policy:

```json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "PUT",
            "POST",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [
            "ETag"
        ],
        "MaxAgeSeconds": 3000
    }
]
```

4. Click **Save changes**.

![Amazon S3 Bucket CORS Configuration Saved](/images/week10/03-s3-cors-configuration-saved.png)

---

## 4. Expected Outcomes

Upon completing this section:
- Bucket `huylam-ocr-documents-ap-southeast-1` is created with **Block all public access = ON**.
- Prefixes `uploads/` and `outputs/` are established.
- CORS rules are active, enabling direct browser communication with S3.