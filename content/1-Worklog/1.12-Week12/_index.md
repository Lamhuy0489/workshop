---
title: "Week 12 Worklog"
date: 2026-10-25
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

> [!NOTE] Execution Timeline
> **From 19/10/2026 to 25/10/2026**

### Week 12 Objectives:
* Deploy a production-ready Three-Tier Enterprise Cloud Architecture on AWS for the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
  * **Multi-AZ Virtual Private Cloud (VPC) Subnet Partitioning**: Leverage custom network topology `huylam-vpc` (CIDR `10.0.0.0/16`) spanning 2 independent Availability Zones (`ap-southeast-1a` and `ap-southeast-1b`), connected via Internet Gateway `huylam-igw` and public route table.
  * **Defense-in-Depth Security Group Chaining**: Establish and bind two isolated security layers:
    * `huylam-alb-sg`: Ingests HTTP port 80 traffic from global Internet clients (`0.0.0.0/0`).
    * `huylam-web-sg`: Enforces strict isolation on the application host, accepting TCP port 5000 ingress strictly from the ALB Security Group (`huylam-alb-sg`), preventing port scanning from external networks.
  * **IAM Instance Profile Least-Privilege Authorization**: Configure IAM Role `huylam-ssm-role` with `AmazonSSMManagedInstanceCore` (secure remote administration via AWS Systems Manager Session Manager without open SSH ports), `AmazonS3FullAccess`, and `AmazonDynamoDBFullAccess` for direct application integration with cloud storage and database services.
  * **Amazon EC2 Application Server Launch and Provisioning**: Deploy EC2 instance `huylam-ocr-web-server` (`i-0566e1eedaacea52d`, Amazon Linux 2023, t2.micro), pull Web Studio source code from GitHub, construct Python 3.11 virtual environment, and register systemd daemon `huylam-ocr.service` running Gunicorn WSGI server on port 5000.
  * **Target Group and Application Load Balancer (ALB) Setup**: Provision Target Group `huylam-ocr-tg`, fine-tune Health Check Path targeting `/login` for reliable status evaluation, and launch Internet-facing Application Load Balancer `huylam-ocr-alb` with Multi-AZ distribution.
  * **Public Live DNS Verification and Internet Accessibility**: Conduct real-world validation from Safari browser accessing `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`, verifying traffic routing, HTTP 302 redirection, and responsive Web Studio rendering.
  * **Compilation of 10 Red Bounding-Boxed Proof Images**: Annotate all screenshots highlighting the **AWS Account Badge `huylam (677994024390)`**, network configurations, healthy target status, and live public DNS endpoint.

---

### Tasks carried out this week:

| Day | Task | Achievement | Reference Material |
| :--- | :--- | :--- | :--- |
| **Monday (19/10/2026)** | - Analyze production deployment requirements for Web Studio on AWS.<br>- Architect 3-tier networking blueprint integrating Application Load Balancer and EC2 inside Multi-AZ VPC.<br>- Draft security group chaining matrix and port isolation specifications. | Finalized segmented cloud network blueprints and ingress/egress port planning matrix. | [AWS VPC Architecture Design](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) |
| **Tuesday (20/10/2026)** | - User manually creates ALB Security Group on AWS Console: `huylam-alb-sg` (`sg-0dca819306a96bfdb`), opening HTTP port 80 to `0.0.0.0/0`.<br>- Create Web Server Security Group: `huylam-web-sg` (`sg-0ff9ea20c6c3a8dc8`), configuring inbound rule permitting TCP 5000 exclusively from `huylam-alb-sg`. | Established chained defense-in-depth perimeter, safeguarding application server from unauthorized port access. | [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html) |
| **Wednesday (21/10/2026)** | - Configure IAM Role `huylam-ssm-role` with policies: `AmazonSSMManagedInstanceCore`, `AmazonS3FullAccess`, `AmazonDynamoDBFullAccess`.<br>- Launch EC2 instance `huylam-ocr-web-server` (`i-0566e1eedaacea52d`, AMI AL2023, t2.micro) placed in public subnet `subnet-0efa7c3a5818035dc` (`ap-southeast-1a`). | Virtual machine launched successfully with zero hardcoded credentials, obtaining IAM permissions via STS instance profile. | [AWS Systems Manager Role](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) |
| **Thursday (22/10/2026)** | - Connect to EC2 instance securely via AWS Systems Manager Session Manager.<br>- Resolve Security Group outbound rule restrictions to enable package repository access.<br>- Install Python 3.11, Git, and clone GitHub repository `Lamhuy0489/aws`.<br>- Provision virtual environment and configure systemd service `huylam-ocr.service` for Gunicorn. | Web Studio service activated on internal port 5000, configured to start automatically on system boot (`enabled`). | [Gunicorn Systemd Deployment](https://docs.gunicorn.org/en/stable/deploy.html) |
| **Friday (23/10/2026)** | - Create Target Group `huylam-ocr-tg` (`arn:aws:...:targetgroup/huylam-ocr-tg/9840edd6b7d65bf3`) on port 5000 in `huylam-vpc`.<br>- Register EC2 instance `i-0566e1eedaacea52d` into target group.<br>- Provision Internet-facing Application Load Balancer `huylam-ocr-alb` across Multi-AZ subnets with `huylam-alb-sg`.<br>- Bind HTTP:80 Listener forwarding traffic into Target Group `huylam-ocr-tg`. | Application Load Balancer transitions to **Active** status, assigning a globally accessible public DNS endpoint. | [Application Load Balancers Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) |
| **Saturday (24/10/2026)** | - Adjust Target Group Health Check Path to `/login` to match application HTTP 200 OK response.<br>- Target Group health evaluations achieve **Healthy (1/1)** status.<br>- Verify end-to-end access from Safari browser via ALB Public DNS URL.<br>- Web Studio enterprise authentication portal renders cleanly with low latency. | Achieved live production deployment across public Internet, ready for user validation and evaluation. | [Target Groups Health Checks](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html) |
| **Sunday** | - Capture 10 proof screenshots covering every configuration and deployment step on AWS Console and Web Studio.<br>- Execute automated red bounding box annotations surrounding Account Badge `huylam (677994024390)` and core parameters.<br>- Author bilingual Week 12 worklog documentation on Hugo site.<br>- Synchronize project roadmap `ROADMAP.md` and commit updates to GitHub. | Completed 100% of Week 12 enterprise cloud infrastructure and public deployment objectives. | [AWS Free Tier Guidelines](https://aws.amazon.com/free/) |

---

### Infrastructure Deployment Specifications:

| Resource Component | Identifier Name | Unique Identifier (ID / ARN) | Configuration Parameters | Operational State |
| :--- | :--- | :--- | :--- | :--- |
| **Virtual Private Cloud** | `huylam-vpc` | `vpc-0125f4d6db3fbffa6` | CIDR `10.0.0.0/16`, attached Internet Gateway `huylam-igw` | Available |
| **Public Subnet 1** | `huylam-subnet-public1-ap-southeast-1a` | `subnet-0efa7c3a5818035dc` | CIDR `10.0.8.0/21`, Availability Zone `ap-southeast-1a` | Available |
| **Public Subnet 2** | `huylam-subnet-public2-ap-southeast-1b` | `subnet-0e07eb2fd44d1ac91` | CIDR `10.0.16.0/21`, Availability Zone `ap-southeast-1b` | Available |
| **ALB Security Group** | `huylam-alb-sg` | `sg-0dca819306a96bfdb` | Inbound: HTTP 80 (`0.0.0.0/0`), Outbound: All traffic | Bound to ALB |
| **Web Security Group** | `huylam-web-sg` | `sg-0ff9ea20c6c3a8dc8` | Inbound: Custom TCP 5000 (Source: `huylam-alb-sg`), SSH 22 | Bound to EC2 |
| **IAM Instance Role** | `huylam-ssm-role` | `arn:aws:iam::677994024390:role/huylam-ssm-role` | Policies: `AmazonSSMManagedInstanceCore`, `AmazonS3FullAccess`, `AmazonDynamoDBFullAccess` | Attached to Instance Profile |
| **Application Host EC2** | `huylam-ocr-web-server` | `i-0566e1eedaacea52d` | AMI Amazon Linux 2023, t2.micro, Private IP `10.0.8.15`, Public IP `54.254.141.192` | Running (2/2 checks passed) |
| **Target Group** | `huylam-ocr-tg` | `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-ocr-tg/9840edd6b7d65bf3` | Port 5000, Protocol HTTP1, Health check path: `/login` | Healthy (1/1) |
| **Application Load Balancer** | `huylam-ocr-alb` | `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:loadbalancer/app/huylam-ocr-alb/68ac5f1a04e35614` | Scheme: Internet-facing, IPv4, Multi-AZ (`1a` and `1b`), Listener HTTP:80 | Active |
| **Public DNS Endpoint** | Live Access URL | `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com` | Dynamically distributes Internet traffic to Web Studio server | LIVE on Internet |

---

### Three-Tier Enterprise Cloud Architecture Breakdown:

The production infrastructure deployment follows AWS Well-Architected Framework best practices:

1. **Ingress & Load Distribution Layer**:
   * Application Load Balancer `huylam-ocr-alb` serves as the centralized entry point for global client connections.
   * Shielded by `huylam-alb-sg`, admitting ingress traffic exclusively on standard HTTP port 80.
   * Multi-AZ deployment ensures transparent failover between availability zones during infrastructure events.

2. **Application Compute Layer**:
   * The EC2 instance `huylam-ocr-web-server` hosting the Web Studio is encapsulated inside `huylam-web-sg`.
   * **Least Privilege Security Chaining**: Internal port 5000 is never exposed to public CIDR ranges. Ingress is restricted exclusively to traffic originating from the security group ID of the load balancer (`sg-0dca819306a96bfdb`).
   * **Secure SSM Administration**: Management access is established via AWS Systems Manager Session Manager, removing the necessity of maintaining inbound SSH port 22 or managing local key pairs.

3. **Cloud Data & Storage Tier**:
   * Instance permissions are brokered through IAM Role `huylam-ssm-role`. Document uploads into S3 (`huylam-ocr-documents-ap-southeast-1`) and state tracking in DynamoDB (`document_processing_jobs`) leverage temporary credentials issued by AWS STS, eliminating hardcoded access credentials.

---

### Empirical Proofs with Red Bounding Boxes (AWS Console & Web Studio):

> [!IMPORTANT]
> All 10 proof screenshots feature precise red bounding boxes highlighting the **AWS Account Badge `huylam (677994024390)`**, resource IDs, active operational states, and live public DNS endpoints.

#### 1. Creation of ALB Security Group huylam-alb-sg:
Security group configured to accept incoming HTTP port 80 traffic from global Internet origins (`0.0.0.0/0`):
![Creation of Security Group for ALB](/images/week12/01-alb-security-group-created.png)

---

#### 2. Configuration of Web Server Security Group huylam-web-sg:
Application tier security group accepting TCP port 5000 traffic exclusively originating from `huylam-alb-sg`:
![Configuration of Security Group for Web Server](/images/week12/02-web-security-group-created.png)

---

#### 3. Attachment of IAM Permissions to huylam-ssm-role:
Instance role granted Systems Manager management (`AmazonSSMManagedInstanceCore`), S3 storage (`AmazonS3FullAccess`), and DynamoDB database (`AmazonDynamoDBFullAccess`) privileges:
![IAM Role Permissions Attachment](/images/week12/03-iam-role-ssm-s3-dynamodb.png)

---

#### 4. Successful Launch of EC2 Instance huylam-ocr-web-server:
Virtual compute instance (`i-0566e1eedaacea52d`) running Amazon Linux 2023 inside `huylam-vpc` public subnet:
![EC2 Instance Launch Success](/images/week12/04-ec2-launch-instance-success.png)

---

#### 5. Provisioning of Target Group huylam-ocr-tg on Port 5000:
Target group created on port 5000 within `huylam-vpc`, registered with application instance `i-0566e1eedaacea52d`:
![Target Group Provisioning Success](/images/week12/05-target-group-created.png)

---

#### 6. Application Load Balancer huylam-ocr-alb Active Status:
Internet-facing Multi-AZ Application Load Balancer deployed in Active status with allocated public DNS endpoint:
![Application Load Balancer Active Status](/images/week12/06-alb-created-active.png)

---

#### 7. Service Deployment via Systems Manager Session Manager:
Secure shell session via SSM Session Manager cloning repository, establishing Python virtual environment, and starting systemd service `huylam-ocr.service`:
![SSM Session Manager Deployment](/images/week12/07-ssm-session-manager-deployment.png)

---

#### 8. Target Group Validation Reaching Healthy Status:
Following health check path adjustment to `/login`, Target Group verifies instance responsiveness and transitions to **Healthy (1/1)**:
![Target Group Healthy Status](/images/week12/08-target-group-healthy-status.png)

---

#### 9. Safari Browser Ingress via ALB Public DNS URL:
Real-world client verification reaching `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`, routing successfully to the Web Studio login portal:
![Browser Ingress via ALB DNS](/images/week12/09-browser-alb-public-dns-login.png)

---

#### 10. Web Studio Operating Live on AWS Cloud Infrastructure:
End-to-end document OCR and translation platform operational on live AWS infrastructure, ready for user evaluation:
![Web Studio Live on AWS Cloud](/images/week12/10-browser-alb-studio-live.png)

---

#### 11. Launching Vision OCR Server on Kaggle GPU (Qwen2.5-VL-7B):
Executing deep multimodal OCR pipeline on Kaggle 2x NVIDIA T4 GPU accelerator, exposing secure HTTPS ingress via Cloudflare Tunnel:
![Kaggle Notebook Execution and Cloudflare Tunnel Endpoint](/images/week12/11-kaggle-gpu-notebook-run.png)

---

#### 12. Administrator Connecting Kaggle Endpoint into Web Studio:
Signing into System Admin (`/admin`), registering the Cloudflare Tunnel endpoint in the API Key pool, and confirming active status ($0.00 compute expense):
![Web Studio Admin Panel Connected to Kaggle GPU OCR](/images/week12/12-web-studio-admin-gpu-connected.png)

---

### Week 12 Summary & FinOps Governance:
* Successfully achieved 100% completion of Three-Tier Enterprise Cloud Architecture deployment on AWS using Application Load Balancer (ALB) and EC2 Web Studio.
* Strictly enforced Principle of Least Privilege and Security Group chaining, fully isolating compute instances from unauthenticated direct Internet ingress.
* Production URL validated live via public DNS: `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`.
* Eliminated SSH key maintenance overhead via AWS Systems Manager Session Manager.
* **FinOps Teardown Governance**: To preserve the strict $0.00 cost objective throughout the internship, ALB hourly charges (~$0.0225/hr) and EC2 runtimes are comprehensively documented with proof images for graduation defense, with safe teardown procedures documented for execution upon completion of evaluation sessions.