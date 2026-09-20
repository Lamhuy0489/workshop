---
title: "Week 6 Worklog"
date: 2026-09-21
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:
* Research and master **AWS Systems Manager (SSM)** services: Fleet Manager, Session Manager, Parameter Store, and Run Command adhering to Zero Trust Architecture standards.
* Implement a **Zero Inbound Ports** security baseline: Configure Security Group `huylam-ssm-sg` with 0 Inbound Rules (no SSH port 22 exposed), completely eliminating external network attack vectors.
* Provision dedicated IAM Role `huylam-ssm-role`: Attach managed policies `AmazonSSMManagedInstanceCore` and `AmazonSSMReadOnlyAccess` to enable bidirectional TLS communication between EC2 instances and SSM Control Plane via SSM Agent.
* Validate SSH-keyless remote shell access via **AWS Systems Manager Session Manager**: Access interactive Linux terminal directly inside the browser console under dedicated system user `ssm-user`.
* Centralize parameter and secret management with **AWS Systems Manager Parameter Store**: Store plain-text configurations (String) and sensitive credentials (SecureString) protected by AWS Key Management Service (AWS KMS `alias/aws/ssm`).
* Verify real-time runtime secret decryption inside the Session Manager terminal: Execute AWS CLI commands with `--with-decryption` to fetch student identity Lam Quang Huy (ID: `0212267`) and decrypted database password.
* Automate fleet-level operations using **AWS Systems Manager Run Command**: Execute `AWS-RunShellScript` document remotely without persistent shell access, recording status `Success` and verifying Standard Output.
* Enforce cloud resource governance with **AWS Resource Groups & Tagging**: Create Resource Group `huylam-fcj-resources` automatically aggregating 6 project resources based on tag criteria `Project = FCJ-Bootcamp-2026`.
* Maintain cloud financial discipline (**FinOps**): Audit AWS Billing and Cost Management dashboard, verify Month-to-date expenditure ($0.10 USD), confirm Healthy state across 2 AWS Budgets, and release all test resources.

---

### Tasks Carried Out in Week 6:

| Day | Task | Key Deliverable | Reference Material |
| :--- | :--- | :--- | :--- |
| **Mon** | - Investigate AWS Systems Manager service architecture.<br>- Examine SSM Agent operational mechanics on Linux.<br>- Analyze Zero Trust remote access patterns eliminating Bastion hosts and port 22. | Understood outbound TLS (HTTPS 443) communication architecture to AWS Systems Manager endpoints. | [AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/) |
| **Tue** | - Create IAM Role `huylam-ssm-role` with required SSM policies.<br>- Provision Security Group `huylam-ssm-sg` (`sg-08a93d881545a9cf8`) with zero inbound rules.<br>- Launch EC2 instance `huylam-ssm-instance` (`i-07c150e87aa231c61`) in VPC `huylam-vpc`. | Managed node automatically registered with Systems Manager Fleet Manager showing `Online` status. | [SSM IAM Policies](https://docs.aws.amazon.com/systems-manager/latest/userguide/security-iam-awsmanpolicies.html) |
| **Wed** | - Connect to EC2 instance via AWS Systems Manager Session Manager.<br>- Inspect shell environment: Verify `ssm-user`, working directory, kernel version.<br>- Output student identity verification string: Lam Quang Huy (Student ID: `0212267`). | Established 100% browser-based SSH-free interactive access without managing key pairs (.pem/.ppk). | [Session Manager Documentation](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) |
| **Thu** | - Explore centralized configuration with SSM Parameter Store.<br>- Create String parameters `/huylam/app/environment` and `/huylam/app/student_name`.<br>- Create encrypted SecureString `/huylam/app/db_password` utilizing KMS key `alias/aws/ssm`. | Decoupled configuration data and secrets from source code according to 12-Factor App standards. | [SSM Parameter Store Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) |
| **Fri** | - Validate parameter retrieval and KMS decryption inside Session Manager shell.<br>- Execute AWS CLI SSM commands reading student identification parameter.<br>- Decrypt sensitive database password `HuyLam2026!SecureDBPassword` using `--with-decryption`. | Empirically validated dynamic credential fetching based on EC2 IAM Instance Profile permissions. | [AWS KMS with Parameter Store](https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html) |
| **Sat** | - Configure and trigger AWS Systems Manager Run Command.<br>- Select `AWS-RunShellScript` targeting managed instance `i-07c150e87aa231c61`.<br>- Execute shell script collecting hardware, network, and student identity metadata. | Command finished with `Success` status code (0), capturing complete execution log in AWS Console. | [SSM Run Command Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) |
| **Sun** | - Provision AWS Resource Group `huylam-fcj-resources` aggregating 6 resources via `Project = FCJ-Bootcamp-2026`.<br>- Conduct FinOps audit: Month-to-date cost at $0.10, 2 Budgets Healthy.<br>- Execute FinOps teardown: Released compute and parameter assets to safeguard Free Tier allowances.<br>- Finalized technical documentation, lab logs, and evidence portfolio. | Completed all Week 6 objectives and hands-on lab requirements with 0 USD unexpected expenditure. | [AWS Resource Groups Guide](https://docs.aws.amazon.com/ARG/latest/userguide/welcome.html) |

---

### Technical Specifications Verified on AWS:

#### 1. Identity & Region:
- **AWS Account ID**: `677994024390`
- **Account Name**: `huylam`
- **Executing IAM User**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **Associated VPC**: `huylam-vpc` (`vpc-0125f4d6db3fbffa6`)
- **Target Subnet**: `subnet-0efa7c3a5818035dc` (Public Subnet)

#### 2. Identity and Access Management (IAM Role & Policies):
- **IAM Role Name**: `huylam-ssm-role`
- **Role ARN**: `arn:aws:iam::677994024390:role/huylam-ssm-role`
- **Trust Entity**: `ec2.amazonaws.com`
- **Attached Policies**:
  - `AmazonSSMManagedInstanceCore`: Enables core AWS Systems Manager service integration (Fleet Manager, Session Manager, Run Command).
  - `AmazonSSMReadOnlyAccess`: Provides read and decryption access to SSM Parameter Store.

#### 3. Security Group Baseline (Zero Inbound Rules):
- **Security Group Name**: `huylam-ssm-sg`
- **Security Group ID**: `sg-08a93d881545a9cf8`
- **Description**: `Security Group for Systems Manager - Zero Inbound SSH - Lam Quang Huy 0212267`
- **Inbound Rules**: **0 rules (No inbound rules)**. Absolutely no ingress ports open (no port 22 SSH, 80 HTTP, or 443 HTTPS).
- **Outbound Rules**: Allows all outbound traffic (`All traffic`, `0.0.0.0/0`) for SSM Agent to establish outbound TLS connections to Systems Manager endpoints.

#### 4. Managed Compute Node (Amazon EC2):
- **Instance Name Tag**: `huylam-ssm-instance`
- **Instance ID**: `i-07c150e87aa231c61`
- **Instance Type**: `t3.micro` (2 vCPUs, 1 GiB RAM)
- **Private IPv4**: `10.0.1.240`
- **Public IPv4**: `13.212.80.147`
- **Operating System**: Amazon Linux 2023 (Kernel 6.18.48-109.150.amzn2023.x86_64)
- **SSM Agent Ping Status**: `Online`
- **SSM Agent Version**: `3.3.4624.0`
- **Session Manager Connection Status**: `Connected`

#### 5. Configuration & Secret Management (SSM Parameter Store):
- **Application Environment Parameter**:
  - Name: `/huylam/app/environment`
  - Type: `String`
  - Tier: `Standard Tier`
  - Value: `Production-Bootcamp`
- **Student Identity Parameter**:
  - Name: `/huylam/app/student_name`
  - Type: `String`
  - Tier: `Standard Tier`
  - Value: `Lam Quang Huy - MSSV: 0212267 - Class: 67CS`
- **Database Password Secret**:
  - Name: `/huylam/app/db_password`
  - Type: `SecureString`
  - Tier: `Standard Tier`
  - KMS Key: AWS KMS Default Key `alias/aws/ssm`
  - Encrypted Payload: `HuyLam2026!SecureDBPassword`
  - Tags: `Project = FCJ-Bootcamp-2026`, `StudentID = 0212267`

#### 6. Fleet Command Execution (SSM Run Command):
- **Command ID**: `ab82dba7-47c6-4727-9a72-ffe6d89b1598`
- **Command Document**: `AWS-RunShellScript`
- **Target Instance**: `i-07c150e87aa231c61` (`huylam-ssm-instance`)
- **Execution Status**: `Success` (Response code: 0)
- **Standard Output**:
  ```text
  === AWS SYSTEMS MANAGER RUN COMMAND LAB ===
  Student Name: Lam Quang Huy
  Student ID: 0212267
  Class: 67CS - HUCE
  Host: ip-10-0-1-240.ap-southeast-1.compute.internal
  Current User: ssm-user
  Kernel: 6.18.48-109.150.amzn2023.x86_64
  SSM Agent Status: Active (running)
  Uptime: up 18 minutes
  ```

#### 7. Tag-based Resource Group (AWS Resource Groups):
- **Resource Group Name**: `huylam-fcj-resources`
- **Resource Group ARN**: `arn:aws:resource-groups:ap-southeast-1:677994024390:group/huylam-fcj-resources`
- **Description**: `Resource Group for student Lam Quang Huy 0212267 - FCJ Bootcamp 2026`
- **Grouping Criteria**: Tag filter matching key `Project` with value `FCJ-Bootcamp-2026`
- **Group Resources (6)**:
  1. Parameter: `/huylam/app/student_name` (SSM Parameter)
  2. Parameter: `/huylam/app/db_password` (SSM Parameter)
  3. Security Group: `huylam-ssm-sg` (`sg-08a93d881545a9cf8`)
  4. EC2 Instance: `huylam-ssm-instance` (`i-07c150e87aa231c61`)
  5. Secondary EC2 Instance: `huylam-ssm-instance` (`i-0a5174023e7d2f6dc`)
  6. Resource Group: `huylam-fcj-resources` (Self group entity)

#### 8. Cloud Financial Management (FinOps & Budgets):
- **Month-to-date Cost (MTD)**: `$0.10 USD`
- **Total Forecasted Month-End Cost**: `$0.26 USD`
- **Budgets Status**: 2 active budgets operating normally, status `OK / Healthy`.
- **Cost Anomalies Status (MTD)**: `None detected`.
- **FinOps Assessment**: All services deployed strictly within AWS Free Tier quotas.

---

### Empirical Implementation Evidence on AWS:

All evidence images below were captured directly from the live AWS Management Console and AWS Systems Manager terminal sessions by student **Lam Quang Huy (Student ID: 0212267)**. Key UI elements including account badge `huylam (677994024390)`, region Singapore `ap-southeast-1`, and technical metadata are highlighted with red bounding boxes:

#### 1. EC2 Instance Registered as Online Managed Node in Fleet Manager:
- **Description**: AWS Systems Manager Fleet Manager console showing instance `i-07c150e87aa231c61` (`huylam-ssm-instance`) successfully registered with `Online` ping status running Amazon Linux 2023.
- **Bounding Boxes**: Account badge `huylam (677994024390)` and managed instance record row with `Online` status.

![Fleet Manager Managed Nodes](/images/week6/01-ssm-fleet-manager-managed-node.png)

---

#### 2. Zero Trust Security Group with 0 Inbound Rules:
- **Description**: Amazon EC2 Security Group `huylam-ssm-sg` (`sg-08a93d881545a9cf8`) with 0 inbound rules (`No security group rules found`), verifying no SSH port 22 exposure.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, Security Group summary card, and empty Inbound rules table.

![Security Group 0 Inbound Rules](/images/week6/02-ec2-security-group-no-inbound.png)

---

#### 3. EC2 Connect to Instance via SSM Session Manager:
- **Description**: Connect to Linux instance console selecting `SSM Session Manager` tab, displaying `Online` ping status, `Connected` session status, IAM role `huylam-ssm-role`, and orange `Connect` button.
- **Bounding Boxes**: Breadcrumb bar for instance `i-07c150e87aa231c61`, SSM Session Manager option tab, SSM agent info card, and `Connect` button.

![SSM Session Manager Connect](/images/week6/03-ssm-session-manager-connect.png)

---

#### 4. Interactive Linux Shell via Session Manager Terminal:
- **Description**: In-browser interactive shell session running commands under `ssm-user`: `whoami`, `pwd`, `uname -r`, and echo statement displaying student identity `Lam Quang Huy - MSSV: 0212267 - Class: 67CS`.
- **Bounding Boxes**: Instance ID header bar `i-07c150e87aa231c61 (huylam-ssm-instance)` and terminal output showing student identity commands.

![Session Manager Terminal Commands](/images/week6/04-ssm-session-manager-commands.png)

---

#### 5. Creating String Parameter in Parameter Store:
- **Description**: Parameter Store console creating `/huylam/app/student_name` of type `String` in `Standard Tier` storing student identity.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, parameter name input field, and parameter value input field.

![Parameter Store Create String](/images/week6/05-ssm-parameter-store-create-string.png)

---

#### 6. Creating SecureString Parameter with AWS KMS Encryption:
- **Description**: Parameter Store console creating sensitive parameter `/huylam/app/db_password` as `SecureString` encrypted with AWS KMS key `alias/aws/ssm` and project tags.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, KMS key selection `alias/aws/ssm`, and attached tags table.

![Parameter Store SecureString Details](/images/week6/06-ssm-parameter-store-details.png)

---

#### 7. Runtime Parameter Decryption in Session Manager Terminal:
- **Description**: Terminal session demonstrating real-time parameter retrieval and decryption via AWS CLI: `aws ssm get-parameter --name "/huylam/app/db_password" --with-decryption` outputting `HuyLam2026!SecureDBPassword`.
- **Bounding Boxes**: Instance ID header bar and AWS CLI decryption commands with output.

![Session Manager Parameter Decryption](/images/week6/07-ssm-parameter-decryption-test.png)

---

#### 8. Configuring Remote Execution with SSM Run Command:
- **Description**: Run Command console selecting `AWS-RunShellScript` document and defining the shell execution script targeting `huylam-ssm-instance`.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, selected document `AWS-RunShellScript`, and script command editor.

![SSM Run Command Submit](/images/week6/08-ssm-run-command-submit.png)

---

#### 9. Run Command Output Success with Student Identity:
- **Description**: Command execution result for `ab82dba7-47c6-4727-9a72-ffe6d89b1598` displaying status `Success` and standard output log containing student identity metadata.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, command status card showing `Success`, and command output viewer.

![SSM Run Command Output Success](/images/week6/09-ssm-run-command-output-success.png)

---

#### 10. Provisioning Tag-based AWS Resource Group:
- **Description**: AWS Resource Groups console configuring new group `huylam-fcj-resources` with tag criteria `Project = FCJ-Bootcamp-2026`.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, group metadata card, and grouping tag configuration.

![Resource Groups Details](/images/week6/10-resource-groups-details.png)

---

#### 11. Resource Group Members Inventory:
- **Description**: Member resources table (`Group resources (6)`) showing automatic discovery of EC2 instances, security groups, IAM roles, and SSM parameters.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, Group ARN card, and member resources inventory table.

![Resource Groups Members](/images/week6/11-resource-groups-members.png)

---

#### 12. FinOps Billing and Budget Verification:
- **Description**: AWS Billing and Cost Management home showing Month-to-date cost of `$0.10 USD` and 2 active budgets in `OK / Healthy` status.
- **Bounding Boxes**: Account badge `huylam (677994024390)`, Cost summary card, and Budgets monitor card.

![AWS Billing and Cost FinOps](/images/week6/12-aws-billing-cost-finops.png)

---

### Key Takeaways:
1. **Zero Trust Architecture**: Completely eliminating inbound SSH access dramatically reduces vulnerability to port scanning and automated exploit attempts.
2. **Modern Fleet Operations**: Managing instances via SSM Agent enables centralized access control, full session auditing, and scalable remote execution without maintaining bastion infrastructure.
3. **Enterprise Configuration Management**: Separating application configuration from code via Parameter Store and securing credentials using KMS keys aligns with modern cloud security best practices.
4. **Automated Resource Governance**: Tag-based Resource Groups streamline management of multi-tier applications, facilitating batch automation and granular cost visibility.
5. **FinOps Discipline**: Real-time billing monitoring ensured all experiments were conducted within Free Tier boundaries ($0.10 MTD expenditure).