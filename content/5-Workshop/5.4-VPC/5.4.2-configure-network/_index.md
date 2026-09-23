---
title: "Configure Network"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

### Hands-on Objective

Attach an Internet Gateway, configure public route tables, and construct defense-in-depth Security Group Chaining to protect the Web Studio application host behind the Application Load Balancer.

---

## 1. Configuring Internet Gateway (huylam-igw)

The Internet Gateway provides bidirectional communication between resources inside the VPC and the public Internet.

### Step-by-Step Procedure:
1. Navigate to: **VPC Console -> Internet Gateways -> Create internet gateway**.
2. Name tag: `huylam-igw`.
3. Click **Create internet gateway**.
4. Once created, select **Actions -> Attach to VPC**.
5. Select target VPC: `huylam-vpc` and click **Attach internet gateway**.
6. Verify status transitions to **Attached**.

---

## 2. Configuring Public Route Table (huylam-rtb-public)

The route table governs outbound traffic redirection from subnets toward the Internet via `huylam-igw`.

### Step-by-Step Procedure:
1. Navigate to: **VPC Console -> Route Tables -> Create route table**.
2. Name tag: `huylam-rtb-public`, select VPC: `huylam-vpc`.
3. Click **Create route table**.
4. Switch to **Routes -> Edit routes**:
   * Click **Add route**.
   * **Destination**: `0.0.0.0/0` (All external IPv4 traffic).
   * **Target**: Select **Internet Gateway** and select `huylam-igw`.
   * Click **Save changes**.
5. Switch to **Subnet associations -> Edit subnet associations**:
   * Select both subnets: `huylam-subnet-public1-ap-southeast-1a` and `huylam-subnet-public2-ap-southeast-1b`.
   * Click **Save associations**.

---

## 3. Establishing Defense-in-Depth Security Group Chaining

Implementing the AWS Well-Architected Principle of Least Privilege:

![Chained Security Groups Defense-in-Depth Architecture Blueprint](/images/architecture/aws-security-group-chaining.png?width=100%&classes=border,shadow)

> [!NOTE] Security Group Chaining Diagram File Formats
> * **High-Resolution Render**: `/images/architecture/aws-security-group-chaining.png` (Retina 1180x560)
> * **Scalable Vector Graphic**: `/images/architecture/aws-security-group-chaining.svg`
> * **Editable Source Diagram**: `/images/architecture/aws-security-group-chaining.drawio` (Directly importable into [diagrams.net](https://app.diagrams.net/) with official AWS4 stencils).

### Step 3.1: Create ALB Security Group (huylam-alb-sg)
1. Navigate to: **EC2 Console -> Network & Security -> Security Groups -> Create security group**.
2. **Security group name**: `huylam-alb-sg`.
3. **Description**: `Security group for Application Load Balancer`.
4. **VPC**: Select `huylam-vpc`.
5. **Inbound rules**:
   * Type: **HTTP**, Port: `80`, Source: `Anywhere-IPv4` (`0.0.0.0/0`), Description: `Allow public HTTP access`.
6. **Outbound rules**:
   * Leave default: **All traffic** (`0.0.0.0/0`).
7. Click **Create security group**.
8. Record the generated Group ID (e.g., `sg-0dca819306a96bfdb`).

![Security Group for Application Load Balancer](/images/week12/01-alb-security-group-created.png)

### Step 3.2: Create Web Server Security Group (huylam-web-sg)
1. Click **Create security group**.
2. **Security group name**: `huylam-web-sg`.
3. **Description**: `Security group for EC2 Web Studio behind ALB`.
4. **VPC**: Select `huylam-vpc`.
5. **Inbound rules**:
   * Rule 1 (Web Studio application ingress):
     * Type: **Custom TCP**, Port: `5000`.
     * Source: Select **Custom** and enter the ALB Security Group ID (`huylam-alb-sg` or `sg-0dca819306a96bfdb`).
     * Description: `Allow traffic only from ALB`.
   * Rule 2 (SSH administration fallback):
     * Type: **SSH**, Port: `22`, Source: `0.0.0.0/0` (or your administrator IP).
6. **Outbound rules**:
   * Add rule: Type: **All traffic**, Destination: `0.0.0.0/0` (Enables host to pull dependencies via package managers and GitHub).
7. Click **Create security group**.

![Security Group for EC2 Web Server](/images/week12/02-web-security-group-created.png)

---

## 4. Expected Outcomes

Upon completing this section:
- `huylam-igw` is attached to `huylam-vpc`.
- `huylam-rtb-public` handles external routing for both Multi-AZ subnets.
- `huylam-alb-sg` admits incoming HTTP port 80 traffic.
- `huylam-web-sg` enforces strict network isolation on internal port 5000.