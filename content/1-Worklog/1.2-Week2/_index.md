---
title: "Week 2 Worklog"
date: 2026-09-20
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:
* Explore object storage architecture and access security on Amazon S3.
* Create a globally unique S3 Bucket and configure controlled Block Public Access de-restriction.
* Enable serverless Static Website Hosting with default index document routing.
* Formulate and enforce JSON-based S3 Bucket Policies in adherence to Least Privilege principles.
* Validate end-to-end global web accessibility via HTTP protocol and AWS CLI diagnostic tooling.
* Prepare hands-on foundational knowledge for Amazon EC2, EC2 User Data, IAM Roles, and Amazon RDS MySQL.

### Completed Tasks in Week 2:

| Day | Task Description | Deliverables & Outcomes | Resource Link |
| :--- | :--- | :--- | :--- |
| **Mon** | - Study Amazon S3 & Static Website Hosting.<br>- Create S3 Bucket `huylam-static-web-677994024390`.<br>- Upload enterprise portal `index.html`.<br>- Enable Static Website Hosting & Enforce Bucket Policy. | Bucket active in Public Read mode; static website live globally via AWS endpoint with HTTP 200 OK. | [Lab 000057](https://000057.awsstudygroup.com) |
| **Tue** | - Research IAM Roles for EC2 compute.<br>- Attach IAM Role to EC2 instance.<br>- Test S3 CLI operations from virtual instance. | Understood seamless credential federation via EC2 Instance Metadata Service (IMDS) without hardcoded keys. | [Lab 000048](https://000048.awsstudygroup.com) |
| **Wed** | - Study EC2 User Data bootstrap scripts.<br>- Launch EC2 instance with automated web server configuration.<br>- Verify web access via Public IPv4. | Automated Apache web server deployment upon initial instance boot sequence. | [Lab 000004](https://000004.awsstudygroup.com) |
| **Thu** | - Configure IAM Deny Policy testing.<br>- Evaluate policy precedence logic in AWS IAM. | Verified that Explicit Deny consistently overrides Explicit Allow under AWS CLI testing. | [Lab 000002](https://000002.awsstudygroup.com) |
| **Fri** | - Provision Amazon RDS MySQL under Free Tier.<br>- Configure isolated Security Groups allowing EC2 ingress only. | Relational database instance achieved Available status inside private network tier. | [Lab 000005](https://000005.awsstudygroup.com) |
| **Sat** | - Connect EC2 to RDS MySQL.<br>- Deploy dynamic database-backed web application.<br>- Execute resource cleanup and synthesize report. | Successfully completed 3-tier architecture verification while safeguarding Free Tier budgets. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

### Verified AWS Technical Configuration:
- **AWS Account ID**: `677994024390`
- **AWS Account Name**: `huylam`
- **IAM Principal**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **Default Region**: `ap-southeast-1` (Singapore)
- **S3 Bucket Name**: `huylam-static-web-677994024390`
- **S3 Bucket ARN**: `arn:aws:s3:::huylam-static-web-677994024390`
- **Bucket Website Endpoint**: `http://huylam-static-web-677994024390.s3-website-ap-southeast-1.amazonaws.com`
- **Security Policy**: Public Read `s3:GetObject` permission strictly granted.
- **CLI Verification**: Confirmed via `aws s3api get-bucket-policy` and `aws s3api get-bucket-website`.

---

### Proof of Work & Hands-on Verifications

#### 1. Globally Unique S3 Bucket Initialization
- **Description**: Created personalized bucket `huylam-static-web-677994024390` in region `ap-southeast-1` (Singapore), directly bound to verified student account ID.
- **Red highlighted areas**: Top navigation identity `huylam (677994024390)`, AWS Region, and Bucket name.

![S3 Bucket Configuration](/images/week2/01-create-bucket-config.png)

---

#### 2. Controlled Block Public Access De-restriction
- **Description**: Deactivated default public access blocks and acknowledged security compliance alerts as required by static web hosting architectures.
- **Red highlighted areas**: Unchecked *Block all public access* and checked acknowledgment box.

![Block Public Access Settings](/images/week2/02-block-public-access-settings.png)

---

#### 3. Server-Side Encryption (SSE-S3) Configuration
- **Description**: Enforced Amazon S3 managed keys (SSE-S3) default encryption to guarantee data protection at rest.
- **Red highlighted areas**: Selected *SSE-S3* radio option and *Create bucket* button.

![SSE-S3 Encryption](/images/week2/03-default-encryption-sse-s3.png)

---

#### 4. Bucket Provisioning Confirmation
- **Description**: AWS management console confirmed instantaneous provisioning of S3 bucket `huylam-static-web-677994024390`.
- **Red highlighted areas**: *Successfully created bucket* notification and confirmed bucket title.

![Bucket Created Confirmation](/images/week2/04-bucket-created-success.png)

---

#### 5. Enterprise Portal `index.html` Upload
- **Description**: Uploaded customized cloud portal artifact `index.html` (17.5 KB) into bucket root.
- **Red highlighted areas**: File list entry `index.html`, target S3 URI `s3://huylam-static-web-677994024390`, and *Upload* button.

![Upload index.html](/images/week2/05-upload-index-html.png)

---

#### 6. Static Website Hosting Activation
- **Description**: Enabled *Host a static website* routing mode and designated `index.html` as the primary index document.
- **Red highlighted areas**: *Enable* toggle, *Host a static website* selection, and `index.html` document name.

![Enable Static Website Hosting](/images/week2/06-enable-static-hosting.png)

---

#### 7. Bucket Website Endpoint Registration
- **Description**: AWS registered a high-availability regional website endpoint for Singapore region.
- **Red highlighted areas**: Success confirmation, *Enabled* status, and clickable *Bucket website endpoint*.

![Bucket Website Endpoint](/images/week2/07-static-hosting-endpoint.png)

---

#### 8. S3 Bucket Policy Enforcement (JSON)
- **Description**: Authored and attached JSON access control policy granting granular `s3:GetObject` read permissions to Internet clients.
- **Red highlighted areas**: *Block all public access: Off*, success banner, and formatted JSON Bucket policy.

![S3 Bucket Policy](/images/week2/08-bucket-policy-json.png)

---

#### 9. Live Global Website Verification
- **Description**: Verified public resolution in browser, displaying live enterprise portal with *Production Status: Online* and validated student metadata for Lâm Quang Huy.
- **Red highlighted areas**: Browser address bar, *Production Status: Online* indicator, and student identity table.

![Live Website Verification](/images/week2/09-website-live-verification.png)