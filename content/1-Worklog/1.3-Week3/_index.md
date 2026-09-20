---
title: "Week 3 Worklog"
date: 2026-09-20
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:
* Master cloud network isolation architecture with Amazon Virtual Private Cloud (Amazon VPC).
* Design and deploy a production-grade Custom VPC with Multi-AZ High Availability (2 Availability Zones, 4 Subnets).
* Configure IPv4 CIDR segmentation for Public Subnets and Private Subnets.
* Create and attach an Internet Gateway (IGW) to the Custom VPC.
* Establish custom Route Tables for default internet routing (`0.0.0.0/0`) and intra-VPC local routing.
* Implement a multi-layered defense-in-depth security model using Security Groups (Stateful Firewall) and Network Access Control Lists (Stateless Firewall).
* Launch an Amazon EC2 instance inside the Custom VPC Public Subnet with automated public IPv4 addressing.
* Validate end-to-end network connectivity, DNS resolution, and latency performance via CLI and browser-based terminal.
* Practice cloud financial engineering (FinOps) by safely terminating compute resources post-validation to preserve AWS Free Tier allowances.

### Weekly Tasks & Execution Breakdown:

| Day | Task | Key Deliverables & Achievements | Reference Material |
| :--- | :--- | :--- | :--- |
| **Monday** | - Theoretical study of Amazon VPC, CIDR blocks, Subnetting, and IPv4 addressing.<br>- Plan Multi-AZ topology for Custom VPC: `10.0.0.0/16`. | Segmented 4 subnets: 2 Public (`/20`) and 2 Private (`/20`) across Availability Zones `ap-southeast-1a` and `ap-southeast-1b`. | [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/) |
| **Tuesday** | - Provision Custom VPC using "VPC and more" on AWS Management Console.<br>- Create and attach Internet Gateway `huylam-igw`.<br>- Inspect interactive VPC Resource Map. | Successfully provisioned VPC `vpc-0125f4d6db3fbffa6`, attached IGW `igw-0b9db3a6eac29ede9`, with automated subnet and route table wiring. | [Lab 000003](https://000003.awsstudygroup.com) |
| **Wednesday** | - Enable automated public IPv4 addressing on Public Subnet 1 (`huylam-subnet-public1-ap-southeast-1a`).<br>- Verify public route table routing `0.0.0.0/0` via IGW. | Enabled `MapPublicIpOnLaunch: true` ensuring compute resources launched in public subnet automatically receive public IPv4 addresses. | [Lab 000003](https://000003.awsstudygroup.com) |
| **Thursday** | - Study stateful virtual firewall mechanisms: Security Groups.<br>- Create Security Group `huylam-vpc-web-sg` (`sg-0dbd6bbde1b366070`) in `huylam-vpc`.<br>- Configure Inbound Rules: TCP 22 (SSH), TCP 80 (HTTP), ICMP IPv4 (Echo Request/Ping). | Security group successfully provisioned, ready to safeguard compute workloads at transport and application layers. | [AWS Security Groups Guide](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) |
| **Friday** | - Study stateless subnet packet filtering: Network ACLs (NACL).<br>- Inspect Default NACL `acl-09a50f9e28bc6477d` associated with all 4 subnets.<br>- Compare architectural differences between Security Groups and Network ACLs. | Mastered the defense-in-depth model: NACL acts as perimeter gatekeeper at subnet boundary, while Security Group enforces instance-level rules. | [AWS NACL Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) |
| **Saturday** | - Launch EC2 instance `huylam-vpc-test-server` (`i-02a465d3907141cfb`) inside Custom VPC.<br>- Establish session via EC2 Instance Connect.<br>- Execute outbound ICMP ping to 8.8.8.8 and HTTP header validation with `curl`. | Verification successful: 1.13 ms average RTT to Google Public DNS; HTTP 301 Moved Permanently response received from Amazon.com. | [Lab 000003](https://000003.awsstudygroup.com) |
| **Sunday** | - Measure inbound network latency from local development machine to EC2 public IPv4.<br>- Execute FinOps cleanup: Terminate EC2 test instance while keeping VPC, Subnets, and IGW active at 0 USD/month.<br>- Finalize technical documentation and deploy worklog. | Achieved 0% packet loss on external ping (~49 ms RTT); safely released compute resources to safeguard AWS Free Tier budget. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

---

### Verified Technical Parameters on AWS:

#### 1. Identity & Region:
- **AWS Account ID**: `677994024390`
- **Account Name**: `huylam`
- **Executing IAM User**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)

#### 2. Custom Virtual Private Cloud (Amazon VPC):
- **VPC Name**: `huylam-vpc`
- **VPC ID**: `vpc-0125f4d6db3fbffa6`
- **IPv4 CIDR Block**: `10.0.0.0/16` (65,536 available addresses)
- **Tenancy**: `Default`
- **DNS Resolution**: `Enabled`
- **DNS Hostnames**: `Enabled`
- **State**: `available`

#### 3. Internet Gateway:
- **Internet Gateway Name**: `huylam-igw`
- **Internet Gateway ID**: `igw-0b9db3a6eac29ede9`
- **Attachment State**: `attached` to VPC `vpc-0125f4d6db3fbffa6`

#### 4. Multi-AZ Subnet Architecture:
| Subnet Name | Subnet ID | Availability Zone | CIDR Block | Subnet Type | Auto-assign Public IP |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `huylam-subnet-public1-ap-southeast-1a` | `subnet-0efa7c3a5818035dc` | `ap-southeast-1a` | `10.0.0.0/20` | Public | **Yes** (`MapPublicIpOnLaunch: true`) |
| `huylam-subnet-public2-ap-southeast-1b` | `subnet-0e07eb2fd44d1ac91` | `ap-southeast-1b` | `10.0.16.0/20` | Public | **No** |
| `huylam-subnet-private1-ap-southeast-1a`| `subnet-08563e499271091ab` | `ap-southeast-1a` | `10.0.128.0/20`| Private | **No** |
| `huylam-subnet-private2-ap-southeast-1b`| `subnet-07e20dc44c40fac46` | `ap-southeast-1b` | `10.0.144.0/20`| Private | **No** |

#### 5. Route Tables:
* **Public Route Table (`huylam-rtb-public` - ID: `rtb-012d99ed0350e956f`)**:
  - Explicit Associations: `huylam-subnet-public1-ap-southeast-1a`, `huylam-subnet-public2-ap-southeast-1b`.
  - Routing Rules:
    - `10.0.0.0/16` -> `local` (Intra-VPC inter-subnet routing).
    - `0.0.0.0/0` -> `igw-0b9db3a6eac29ede9` (Default internet route via Internet Gateway).
* **Private Route Table 1 (`huylam-rtb-private1-ap-southeast-1a` - ID: `rtb-0d49944883c933d2a`)**:
  - Associated Subnet: `huylam-subnet-private1-ap-southeast-1a`.
  - Routing Rules: `10.0.0.0/16` -> `local` (Isolated from direct internet ingress/egress).
* **Private Route Table 2 (`huylam-rtb-private2-ap-southeast-1b` - ID: `rtb-00f6e53a047aafc1f`)**:
  - Associated Subnet: `huylam-subnet-private2-ap-southeast-1b`.
  - Routing Rules: `10.0.0.0/16` -> `local` (Isolated from direct internet ingress/egress).

#### 6. Security Group (`huylam-vpc-web-sg` - ID: `sg-0dbd6bbde1b366070`):
- **VPC Association**: `vpc-0125f4d6db3fbffa6`
- **Inbound Rules**:
  - SSH (TCP 22): Source `0.0.0.0/0` (Remote administration via SSH / EC2 Instance Connect).
  - HTTP (TCP 80): Source `0.0.0.0/0` (Public web traffic).
  - All ICMP - IPv4: Source `0.0.0.0/0` (ICMP Echo request and latency testing).
- **Outbound Rules**:
  - All Traffic: Destination `0.0.0.0/0` (Unrestricted outbound for package management and DNS queries).

#### 7. Network ACL (`acl-09a50f9e28bc6477d`):
- **VPC Association**: `vpc-0125f4d6db3fbffa6`
- **Associated Subnets**: All 4 Subnets within `huylam-vpc`.
- **Inbound Rules**:
  - Rule 100: All traffic from `0.0.0.0/0` -> `Allow`.
  - Rule `*`: All traffic -> `Deny` (Implicit default deny).
- **Outbound Rules**:
  - Rule 100: All traffic to `0.0.0.0/0` -> `Allow`.
  - Rule `*`: All traffic -> `Deny`.

#### 8. EC2 Validation Instance (`huylam-vpc-test-server` - ID: `i-02a465d3907141cfb`):
- **Instance Type**: `t3.micro` (AWS Free Tier eligible)
- **Operating System**: Amazon Linux 2023 AMI (`al2023-ami-2023.6.20260218.0-kernel-6.1-x86_64`)
- **VPC / Subnet**: `huylam-vpc` / `huylam-subnet-public1-ap-southeast-1a`
- **Public IPv4**: `54.151.162.47`
- **Private IPv4**: `10.0.14.174`
- **Security Group**: `huylam-vpc-web-sg`
- **Lifecycle Status**: Successfully launched (`running`), end-to-end verified, then cleanly terminated (`shutting-down` -> `terminated`).

---

### Proof of Work & Verifications:

#### 1. Custom VPC Configuration & Resource Preview (VPC and more)
- **Description**: Utilizing the unified "VPC and more" workflow to configure VPC name `huylam-vpc`, IPv4 CIDR `10.0.0.0/16`, 2 Availability Zones, 2 Public Subnets, and 2 Private Subnets.
- **Red Highlight**: Account identity `huylam (677994024390)`, Name tag, CIDR configuration, Availability Zone count, and real-time interactive Resource Map.

![Custom VPC Configuration Settings](/images/week3/01-vpc-create-settings-preview.png)

---

#### 2. NAT Gateway, VPC Endpoints & DNS Options Configuration
- **Description**: Practicing cloud financial management (FinOps), NAT Gateways and VPC Endpoints are set to `None` to prevent hourly charges. DNS Hostnames and DNS Resolution options are enabled.
- **Red Highlight**: Account badge `huylam (677994024390)`, NAT gateways (None), VPC endpoints (None), Enable DNS hostnames, Enable DNS resolution, and the *Create VPC* action button.

![NAT and DNS Options Configuration](/images/week3/02-vpc-create-nat-dns-options.png)

---

#### 3. Interactive VPC Resource Map
- **Description**: Inspecting `huylam-vpc` (`vpc-0125f4d6db3fbffa6`) on the AWS Console. The Resource Map illustrates the end-to-end topology across VPC, 4 Subnets, Route Tables, and Internet Gateway `huylam-igw`.
- **Red Highlight**: Account badge `huylam (677994024390)`, VPC ID details card, and the full architectural resource diagram.

![VPC Resource Map](/images/week3/03-vpc-resource-map.png)

---

#### 4. Enable Auto-Assign Public IPv4 on Public Subnet
- **Description**: Navigating to `huylam-subnet-public1-ap-southeast-1a` (`subnet-0efa7c3a5818035dc`), enabling the *Enable auto-assign public IPv4 address* setting to guarantee internet accessibility for instances launched in this subnet.
- **Red Highlight**: Account badge `huylam (677994024390)`, Subnet ID and Name, and the auto-assign IP checkbox setting.

![Subnet Auto-assign Public IP Settings](/images/week3/04-subnet-enable-auto-assign-public-ip.png)

---

#### 5. Configure Inbound Rules for Custom VPC Security Group
- **Description**: Creating Security Group `huylam-vpc-web-sg` within `huylam-vpc` with explicit Inbound Rules for SSH (22), HTTP (80), and All ICMP - IPv4 from source `0.0.0.0/0`.
- **Red Highlight**: Account badge `huylam (677994024390)`, Security Group Name, VPC association, and the 3 inbound rule entries.

![Security Group Inbound Rules](/images/week3/05-security-group-create-rules.png)

---

#### 6. Security Group Creation Success Confirmation
- **Description**: AWS confirmation displaying the successful provisioning of Security Group `huylam-vpc-web-sg` (`sg-0dbd6bbde1b366070`).
- **Red Highlight**: Account badge `huylam (677994024390)`, green success alert banner, Security Group Details, and the configured Inbound Rules table.

![Security Group Created Success](/images/week3/06-security-group-created-success.png)

---

#### 7. EC2 Launch Network Settings Configuration
- **Description**: Configuring network settings during EC2 launch wizard: Selecting `huylam-vpc`, Subnet `huylam-subnet-public1-ap-southeast-1a`, Auto-assign public IP Enabled, and attaching `huylam-vpc-web-sg`.
- **Red Highlight**: Account badge `huylam (677994024390)`, Network Settings panel, and instance summary configuration panel.

![EC2 Network Settings Configuration](/images/week3/07-ec2-launch-custom-vpc-network-settings.png)

---

#### 8. EC2 Instance Launch Initiation Success
- **Description**: AWS confirmation banner verifying the successful launch initiation of instance `i-02a465d3907141cfb`.
- **Red Highlight**: Account badge `huylam (677994024390)` and green *Successfully initiated launch of instance* alert.

![EC2 Launch Initiation Success](/images/week3/08-ec2-launch-success.png)

---

#### 9. EC2 Instance Summary Verification (Running State)
- **Description**: Instance `huylam-vpc-test-server` active in *Running* state with Public IPv4 `54.151.162.47`, Private IPv4 `10.0.14.174`, assigned to `huylam-vpc` and public subnet.
- **Red Highlight**: Account badge `huylam (677994024390)` and the complete Instance Summary card.

![EC2 Instance Summary](/images/week3/09-ec2-instance-summary-running.png)

---

#### 10. Network Connectivity & DNS Resolution Test via EC2 Instance Connect
- **Description**: Accessing terminal via EC2 Instance Connect, executing `ping -c 4 8.8.8.8` (0% loss, 1.13 ms avg RTT) and `curl -I https://amazon.com` (HTTP 301 response).
- **Red Highlight**: Account badge `huylam (677994024390)`, command execution output in terminal, and footer identification bar with Instance ID `i-02a465d3907141cfb` and Public IP `54.151.162.47`.

![EC2 Instance Connect Terminal Test](/images/week3/10-ec2-instance-connect-terminal-test.png)

---

#### 11. Custom VPC Network Access Control List (NACL) Inspection
- **Description**: Reviewing Default NACL `acl-09a50f9e28bc6477d` associated with all 4 subnets in `huylam-vpc`. Demonstrating Rule 100 (Allow all) and Rule `*` (Deny all).
- **Red Highlight**: Account badge `huylam (677994024390)`, NACL ID header, associated subnets count, and Inbound Rules table.

![VPC Network ACL Inspection](/images/week3/11-vpc-network-acl-inbound-rules.png)

---

### Empirical Testing & Network Metrics:

#### 1. Inbound Latency Measurement (Local to EC2):
```bash
ping -c 4 54.151.162.47
```
*Telemetry Results*:
```text
PING 54.151.162.47 (54.151.162.47): 56 data bytes
64 bytes from 54.151.162.47: icmp_seq=0 ttl=114 time=49.123 ms
64 bytes from 54.151.162.47: icmp_seq=1 ttl=114 time=48.910 ms
64 bytes from 54.151.162.47: icmp_seq=2 ttl=114 time=49.450 ms
64 bytes from 54.151.162.47: icmp_seq=3 ttl=114 time=49.020 ms

--- 54.151.162.47 ping statistics ---
4 packets transmitted, 4 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 48.910/49.126/49.450/0.201 ms
```
*Analysis*: Low-latency (~49 ms) trans-national connectivity from Vietnam to AWS Singapore Region (`ap-southeast-1`) with 0% packet loss. Confirms that ICMP Inbound rules are functioning properly.

#### 2. Outbound Internet Connectivity (EC2 to Internet):
```bash
ping -c 4 8.8.8.8
```
*Telemetry Results*:
```text
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=1.12 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=1.11 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=1.20 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=114 time=1.10 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 1.103/1.133/1.200/0.038 ms
```
*Analysis*: Sub-2ms latency to Google Public DNS validates efficient routing through `huylam-igw`.

#### 3. DNS Resolution & Web Ingress/Egress:
```bash
curl -I https://amazon.com
```
*Telemetry Results*:
```text
HTTP/1.1 301 Moved Permanently
Server: Server
Date: Sun, 20 Sep 2026 09:39:26 GMT
Content-Type: text/html
Connection: keep-alive
Location: https://www.amazon.com/
x-amz-rid: 7S4DNVXPQM3D0EMFTPF1
Vary: User-Agent,Accept-Encoding
```
*Analysis*: VPC DNS Resolver functions as expected, translating domain names and completing TLS handshakes smoothly.

---

### Architectural Comparison: Security Groups vs. Network ACLs

| Technical Criterion | Security Group (Virtual Firewall) | Network ACL (NACL) |
| :--- | :--- | :--- |
| **Operating Layer** | Operates at the virtual network interface (ENI / Instance level). | Operates at the Subnet boundary level. |
| **Connection State** | **Stateful**: Return traffic is automatically tracked and allowed regardless of rules. | **Stateless**: Inbound and Outbound traffic must be explicitly permitted in both directions. |
| **Rule Types** | Supports **Allow** rules only. Non-matching traffic is denied by default. | Supports both explicit **Allow** and **Deny** rules. |
| **Evaluation Order** | All rules are evaluated concurrently before granting or denying access. | Rules are processed sequentially in ascending order of rule numbers. |
| **Traffic Flow Position**| Filters packets after they have already cleared the Subnet NACL. | First line of perimeter defense upon entering or exiting a Subnet. |

---

### Financial Engineering & FinOps Best Practices:
1. **Zero NAT Gateway Overhead**: NAT Gateways cost approximately 0.045 USD/hour (~32.4 USD/month). By choosing `None`, recurring infrastructure overhead is avoided.
2. **Zero Base VPC Cost**: Core VPC primitives (VPC, Subnets, Route Tables, Internet Gateways, Security Groups, NACLs) are 100% free of charge when not bound to Elastic IPs or NAT Gateways.
3. **Automated Resource Deprovisioning**: Test instance `i-02a465d3907141cfb` was terminated immediately upon test completion, preventing compute leakage against AWS Free Tier limits.

---

### Key Takeaways & Conclusion:
1. **Production Multi-AZ Resilience**: Segmenting into 2 Public and 2 Private Subnets across independent Availability Zones forms the cornerstone for high availability and load-balanced architectures.
2. **Automated IP Assignment Awareness**: Ensuring `MapPublicIpOnLaunch` is enabled on public subnets avoids connectivity deadlocks during automated deployments.
3. **Defense-in-Depth Implementation**: Coordinating subnet-level NACL rules with instance-level Security Group policies guarantees enterprise-grade security posture on AWS.