---
title: "Week 4 Worklog"
date: 2026-09-20
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:
* Explore highly available and scalable cloud compute architectures on AWS.
* Design a two-tier security group model separating public load balancing from private application backend servers.
* Create an Amazon EC2 Launch Template using Amazon Linux 2023, `t3.micro` instance type, and a bootstrap User Data script utilizing IMDSv2 session tokens.
* Configure a Target Group on HTTP port 80 with automated periodic health checks.
* Deploy an Internet-facing Application Load Balancer (ALB) across 2 Multi-AZ Public Subnets within Custom VPC `huylam-vpc`.
* Configure an Auto Scaling Group (ASG) with elastic capacity (Desired: 2, Min: 1, Max: 4) distributing instances across two Availability Zones (`ap-southeast-1a` and `ap-southeast-1b`).
* Verify target health status (achieving 2/2 Healthy targets).
* Validate round-robin load distribution across independent Instance IDs and Availability Zones using the ALB DNS name on a web browser.
* Apply FinOps cost-governance practices: Immediately decommission all compute and load balancer resources to preserve the AWS Free Tier allowance.

### Tasks Carried Out in Week 4:

| Day | Task | Key Deliverable | Reference Material |
| :--- | :--- | :--- | :--- |
| **Mon** | - Research Elastic Load Balancing (ELB) theory, comparing ALB, NLB, and GLB.<br>- Study Layer 7 routing mechanisms, Listeners, and Target Groups. | Mastered Application Load Balancer operation principles and target registration dynamics. | [AWS ELB Documentation](https://docs.aws.amazon.com/elasticloadbalancing/) |
| **Tue** | - Study Amazon EC2 Auto Scaling principles.<br>- Understand capacity metrics: Desired, Minimum, and Maximum.<br>- Investigate the EC2 instance lifecycle. | Gained thorough understanding of traffic-driven scaling and self-healing mechanisms upon instance failure. | [AWS Auto Scaling Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) |
| **Wed** | - Design a two-tier network security architecture on `huylam-vpc`.<br>- Create Security Group `huylam-alb-sg` accepting HTTP (port 80) from Internet (`0.0.0.0/0`).<br>- Create Security Group `huylam-asg-web-sg` accepting HTTP only from `huylam-alb-sg`. | Established Defense in Depth, eliminating direct Internet exposure for backend application servers. | [AWS Security Best Practices](https://docs.aws.amazon.com/whitepapers/latest/architecting-for-the-cloud-aws-best-practices/security.html) |
| **Thu** | - Create Launch Template `huylam-launch-template` (`lt-064a116476cc47e0a`).<br>- Specify Amazon Linux 2023 AMI and `t3.micro` hardware type.<br>- Develop User Data script retrieving IMDSv2 tokens and rendering student identity data. | Standardized web server deployment template; injected dynamic Instance ID, Private IP, and AZ into the web page. | [Lab 000006](https://000006.awsstudygroup.com) |
| **Fri** | - Create Target Group `huylam-alb-tg` with HTTP port 80 and Health Check path `/`.<br>- Create Internet-facing ALB `huylam-alb` mapped across 2 Multi-AZ Public Subnets.<br>- Configure HTTP:80 Listener forwarding to `huylam-alb-tg`. | Completed load balancing infrastructure; ALB provisioned with public DNS name and reached Active state. | [AWS ALB Getting Started](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/application-load-balancer-getting-started.html) |
| **Sat** | - Create Auto Scaling Group `huylam-asg` (Desired: 2, Min: 1, Max: 4).<br>- Associate with Launch Template and Target Group `huylam-alb-tg`.<br>- Monitor automated instance provisioning across 2 Availability Zones. | Automated provisioning of 2 instances: `i-01abe8b9b987aad60` (`ap-southeast-1a`) and `i-0e633e2910dd9ea8f` (`ap-southeast-1b`). | [Lab 000006](https://000006.awsstudygroup.com) |
| **Sun** | - Target Health check: Verified 2/2 targets reached Healthy status.<br>- Performed browser-based round-robin load distribution tests.<br>- Executed FinOps teardown: Deleted ASG, terminated instances, deleted ALB and Target Group.<br>- Compiled technical report and deployed documentation. | Successfully validated fault tolerance and load balancing; safely decommissioned compute resources to maintain 0 USD cost. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

---

### Technical Specifications Verified on AWS:

#### 1. Identity & Region:
- **AWS Account ID**: `677994024390`
- **Account Name**: `huylam`
- **Executing IAM User**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **Associated VPC**: `huylam-vpc` (`vpc-0125f4d6db3fbffa6`)

#### 2. Two-Tier Security Groups:
* **ALB Security Group (`huylam-alb-sg` - ID: `sg-066b3dc2f4c6b4c86`)**:
  - Inbound Rules: HTTP (TCP 80), Source `0.0.0.0/0` (Accepts inbound public traffic).
  - Outbound Rules: All traffic to `0.0.0.0/0` (Forwards requests to backend instances).
* **ASG Web Security Group (`huylam-asg-web-sg` - ID: `sg-0a9dede4860cae619`)**:
  - Inbound Rule 1: HTTP (TCP 80), Source `sg-066b3dc2f4c6b4c86` (`huylam-alb-sg`) - Restricts HTTP access strictly to load balancer traffic.
  - Inbound Rule 2: SSH (TCP 22), Source `0.0.0.0/0` - Remote administration access.
  - Outbound Rules: All traffic to `0.0.0.0/0` - Allows OS package installation and software updates.

#### 3. EC2 Launch Template:
- **Launch Template ID**: `lt-064a116476cc47e0a`
- **Name**: `huylam-launch-template`
- **Default Version**: Version 1
- **Amazon Machine Image (AMI)**: `ami-095f155a67469a548` (Amazon Linux 2023 Kernel 6.1 x86_64)
- **Instance Type**: `t3.micro` (AWS Free Tier eligible)
- **Associated Security Group**: `sg-0a9dede4860cae619` (`huylam-asg-web-sg`)
- **Bootstrap Script (User Data)**:
  - Generates an IMDSv2 session token via HTTP PUT request to `http://169.254.169.254/latest/api/token` with 21,600s TTL.
  - Retrieves dynamic instance metadata: `instance-id`, `local-ipv4`, and `placement/availability-zone`.
  - Automatically installs and enables the Apache HTTP Server (`httpd`).
  - Publishes a customized web page presenting student identity details for Lam Quang Huy (Student ID: `0212267`), Class 67CS - HUCE along with active server metadata.

#### 4. Target Group:
- **Target Group ARN**: `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-alb-tg/01224986629b1f06`
- **Name**: `huylam-alb-tg`
- **Target Type**: `instance`
- **Protocol & Port**: `HTTP:80`
- **Associated VPC**: `vpc-0125f4d6db3fbffa6` (`huylam-vpc`)
- **Health Check Path**: `/`
- **Health Check Protocol**: `HTTP`
- **Target Status**: 2/2 targets registered and verified `healthy`.

#### 5. Application Load Balancer:
- **ALB ARN**: `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:loadbalancer/app/huylam-alb/1d3845375aef8945`
- **Name**: `huylam-alb`
- **Scheme**: `internet-facing`
- **IP Address Type**: `ipv4`
- **Associated VPC**: `vpc-0125f4d6db3fbffa6` (`huylam-vpc`)
- **Subnet Mapping**:
  - `subnet-0efa7c3a5818035dc` (`huylam-subnet-public1-ap-southeast-1a`)
  - `subnet-0e07eb2fd44d1ac91` (`huylam-subnet-public2-ap-southeast-1b`)
- **Associated Security Group**: `sg-066b3dc2f4c6b4c86` (`huylam-alb-sg`)
- **DNS Name**: `huylam-alb-1974057692.ap-southeast-1.elb.amazonaws.com`
- **Listener**: HTTP:80 forwarding to `huylam-alb-tg`
- **Operating State**: `active`

#### 6. Auto Scaling Group:
- **ASG Name**: `huylam-asg`
- **Associated Launch Template**: `huylam-launch-template` (Version 1)
- **Active Subnets**: 2 Multi-AZ Public Subnets (`subnet-0efa7c3a5818035dc`, `subnet-0e07eb2fd44d1ac91`)
- **Attached Target Group**: `huylam-alb-tg`
- **Health Check Type**: `ELB`
- **Health Check Grace Period**: 300 seconds
- **Capacity Sizing**:
  - Desired Capacity: `2` (Maintains 2 healthy instances continuously)
  - Minimum Capacity: `1` (Prevents scaling below 1 instance during off-peak)
  - Maximum Capacity: `4` (Allows horizontal expansion up to 4 instances under load)
- **Auto-provisioned Backend Instances**:
  - Instance 1: `i-01abe8b9b987aad60` | Availability Zone: `ap-southeast-1a` | Private IP: `10.0.9.229` | Public IP: `47.129.221.209`
  - Instance 2: `i-0e633e2910dd9ea8f` | Availability Zone: `ap-southeast-1b` | Private IP: `10.0.25.48` | Public IP: `54.255.196.87`

---

### Implementation Evidence Screenshots:

All screenshots below were captured directly from the AWS Management Console and active testing browsers, annotated with distinct red boundary boxes highlighting account identity `huylam (677994024390)`, region `ap-southeast-1`, and key configuration parameters:

#### 1. Security Group Creation for Application Load Balancer (`huylam-alb-sg`):
- **Description**: Configuring the edge firewall accepting HTTP traffic from the public Internet on TCP port 80.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, security group name `huylam-alb-sg`, VPC ID `vpc-0125f4d6db3fbffa6`, and Inbound Rules allowing `0.0.0.0/0`.

![ALB Security Group Configuration](/images/week4/01-alb-security-group.png)

---

#### 2. Security Group Creation for Auto Scaling Web Instances (`huylam-asg-web-sg`):
- **Description**: Restricting backend HTTP ingress strictly to traffic originating from the ALB Security Group (`sg-066b3dc2f4c6b4c86`), preventing direct access from the public Internet.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, name `huylam-asg-web-sg`, and inbound rule bound to `huylam-alb-sg`.

![ASG Web Security Group Configuration](/images/week4/02-asg-security-group.png)

---

#### 3. Launch Template Configuration Details (`huylam-launch-template`):
- **Description**: Setting up the instance provisioning template with Amazon Linux 2023 AMI (`ami-095f155a67469a548`) and `t3.micro` instance type.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, Launch template name `huylam-launch-template`, AMI ID, and instance type specification.

![Launch Template Details](/images/week4/03-launch-template-details.png)

---

#### 4. Automated User Data Script with IMDSv2:
- **Description**: Automating web server installation and metadata retrieval using IMDSv2 session tokens.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, Advanced Details section, and User data script content.

![User Data Script with IMDSv2](/images/week4/04-launch-template-user-data.png)

---

#### 5. Launch Template Successfully Created:
- **Description**: Confirmation banner indicating successful creation of `huylam-launch-template` Version 1 ready for Auto Scaling integration.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, green success notification banner, and action links.

![Launch Template Created Successfully](/images/week4/05-launch-template-created-success.png)

---

#### 6. Target Group Creation for ALB (`huylam-alb-tg`):
- **Description**: Registering the Target Group of type Instance on HTTP:80 within `huylam-vpc` for load balancer request routing.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, creation confirmation, Target Group ARN, protocol, and VPC attributes.

![Target Group Created Successfully](/images/week4/06-target-group-created.png)

---

#### 7. Multi-AZ Network Mapping for Application Load Balancer:
- **Description**: Mapping `huylam-alb` across 2 Public Subnets in `ap-southeast-1a` and `ap-southeast-1b`, associated with `huylam-alb-sg`.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, selected subnet list, and associated security group.

![Multi-AZ Network Mapping for ALB](/images/week4/07-alb-create-network-mapping.png)

---

#### 8. Application Load Balancer Details and Routing Listener:
- **Description**: Verification of active load balancer parameters, public DNS name, and HTTP:80 forward listener to `huylam-alb-tg`.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, success banner, DNS Name `huylam-alb-1974057692.ap-southeast-1.elb.amazonaws.com`, and Listeners and rules list.

![ALB Details and DNS Name](/images/week4/08-alb-created-details-active.png)

---

#### 9. Auto Scaling Group Configuration and Capacity Sizing (`huylam-asg`):
- **Description**: Provisioning the Auto Scaling Group associated with `huylam-launch-template`, deployed across 2 Multi-AZ Public Subnets.
- **Red highlighted areas**: Account badge `huylam (677994024390)`, ASG name `huylam-asg`, Launch Template link, and Subnet CIDRs `10.0.0.0/20` and `10.0.16.0/20`.

![Auto Scaling Group Configuration](/images/week4/09-asg-configuration-and-capacity.png)

---

#### 10. Target Group Health Status Verification:
- **Description**: Verification of the Targets tab on `huylam-alb-tg`, demonstrating that both auto-provisioned EC2 instances passed ELB health checks (`2/2 Healthy`).
- **Red highlighted areas**: Account badge `huylam (677994024390)`, metrics showing `2 Total targets`, `2 Healthy`, attached ALB `huylam-alb`, and VPC `vpc-0125f4d6db3fbffa6`.

![Target Group Health Check Verification](/images/week4/10-target-group-healthy-instances.png)

---

#### 11. Live Browser Load Balancing Test: Response from Availability Zone `ap-southeast-1b`:
- **Description**: Accessing the ALB public DNS name on a web browser, routing to backend instance `i-0e633e2910dd9ea8f` in `ap-southeast-1b`.
- **Red highlighted areas**: Browser address bar with ALB DNS Name, student identity card for Lam Quang Huy (ID: `0212267`), Instance ID `i-0e633e2910dd9ea8f`, and AZ `ap-southeast-1b`.

![Browser Access via ALB - Response from Node 1b](/images/week4/11-alb-browser-round-robin-az1.png)

---

#### 12. Live Browser Load Balancing Test: Round-Robin Response from Availability Zone `ap-southeast-1a`:
- **Description**: Upon page reload, the ALB round-robin algorithm directs the subsequent request to the second backend instance `i-01abe8b9b987aad60` in `ap-southeast-1a`.
- **Red highlighted areas**: Browser address bar with ALB DNS Name, student identity card for Lam Quang Huy (ID: `0212267`), Instance ID `i-01abe8b9b987aad60`, and AZ `ap-southeast-1a`.

![Browser Access via ALB - Response from Node 1a](/images/week4/12-alb-browser-round-robin-az2.png)

---

### Empirical Testing and Technical Measurements:

#### 1. Target Health Verification via AWS CLI:
Querying live target health using the AWS CLI:
```bash
aws elbv2 describe-target-health \
  --target-group-arn arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-alb-tg/01224986629b1f06 \
  --query "TargetHealthDescriptions[*].[Target.Id,Target.Port,TargetHealth.State]" \
  --output table
```
*Recorded output*:
```text
---------------------------------------------
|            DescribeTargetHealth           |
+----------------------+-----+--------------+
|  i-01abe8b9b987aad60 |  80 |  healthy     |
|  i-0e633e2910dd9ea8f |  80 |  healthy     |
+----------------------+-----+--------------+
```
*Assessment*: Both backend instances automatically provisioned by the ASG passed ELB health checks and are actively serving traffic.

#### 2. Round-Robin Distribution Verification via cURL:
Dispatching repeated HTTP requests to the ALB DNS name:
```bash
for i in {1..4}; do
  curl -s http://huylam-alb-1974057692.ap-southeast-1.elb.amazonaws.com | grep -E "Instance ID|Availability Zone"
  echo "---"
done
```
*Recorded output*:
```text
  Instance ID: i-0e633e2910dd9ea8f
  Availability Zone: ap-southeast-1b
---
  Instance ID: i-01abe8b9b987aad60
  Availability Zone: ap-southeast-1a
---
  Instance ID: i-0e633e2910dd9ea8f
  Availability Zone: ap-southeast-1b
---
  Instance ID: i-01abe8b9b987aad60
  Availability Zone: ap-southeast-1a
---
```
*Assessment*: The ALB round-robin algorithm evenly alternates traffic between instances across both independent Availability Zones.

#### 3. Security Isolation Verification on Backend Instances:
Testing direct HTTP access from the Internet to the backend instance's public IP (`47.129.221.209`):
```bash
curl --connect-timeout 5 http://47.129.221.209
```
*Recorded output*:
```text
curl: (28) Failed to connect to 47.129.221.209 port 80: Connection timed out
```
*Assessment*: Direct public connections are blocked by `huylam-asg-web-sg`, enforcing all inbound traffic to route through the Application Load Balancer.

---

### Architecture Comparison: Single Instance vs. Multi-AZ Auto Scaling + ALB:

| Criteria | Single EC2 Instance | Multi-AZ Auto Scaling + ALB |
| :--- | :--- | :--- |
| **Availability** | Low. Failure of the host or Availability Zone results in complete service outage. | **High Availability (99.99%)**. Traffic automatically redirects to healthy instances in the surviving zone. |
| **Scalability** | Vertical only (resizing instance CPU/RAM), requiring planned downtime. | **Dynamic Horizontal Scaling**. Automatically adds or terminates instances based on real-time load. |
| **Network Security** | Instance must expose ports directly to the Internet, increasing attack surface. | **Two-Tier Isolation**. Backend instances remain protected behind the load balancer layer. |
| **Traffic Distribution** | None. A single instance absorbs all incoming traffic. | **Balanced Utilization**. Round-robin algorithms distribute requests evenly across healthy nodes. |
| **Operational Effort** | Manual. Replacing failed nodes and scaling requires administrator intervention. | **Fully Automated**. Launch Templates and ASG handle node lifecycle, scaling, and self-healing. |

---

### FinOps Cost Governance Practices:

1. **Understanding Application Load Balancer Base Cost**: Unlike standard VPC networking resources which carry no hourly fee, an Application Load Balancer incurs a base hourly charge of approximately 0.0225 USD per hour (~16.20 USD per month) plus Load Balancer Capacity Units (LCUs).
2. **Post-Validation Resource Teardown Procedure**:
   - To avoid unwanted costs once validation was achieved, all billable resources were deleted immediately in dependency order:
     - Deleting Auto Scaling Group: `aws autoscaling delete-auto-scaling-group --auto-scaling-group-name huylam-asg --force-delete` (automatically terminated all backend EC2 instances).
     - Deleting Application Load Balancer: `aws elbv2 delete-load-balancer --load-balancer-arn arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:loadbalancer/app/huylam-alb/1d3845375aef8945`.
     - Deleting Target Group: `aws elbv2 delete-target-group --target-group-arn arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-alb-tg/01224986629b1f06`.
     - Deleting Launch Template: `aws ec2 delete-launch-template --launch-template-id lt-064a116476cc47e0a`.
     - Deleting associated Security Groups to leave the VPC in a clean state.
3. **FinOps Result**: All billable compute resources were safely decommissioned, guaranteeing zero unexpected charges against the AWS Free Tier budget.

---

### Key Takeaways & Conclusion:
1. **Security Advantage of IMDSv2**: Leveraging IMDSv2 session tokens protects against SSRF vulnerabilities while allowing instance bootstrap scripts to discover their runtime environment dynamically.
2. **Security Group Chaining**: Using the ALB security group as the sole authorized source for backend security groups is an AWS best practice that eliminates fragile IP-based firewall maintenance.
3. **Multi-AZ as the Baseline for High Availability**: Distributing load balancers, subnets, and compute groups across at least two independent Availability Zones is essential for building resilient, enterprise-ready cloud solutions.