---
title: "Create VPC"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

### Hands-on Objective

Provision an Amazon Virtual Private Cloud (VPC) named `huylam-vpc` in region `ap-southeast-1` (Singapore) with 2 Multi-AZ Public Subnets distributed across independent Availability Zones (`ap-southeast-1a` and `ap-southeast-1b`) to support fault-tolerant load balancing.

---

## 1. Creating Amazon VPC (huylam-vpc)

### Step-by-Step AWS Management Console Procedure:
1. Sign in to the AWS Management Console in region **ap-southeast-1 (Singapore)**.
2. Navigate to: **VPC -> Your VPCs -> Create VPC**.
3. Select **VPC only** and configure the parameters:

| Attribute | Configured Value | Architectural Rationale |
| :--- | :--- | :--- |
| **Resources to create** | VPC only | Standalone custom virtual cloud network |
| **Name tag** | `huylam-vpc` | Project naming convention identifier |
| **IPv4 CIDR block** | `10.0.0.0/16` | Dedicated address space providing 65,536 private IP addresses |
| **IPv6 CIDR block** | No IPv6 CIDR block | Exclusively operating IPv4 network stack |
| **Tenancy** | Default | Shared tenancy adhering to FinOps budget constraints |

4. Click **Create VPC**.

---

## 2. Enabling DNS Hostnames & DNS Resolution

To ensure seamless internal resolution for EC2 instances and Application Load Balancer:
1. In the **Your VPCs** table, select `huylam-vpc`.
2. Click **Actions -> Edit VPC settings**.
3. Under **DNS settings**, check both:
   * **Enable DNS resolution**: Permits internal AWS DNS lookups.
   * **Enable DNS hostnames**: Assigns public DNS hostnames to instances with public IPs.
4. Click **Save changes**.

---

## 3. Provisioning 2 Multi-AZ Public Subnets

Application Load Balancer strictly requires a minimum of 2 Subnets spanning at least two independent Availability Zones.

### Provisioning Subnet 1 (`ap-southeast-1a`):
* Navigate to **VPC -> Subnets -> Create subnet**.
* Select **VPC ID**: `huylam-vpc`.
* **Subnet name**: `huylam-subnet-public1-ap-southeast-1a`.
* **Availability Zone**: `ap-southeast-1a`.
* **IPv4 CIDR block**: `10.0.8.0/21` (2,048 available IP addresses).
* Enable Public IPv4 Auto-assignment: Select subnet -> **Actions -> Edit subnet settings -> Enable auto-assign public IPv4 address**.

### Provisioning Subnet 2 (`ap-southeast-1b`):
* Navigate to **VPC -> Subnets -> Create subnet**.
* Select **VPC ID**: `huylam-vpc`.
* **Subnet name**: `huylam-subnet-public2-ap-southeast-1b`.
* **Availability Zone**: `ap-southeast-1b`.
* **IPv4 CIDR block**: `10.0.16.0/21` (2,048 available IP addresses).
* Enable Public IPv4 Auto-assignment: Select subnet -> **Actions -> Edit subnet settings -> Enable auto-assign public IPv4 address**.

---

## 4. Expected Outcomes

Upon completing this section:
- VPC `huylam-vpc` (`10.0.0.0/16`) is operational in **Available** state.
- DNS Resolution and DNS Hostnames are enabled.
- 2 Multi-AZ Public Subnets are successfully allocated for Internet Gateway routing and Application Load Balancer association.