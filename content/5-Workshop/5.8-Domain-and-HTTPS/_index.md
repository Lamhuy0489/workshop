---
title: "Domain & Public URL"
date: 2026-09-23
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

### Module Objective

Verify the public DNS resolution of the Application Load Balancer (ALB), validate end-to-end traffic routing from the public Internet into the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**, and explore custom domain integration options.

---

## 1. Public Traffic Distribution Overview

When an Application Load Balancer is deployed in Internet-facing scheme, AWS dynamically allocates a canonical DNS record (distributed alias A records) conforming to:

```text
huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

Key technical advantages of ALB DNS resolution:
- **Dynamic IP Management & Multi-AZ Distribution**: AWS dynamically associates and balances traffic across public IP nodes representing independent Availability Zones (`ap-southeast-1a` and `ap-southeast-1b`).
- **High Availability & Fault Recovery**: Should an availability zone encounter hardware failure, Route 53 health monitoring drops the impaired IP endpoint within seconds.
- **Global Ingress Reachability**: Clients across diverse network topologies (broadband, mobile cellular networks, enterprise LANs) connect seamlessly without specialized VPN configurations.

---

## 2. Hands-on Execution Steps

This module comprises the following practical section:

- **[5.8.1 DNS Resolution & Public Endpoint Validation](5.8.1-configure-public-dns/)**: Utilizing network diagnostic utilities (`dig`, `nslookup`, `curl`) and web browsers to validate public endpoint availability.

---

## 3. Expected Outcomes

Upon completing this module, you have:
- Verified active DNS resolution mapping to Multi-AZ load balancer IPs.
- Validated the complete HTTP request pipeline: Port 80 ingress -> Port 5000 forward -> 302 redirection -> 200 OK rendering on `/login`.
- Confirmed global accessibility of the Web Studio application.