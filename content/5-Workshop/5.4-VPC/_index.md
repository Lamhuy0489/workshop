---
title: "Networking Infrastructure"
date: 2026-09-23
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Module Objective

Design and provision an enterprise-grade Virtual Private Cloud (Amazon VPC) architecture supporting Multi-Availability Zone redundancy and defense-in-depth Security Group Chaining to protect the Web Studio application host.

---

## 1. Networking Architecture Overview

The cloud network infrastructure serves as the architectural foundation ensuring high availability, fault tolerance, and security for the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:

- **Virtual Private Cloud (`huylam-vpc`)**: Dedicated CIDR block `10.0.0.0/16` providing an isolated network boundary in the AWS cloud.
- **Multi-AZ Availability Partitioning**:
  - Public Subnet 1: `huylam-subnet-public1-ap-southeast-1a` (`10.0.8.0/21`) in `ap-southeast-1a`.
  - Public Subnet 2: `huylam-subnet-public2-ap-southeast-1b` (`10.0.16.0/21`) in `ap-southeast-1b`.
- **Internet Gateway (`huylam-igw`)**: Provides bidirectional Internet connectivity for public subnets.
- **Route Table (`huylam-rtb-public`)**: Routes outbound default traffic `0.0.0.0/0` via the Internet Gateway.
- **Security Group Chaining Architecture**:
  - `huylam-alb-sg`: Ingests HTTP port 80 traffic from global Internet clients.
  - `huylam-web-sg`: Accepts TCP port 5000 ingress strictly from the ALB Security Group (`huylam-alb-sg`), preventing port scanning from external networks.

### Multi-AZ Networking & Chained Security Groups Blueprint:

![Amazon VPC Multi-AZ & Chained Security Groups Blueprint](/images/architecture/aws-vpc-network-architecture.png?width=100%&classes=border,shadow)

> [!NOTE] Networking Diagram File Formats
> * **High-Resolution Render**: `/images/architecture/aws-vpc-network-architecture.png` (Retina 1380x820)
> * **Scalable Vector Graphic**: `/images/architecture/aws-vpc-network-architecture.svg`
> * **Editable Source Diagram**: `/images/architecture/aws-vpc-network-architecture.drawio` (Directly importable into [diagrams.net](https://app.diagrams.net/) with official AWS4 stencils).

---

## 2. Hands-on Execution Steps

This module is organized into two sequential sections:

- **[5.4.1 Provisioning Multi-AZ Amazon VPC](5.4.1-create-vpc/)**: Creating `huylam-vpc` and configuring subnets across multiple Availability Zones.
- **[5.4.2 Configuring Network Routing & Chained Security Groups](5.4.2-configure-network/)**: Attaching Internet Gateway, defining route tables, and binding layered Inbound/Outbound security rules for ALB and EC2.

---

## 3. Expected Outcomes

Upon completing this networking module, you have:
- An active Amazon VPC (`huylam-vpc`) operating on `10.0.0.0/16`.
- 2 Multi-AZ Public Subnets spanning `ap-southeast-1a` and `ap-southeast-1b`.
- An attached and routed Internet Gateway (`huylam-igw`).
- Layered Security Groups (`huylam-alb-sg` and `huylam-web-sg`) establishing a defense-in-depth security perimeter.