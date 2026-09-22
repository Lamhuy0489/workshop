---
title: "DNS Resolution & Public URL Verification"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.8.1. </b> "
---

### Hands-on Objective

Inspect canonical DNS resolution for the Application Load Balancer, conduct external connectivity verification using command-line diagnostic tools (`dig`, `curl`), and validate Web Studio application responsiveness inside a web browser.

---

## 1. Validating Load Balancer DNS Resolution

The Application Load Balancer is assigned an AWS canonical DNS hostname:
```text
huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

### Step 1.1: Querying DNS A Records via dig
Open your terminal and query the DNS resolution:

```bash
dig huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com +short
```

**Expected Output**: Returns a set of public IPv4 addresses corresponding to load balancing nodes across `ap-southeast-1a` and `ap-southeast-1b`.

---

## 2. HTTP Telemetry Inspection via curl

### Step 2.1: Verifying Root Endpoint (/)
Send an HTTP HEAD request to the load balancer:

```bash
curl -I http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

**Recorded Empirical Response**:
```http
HTTP/1.1 302 FOUND
Date: Wed, 23 Sep 2026 01:41:24 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 199
Connection: keep-alive
Server: gunicorn
Location: /login
Vary: Cookie
```

**Technical Insights**:
- `HTTP/1.1 302 FOUND`: The load balancer successfully forwarded the request to EC2; the Flask framework detected an unauthenticated session and initiated a secure redirect to the login endpoint.
- `Server: gunicorn`: Directly confirms that requests are handled by the Gunicorn WSGI daemon on `huylam-ocr-web-server`.

---

### Step 2.2: Verifying Login Endpoint (/login)
Query the authentication endpoint:

```bash
curl -I http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com/login
```

**Recorded Empirical Response**:
```http
HTTP/1.1 200 OK
Date: Wed, 23 Sep 2026 01:41:34 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 6483
Connection: keep-alive
Server: gunicorn
Vary: Cookie
```

**Checkpoint**: Response code is `200 OK` delivering the full HTML application payload.

---

## 3. Real-World Web Browser Access

1. Launch a browser (Safari, Chrome) on a workstation or mobile device.
2. Enter the address:
   ```text
   http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
   ```
3. Confirm that:
   * The platform brand renders: **HYBRID OCR ENTERPRISE**.
   * Enterprise login credentials form is interactive.
   * Theme toggle (Light/Dark) and language selectors (VI/EN) are fully operational.
   * Initial page load latency remains under 50 ms.

![Authentication Portal via ALB Public DNS URL](/images/week12/09-browser-alb-public-dns-login.png)

4. Authenticate and enter the main Web Studio application:
   * Instantaneous SPA loading, featuring document dropzone, model switcher (Auto Hybrid, Bedrock, Gemini Flash), and split-view display.

![Live Studio Interface Operating Behind ALB](/images/week12/10-browser-alb-studio-live.png)

---

## 4. Custom Domain & HTTPS Extension Guidelines (Optional)

For organizations possessing a custom domain (e.g., `ocr.huylam.dev`), HTTPS can be provisioned at zero certificate cost:
1. **AWS Certificate Manager (ACM)**: Request a free public SSL/TLS certificate via DNS validation.
2. **Amazon Route 53**: Create an **A - Alias Record** pointing directly to Application Load Balancer `huylam-ocr-alb`.
3. **ALB HTTPS Listener**: Bind port 443 with the ACM certificate forwarding to Target Group `huylam-ocr-tg`.

---

## 5. Expected Outcomes

Upon completing this section:
- ALB public DNS hostname resolves reliably across public networks.
- HTTP 302 -> 200 OK redirection sequence is validated.
- Global users can access Web Studio directly across the Internet.