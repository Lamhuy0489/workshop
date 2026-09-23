---
title: "Week 2 Worklog"
date: 2026-08-16
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

> [!NOTE] Execution Timeline
> **From 10/08/2026 to 16/08/2026**

### Week 2 Objectives:
* Explore object storage architecture and access security mechanisms on Amazon S3.
* Create a globally unique S3 Bucket and configure controlled Block Public Access de-restriction.
* Enable serverless Static Website Hosting with default index document routing.
* Formulate and enforce JSON-based S3 Bucket Policies in strict adherence to Least Privilege principles.
* Launch an Amazon EC2 virtual machine running Amazon Linux 2023 with User Data bootstrapping for automated Apache HTTP Server deployment.
* Design, configure, and attach an IAM Role to Amazon EC2 for secure Amazon S3 resource access via Instance Metadata Service (IMDS).
* Validate end-to-end global web accessibility, browser rendering, and AWS CLI diagnostic tooling.

### Completed Tasks in Week 2:

| Day | Task Description | Deliverables & Outcomes | Resource Link |
| :--- | :--- | :--- | :--- |
| **Monday (10/08/2026)** | - Study Amazon S3 & Static Website Hosting.<br>- Create S3 Bucket `huylam-static-web-677994024390`.<br>- Upload enterprise portal `index.html`.<br>- Enable Static Website Hosting & Enforce Bucket Policy. | Bucket active in Public Read mode; static website live globally via AWS endpoint with HTTP 200 OK. | [Lab 000057](https://000057.awsstudygroup.com) |
| **Tuesday (11/08/2026)** | - Research IAM Roles for EC2 compute.<br>- Create IAM Role `huylam-ec2-s3-readonly-role` with `AmazonS3ReadOnlyAccess`.<br>- Attach IAM Role to EC2 instance.<br>- Test S3 CLI operations from virtual instance via EC2 Instance Connect. | Understood seamless credential federation via EC2 Instance Metadata Service (IMDS), successfully listing S3 buckets without hardcoded credentials. | [Lab 000048](https://000048.awsstudygroup.com) |
| **Wednesday (12/08/2026)** | - Study EC2 User Data bootstrap automation.<br>- Configure Security Group opening HTTP (80) and SSH (22).<br>- Launch EC2 `t3.micro` instance with automated Apache httpd installation.<br>- Verify web access via Public IPv4. | Automated Apache web server deployment upon initial instance boot sequence; web server responded HTTP 200 OK with student identification card. | [Lab 000004](https://000004.awsstudygroup.com) |
| **Thursday (13/08/2026)** | - Configure IAM Deny Policy testing.<br>- Evaluate policy precedence logic in AWS IAM. | Verified that Explicit Deny consistently overrides Explicit Allow under AWS CLI testing. | [Lab 000002](https://000002.awsstudygroup.com) |
| **Friday (14/08/2026)** | - Provision Amazon RDS MySQL under Free Tier.<br>- Configure isolated Security Groups allowing EC2 ingress only. | Relational database instance achieved Available status inside private network tier. | [Lab 000005](https://000005.awsstudygroup.com) |
| **Saturday (15/08/2026)** | - Connect EC2 to RDS MySQL.<br>- Deploy dynamic database-backed web application.<br>- Execute resource cleanup and synthesize report. | Successfully completed 3-tier architecture verification while safeguarding Free Tier budgets. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

### Verified AWS Technical Configuration:

#### 1. Amazon S3 Resources (Lab 000057):
- **AWS Account ID**: `677994024390`
- **AWS Account Name**: `huylam`
- **IAM Principal**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **Default Region**: `ap-southeast-1` (Singapore)
- **S3 Bucket Name**: `huylam-static-web-677994024390`
- **S3 Bucket ARN**: `arn:aws:s3:::huylam-static-web-677994024390`
- **Bucket Website Endpoint**: `http://huylam-static-web-677994024390.s3-website-ap-southeast-1.amazonaws.com`
- **Security Policy**: Public Read `s3:GetObject` permission strictly granted following Least Privilege principles.
- **CLI Verification**: Confirmed via `aws s3api get-bucket-policy` and `aws s3api get-bucket-website`.

#### 2. Amazon EC2 & Web Server Resources (Lab 000004):
- **Instance Name**: `huylam-web-server`
- **Instance ID**: `i-0520ad41a8d6ce258`
- **Instance Type**: `t3.micro` (Covered under AWS Free Tier)
- **Operating System (AMI)**: `Amazon Linux 2023 AMI` (Kernel 6.1, Architecture x86_64)
- **VPC / Subnet**: Default VPC `vpc-0c84feaf395ece4dd` / Subnet `subnet-0497f256c54a58825` (`ap-southeast-1a`)
- **Public IPv4 Address**: `47.129.234.42`
- **Security Group**: `sg-0eb53b21a70a15a9c` (Inbound: TCP 22 SSH from 0.0.0.0/0, TCP 80 HTTP from 0.0.0.0/0)
- **Web Service**: Apache HTTP Server `httpd 2.4.68` responding HTTP 200 OK.
- **Automation Mechanism**: EC2 User Data bootstrap script automatically updating system packages, installing Apache httpd, enabling systemd service, and generating student identification web page for Lâm Quang Huy.

#### 3. IAM Role Configuration for EC2 (Lab 000048):
- **Role Name**: `huylam-ec2-s3-readonly-role`
- **Role ARN**: `arn:aws:iam::677994024390:role/huylam-ec2-s3-readonly-role`
- **Trusted Entity**: AWS Service `ec2.amazonaws.com` with `sts:AssumeRole` action.
- **Attached Policy**: AWS Managed Policy `AmazonS3ReadOnlyAccess` (`arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess`).
- **Connection Method & Verification**: Used EC2 Instance Connect browser-based console to execute `aws s3 ls`, successfully listing bucket `huylam-static-web-677994024390` through IAM Role and IMDS without static credentials.

---

### Proof of Work & Hands-on Verifications

#### Part 1: Amazon S3 Static Website Hosting (Lab 000057)

##### 1. Globally Unique S3 Bucket Initialization
- **Description**: Created personalized bucket `huylam-static-web-677994024390` in region `ap-southeast-1` (Singapore), directly bound to verified student account ID.
- **Red highlighted areas**: Top navigation identity `huylam (677994024390)`, AWS Region, and Bucket name.

![S3 Bucket Configuration](/images/week2/01-create-bucket-config.png)

---

##### 2. Controlled Block Public Access De-restriction
- **Description**: Deactivated default public access blocks and acknowledged security compliance alerts as required by static web hosting architectures.
- **Red highlighted areas**: Unchecked *Block all public access* and checked acknowledgment box.

![Block Public Access Settings](/images/week2/02-block-public-access-settings.png)

---

##### 3. Server-Side Encryption (SSE-S3) Configuration
- **Description**: Enforced Amazon S3 managed keys (SSE-S3) default encryption to guarantee data protection at rest.
- **Red highlighted areas**: Selected *SSE-S3* radio option and *Create bucket* button.

![SSE-S3 Encryption](/images/week2/03-default-encryption-sse-s3.png)

---

##### 4. Bucket Provisioning Confirmation
- **Description**: AWS management console confirmed instantaneous provisioning of S3 bucket `huylam-static-web-677994024390`.
- **Red highlighted areas**: *Successfully created bucket* notification and confirmed bucket title.

![Bucket Created Confirmation](/images/week2/04-bucket-created-success.png)

---

##### 5. Enterprise Portal `index.html` Upload
- **Description**: Uploaded customized cloud portal artifact `index.html` (17.5 KB) into bucket root.
- **Red highlighted areas**: File list entry `index.html`, target S3 URI `s3://huylam-static-web-677994024390`, and *Upload* button.

![Upload index.html](/images/week2/05-upload-index-html.png)

---

##### 6. Static Website Hosting Activation
- **Description**: Enabled *Host a static website* routing mode and designated `index.html` as the primary index document.
- **Red highlighted areas**: *Enable* toggle, *Host a static website* selection, and `index.html` document name.

![Enable Static Website Hosting](/images/week2/06-enable-static-hosting.png)

---

##### 7. Bucket Website Endpoint Registration
- **Description**: AWS registered a high-availability regional website endpoint for Singapore region.
- **Red highlighted areas**: Success confirmation, *Enabled* status, and clickable *Bucket website endpoint*.

![Bucket Website Endpoint](/images/week2/07-static-hosting-endpoint.png)

---

##### 8. S3 Bucket Policy Enforcement (JSON)
- **Description**: Authored and attached JSON access control policy granting granular `s3:GetObject` read permissions to Internet clients.
- **Red highlighted areas**: *Block all public access: Off*, success banner, and formatted JSON Bucket policy.

![S3 Bucket Policy](/images/week2/08-bucket-policy-json.png)

---

##### 9. Live Global Website Verification
- **Description**: Verified public resolution in browser, displaying live enterprise portal with *Production Status: Online* and validated student metadata for Lâm Quang Huy.
- **Red highlighted areas**: Browser address bar, *Production Status: Online* indicator, and student identity table.

![Live Website Verification](/images/week2/09-website-live-verification.png)

---

#### Part 2: IAM Role Provisioning for Amazon EC2 Compute (Lab 000048)

##### 10. IAM Role Trusted Entity Specification
- **Description**: Configured a new IAM Role specifying *AWS service* as the trusted entity type with *EC2* designated use case, enabling instances to assume AWS API privileges dynamically.
- **Red highlighted areas**: Top identity `huylam (677994024390)`, *AWS service* selection card, and *EC2* use case.

![IAM Role Trusted Entity Selection](/images/week2/10-iam-role-trusted-entity.png)

---

##### 11. AmazonS3ReadOnlyAccess Policy Attachment
- **Description**: Selected and attached AWS Managed Policy `AmazonS3ReadOnlyAccess` adhering to Least Privilege principles (granting list and read access while denying write or delete operations).
- **Red highlighted areas**: Top identity `huylam (677994024390)` and checked `AmazonS3ReadOnlyAccess` policy row.

![AmazonS3ReadOnlyAccess Policy Attachment](/images/week2/11-iam-role-permissions-s3-readonly.png)

---

##### 12. IAM Role Name and Trust Policy Verification
- **Description**: Named role `huylam-ec2-s3-readonly-role`, audited permissions summary, and validated JSON Trust Policy granting `sts:AssumeRole` strictly to service principal `ec2.amazonaws.com`.
- **Red highlighted areas**: Top identity `huylam (677994024390)`, Role name input field, and JSON Trust policy block.

![IAM Role Review and Trust Policy](/images/week2/12-iam-role-name-review.png)

---

##### 13. IAM Role Provisioning Confirmation
- **Description**: AWS IAM Console validated successful role creation and indexed `huylam-ec2-s3-readonly-role` into the global IAM catalog.
- **Red highlighted areas**: Top identity `huylam (677994024390)`, green banner notification *Role huylam-ec2-s3-readonly-role created*, and corresponding row in roles catalog.

![IAM Role Creation Confirmation](/images/week2/13-iam-role-created-success.png)

---

#### Part 3: Amazon EC2 Web Server Deployment with Automated User Data (Lab 000004)

##### 14. Network Security Group Ingress Rules Configuration
- **Description**: Configured virtual firewall rules within custom Security Group, permitting inbound TCP port 22 (SSH) for administrative shell access and TCP port 80 (HTTP) for general web traffic.
- **Red highlighted areas**: Top identity `huylam (677994024390)`, inbound SSH/HTTP rules table, and launch configuration *Summary* sidebar.

![Security Group Configuration](/images/week2/14-ec2-launch-security-group.png)

---

##### 15. Automated User Data Bootstrap Script Configuration
- **Description**: Injected bash bootstrap script into *Advanced details > User data* to automatically update OS packages, install Apache httpd daemon, activate system services, and deploy personalized student identification landing page.
- **Red highlighted areas**: Top identity `huylam (677994024390)`, *User data script* code editor, and *Summary* panel.

![EC2 User Data Configuration](/images/week2/15-ec2-launch-user-data-script.png)

---

##### 16. EC2 Instance Launch Execution Confirmation
- **Description**: AWS compute service initiated deployment of instance `huylam-web-server` bearing identifier `i-0520ad41a8d6ce258`.
- **Red highlighted areas**: Top identity `huylam (677994024390)` and green banner *Successfully initiated launch of instance (i-0520ad41a8d6ce258)*.

![EC2 Instance Launch Success](/images/week2/16-ec2-launch-success.png)

---

#### Part 4: Web Server Operational & IAM Role Access Validation (Lab 000004 & Lab 000048)

##### 17. Live Web Server Verification via Public IPv4
- **Description**: Resolved public IP `http://47.129.234.42` in browser, verifying Apache HTTP Server 2.4.68 responding HTTP 200 OK and successfully rendering student identity credentials for Lâm Quang Huy.
- **Red highlighted areas**: Browser address bar with IP `47.129.234.42` and rendered Amazon EC2 Apache Web Server identity card.

![Live Web Server Verification](/images/week2/17-ec2-web-server-browser-verification.png)

---

##### 18. IAM Role Verification via EC2 Instance Connect CLI (`aws s3 ls`)
- **Description**: Established secure browser-based terminal session into instance `i-0520ad41a8d6ce258` using EC2 Instance Connect. Executed `aws s3 ls`, which successfully listed bucket `huylam-static-web-677994024390` via IAM Role credentials federated through IMDS without requiring static API keys.
- **Red highlighted areas**: Top identity `huylam (677994024390)`, terminal output from `aws s3 ls`, and instance status footer `i-0520ad41a8d6ce258`.

![IAM Role Verification via EC2 Instance Connect](/images/week2/18-ec2-instance-connect-s3-ls.png)