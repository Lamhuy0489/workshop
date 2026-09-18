---
title: "Week 1 Worklog"
date: 2026-09-18
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

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
| **Mon** | - Review FCAJ Workforce Bootcamp 2026 regulations.<br>- Study The First Cloud Journey (FCJ) curriculum. | Familiarized with bootcamp rules at hn-rules.awsfcaj.com and graduation criteria. | https://cloudjourney.awsstudygroup.com |
| **Tue** | - Register AWS account.<br>- Configure Root MFA (Lab 000001). | Successfully enabled Virtual MFA device on mobile; Root account secured. | https://000001.awsstudygroup.com |
| **Wed** | - Configure AWS Budgets (Lab 000007). | Configured two active budgets (100 USD & 200 USD) in HEALTHY state. | https://000007.awsstudygroup.com |
| **Thu** | - Configure IAM permissions (Lab 000002). | Created `dev_admin` user, attached AdministratorAccess, generated CLI access keys. | https://000002.awsstudygroup.com |
| **Fri** | - Install AWS CLI v2 on macOS (Lab 000011).<br>- Configure profile credentials via `aws configure`. | AWS CLI v2.36.48 working; authenticated successfully via `aws sts get-caller-identity`. | https://000011.awsstudygroup.com |
| **Sat** | - Set up Hugo Learn Theme and deploy report site to GitHub Pages.<br>- Draft Capstone Proposal: Enterprise Agentic RAG Platform on AWS. | Bilingual report site live on GitHub Pages at lamhuy0489.github.io/workshop. | https://github.com/AWS-First-Cloud-Journey/Workshop-template |

### Verified AWS Technical Configuration:
- **AWS Account ID**: `677994024390`
- **IAM User**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **Default Region**: `ap-southeast-1` (Singapore)
- **Root Account MFA**: Enabled (`MFA: 1`)
- **AWS Budgets**: `My Monthly Cost Budget` (100 USD) and `My-200$-budget` (200 USD) active.