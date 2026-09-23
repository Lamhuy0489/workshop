---
title: "Week 1 Worklog"
date: 2026-08-09
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

> [!NOTE] Execution Timeline
> **From 03/08/2026 to 09/08/2026**

### Week 1 Objectives:
* Register and configure a new AWS personal account with 12-month Free Tier benefits.
* Enforce security best practices: Enable Multi-Factor Authentication (MFA) on the Root Account.
* Manage access control with AWS IAM: Create the `dev_admin` administrative IAM User and Group.
* Configure automated cost alerts using AWS Budgets to mitigate unexpected spending risks.
* Install and configure AWS CLI v2 on the local macOS environment.
* Establish an Obsidian-based external knowledge brain and launch the Hugo Internship Report site on GitHub Pages.

### Completed Tasks in Week 1:

| Day | Task Description | Deliverables & Outcomes | Resource Link |
| :--- | :--- | :--- | :--- |
| **Monday (03/08/2026)** | - Review FCAJ Workforce Bootcamp 2026 regulations.<br>- Study The First Cloud Journey (FCJ) curriculum. | Familiarized with bootcamp rules at hn-rules.awsfcaj.com and graduation criteria. | https://cloudjourney.awsstudygroup.com |
| **Tuesday (04/08/2026)** | - Register AWS account.<br>- Configure Root MFA (Lab 000001). | Successfully enabled Virtual MFA device on mobile; Root account secured. | https://000001.awsstudygroup.com |
| **Wednesday (05/08/2026)** | - Configure AWS Budgets (Lab 000007). | Configured two active budgets (100 USD & 200 USD) in HEALTHY state. | https://000007.awsstudygroup.com |
| **Thursday (06/08/2026)** | - Configure IAM permissions (Lab 000002). | Created `dev_admin` user, attached AdministratorAccess, generated CLI access keys. | https://000002.awsstudygroup.com |
| **Friday (07/08/2026)** | - Install AWS CLI v2 on macOS (Lab 000011).<br>- Configure profile credentials via `aws configure`. | AWS CLI v2.36.48 working; authenticated successfully via `aws sts get-caller-identity`. | https://000011.awsstudygroup.com |
| **Saturday (08/08/2026)** | - Set up Hugo Learn Theme and deploy report site to GitHub Pages.<br>- Draft Capstone Proposal: Enterprise Agentic RAG Platform on AWS. | Bilingual report site live on GitHub Pages at lamhuy0489.github.io/workshop. | https://github.com/AWS-First-Cloud-Journey/Workshop-template |

### Verified AWS Technical Configuration:
- **AWS Account ID**: `677994024390`
- **AWS Account Name**: `huylam`
- **IAM User**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **Default Region**: `ap-southeast-1` (Singapore)
- **Root Account MFA**: Enabled (`MFA: 1`)
- **AWS Budgets**: `My Monthly Cost Budget` (100 USD) and `My-200$-budget` (200 USD) active.

---

### Proof of Work & Hands-on Verifications

#### 1. Personal AWS Account Verification & Free Tier Status
- **Description**: Verified official AWS personal account enrolled in 12-month Free Tier with account name `huylam` and Account ID `677994024390`. Active promotional credits balance stands at 188.21 USD.
- **Red highlighted areas**: Top navigation account pill, Account ID `6779-9402-4390`, Account name `huylam`, and remaining credits.

![AWS Account Verification huylam](/images/week1/01-account-huylam.png)

---

#### 2. Root Account Multi-Factor Authentication (MFA) Hardening
- **Description**: In strict compliance with AWS Well-Architected Security Pillar and CIS AWS Foundations Benchmark, Virtual MFA is enabled on the Root account and no root access keys are provisioned.
- **Red highlighted areas**: Green checkmarks for both "Root user has MFA" and "Root user has no active access keys".

![MFA Verification on Root Account](/images/week1/02-mfa-root.png)

---

#### 3. Automated Cost Anomaly Defense with AWS Budgets
- **Description**: Configured monthly budget `My Monthly Cost Budget` with a threshold limit of 100 USD to continuously track actual vs. forecasted spend and trigger immediate automated email alerts upon reaching set variance percentages.
- **Red highlighted areas**: Budget name `My Monthly Cost Budget`, `Healthy` health status, `OK` alert thresholds, and 100.00 USD allocation.

![AWS Budgets Configuration](/images/week1/03-aws-budgets.png)

---

#### 4. AWS CLI v2 Local Setup & STS Identity Verification
- **Description**: Installed AWS CLI v2 locally on macOS, securely configured developer profile for `dev_admin`, and executed `aws sts get-caller-identity`. The Secret Access Key is masked to uphold enterprise security disclosure guidelines.
- **Red highlighted areas**: Executed `aws sts get-caller-identity` command and resulting JSON payload confirming Account ID `677994024390` and User ARN `arn:aws:iam::677994024390:user/dev_admin`.

![AWS CLI v2 Identity Verification](/images/week1/04-aws-cli-verified.png)