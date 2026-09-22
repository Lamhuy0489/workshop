---
title: "Configure Load Balancer"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

### Hands-on Objective

Provision a Target Group on port 5000 and configure an Internet-facing Application Load Balancer (ALB) distributed across 2 Availability Zones (Multi-AZ) to route incoming traffic for the Web Studio platform.

---

## 1. Provisioning Target Group (huylam-ocr-tg)

The Target Group specifies backend destination instances and periodic health evaluation policies.

### AWS Management Console Procedure:
1. Sign in to the AWS Console in region **ap-southeast-1 (Singapore)**.
2. Navigate to: **EC2 -> Target Groups -> Create target group**.
3. **Step 1: Specify group details**:
   * **Target type**: Select **Instances**.
   * **Target group name**: `huylam-ocr-tg`.
   * **Protocol**: `HTTP`, **Port**: `5000`.
   * **IP address type**: `IPv4`.
   * **VPC**: Ensure you select **`huylam-vpc`** (Critical: Default VPC will prevent instances in custom VPC from displaying).
   * **Protocol version**: `HTTP1`.
4. **Health checks**:
   * **Health check protocol**: `HTTP`.
   * **Health check path**: Enter **`/login`** (Architectural Rationale: Web Studio redirects 302 from root `/` to `/login` which returns HTTP 200 OK. Targeting `/login` allows the Target Group to transition to Healthy immediately).
   * Expand **Advanced health check settings**:
     * **Healthy threshold**: `2`.
     * **Unhealthy threshold**: `2`.
     * **Timeout**: `5 seconds`.
     * **Interval**: `30 seconds`.
     * **Success codes**: `200` (or `200,302`).
5. Click **Next**.
6. On **Step 2: Register targets**, skip for now (we register the EC2 host in the subsequent module).
7. Click **Create target group**.

![Target Group Created Successfully](/images/week12/05-target-group-created.png)

---

## 2. Provisioning Application Load Balancer (huylam-ocr-alb)

The Application Load Balancer operates as the centralized Layer 7 ingress gateway:

### Step-by-Step Procedure:
1. Navigate to: **EC2 Console -> Load Balancers -> Create load balancer**.
2. Under **Application Load Balancer**, click **Create**.
3. **Basic configuration**:
   * **Load balancer name**: `huylam-ocr-alb`.
   * **Scheme**: **Internet-facing**.
   * **IP address type**: **IPv4**.
4. **Network mapping**:
   * **VPC**: Select **`huylam-vpc`**.
   * **Mappings (Minimum 2 Availability Zones)**:
     * Check **`ap-southeast-1a`**, select Subnet: `huylam-subnet-public1-ap-southeast-1a`.
     * Check **`ap-southeast-1b`**, select Subnet: `huylam-subnet-public2-ap-southeast-1b`.
5. **Security groups**:
   * Remove the default security group (`default`).
   * Select the dedicated ALB group: **`huylam-alb-sg`** (`sg-0dca819306a96bfdb`).
6. **Listeners and routing**:
   * **Protocol**: `HTTP`, **Port**: `80`.
   * **Default action**: Select **Forward to** and pick Target Group **`huylam-ocr-tg`**.
7. Review parameters and click **Create load balancer**.

---

## 3. Verifying Load Balancer Status

1. In the Load Balancers table, select `huylam-ocr-alb`.
2. Wait 1 to 2 minutes until **Status** transitions from `Provisioning` to **Active**.

![Application Load Balancer Created and Active](/images/week12/06-alb-created-active.png)

3. Under **Details**, record the allocated Public DNS endpoint:

```text
DNS name: huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

This endpoint allows global Internet users to access Web Studio over standard HTTP port 80.

---

## 4. Expected Outcomes

Upon completing this section:
- Target Group `huylam-ocr-tg` is created on port 5000 targeting health path `/login`.
- Application Load Balancer `huylam-ocr-alb` is **Active** across 2 Availability Zones.
- HTTP:80 Listener forwards traffic into the Target Group.
- Public DNS endpoint is allocated.