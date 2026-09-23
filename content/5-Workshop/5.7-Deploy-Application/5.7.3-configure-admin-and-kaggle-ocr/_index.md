---
title: "Configure Admin & Kaggle GPU OCR"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 5.7.3. </b> "
---

### Hands-on Objective

Configure Administrator privileges, provision a dedicated vision inference server featuring **Qwen2.5-VL-7B-Instruct** on free Kaggle GPU virtual environments, establish a secure public egress via **Cloudflare Tunnel**, and integrate the generated endpoint into Web Studio to operate Tier 2 (Selective Vision OCR) at $0.00 cost.

---

## 1. Role of Kaggle GPU OCR in the Hybrid Architecture

In the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
* **Tier 1 (Fast-Path Native)**: Directly parses digital text and table structures in 0.1s - 0.3s/page on the EC2 host via PyMuPDF at zero cost.
* **Tier 2 (Selective Vision OCR)**: When encountering scanned image pages, handwritten forms, or complex blueprints, the system routes the page to Kaggle GPU for vision inference.

```text
                     [ Web Studio / ALB / EC2 ]
                                 │
                 ┌───────────────┴───────────────┐
                 ▼ (Digital Text Pages)          ▼ (Scanned Image Pages)
       [ Tier 1: Fast-Path ]           [ Tier 2: Selective Vision OCR ]
       PyMuPDF on EC2                  Dispatched via Cloudflare Tunnel (HTTPS)
       Latency: 0.1s - 0.3s                      │
       Cost: $0.00                               ▼
                                       ┌──────────────────────────────────┐
                                       │   Kaggle GPU OCR Server          │
                                       │   Model: Qwen2.5-VL-7B-Instruct  │
                                       │   GPU: NVIDIA T4 x 2 / P100      │
                                       │   FastAPI + Cloudflare Tunnel    │
                                       └──────────────────────────────────┘
```

---

## 2. Step-by-Step Setup Procedure for Admin

### Step 2.1: Access Kaggle Notebook
Administrators can access the official Kaggle Notebook directly:
* **Kaggle Notebook URL**: [https://www.kaggle.com/code/lamhuy8904/qwen2-5-vl-ocr-server](https://www.kaggle.com/code/lamhuy8904/qwen2-5-vl-ocr-server)
* Alternatively, upload `src/kaggle/qwen_ocr_server.ipynb` from the repository into your Kaggle workspace.

---

### Step 2.2: Configure GPU Accelerator & Internet Egress
In the Kaggle Notebook interface, locate the right-hand **Session options** panel:
1. **Accelerator**: Select **GPU T4 x 2** (or **GPU P100**).
2. **Language**: Python.
3. **Internet**: Toggle to **Internet on** (Mandatory for downloading packages and establishing the Cloudflare Tunnel).

---

### Step 2.3: Execute Notebook (Run All)
Click **Run All** in the top navigation bar (or press `Ctrl + F9`):
1. **Install Dependencies**: Automatically installs `transformers>=4.49.0`, `accelerate`, `qwen-vl-utils`, `pycloudflared`, `fastapi`, and `uvicorn`.
2. **Load Model Weights**: Mounts `Qwen2.5-VL-7B-Instruct` directly into 16 GB GPU VRAM.
3. **Launch Web Server**: Initializes FastAPI handling Base64 payloads and emitting structured Markdown.
4. **Expose Cloudflare Tunnel**: Activates `pycloudflared` to expose a public HTTPS endpoint.

---

### Step 2.4: Inspect Logs and Copy Tunnel Endpoint
1. Scroll down to the final cell (Cell 5).
2. In the console output log, locate:
   ```text
   ======================================================================
   MAY CHU KAGGLE OCR DA SAN SANG HOAT DONG!
   URL Endpoint: https://random-subdomain.trycloudflare.com/ocr
   URL Health:   https://random-subdomain.trycloudflare.com/health
   ======================================================================
   Sao chep URL Endpoint tren va dan vao trang Admin / Settings!
   ```
3. Copy the URL string:
   ```text
   https://random-subdomain.trycloudflare.com/ocr
   ```

---

### Step 2.5: Ingest Endpoint into Web Studio Admin Panel
1. Navigate to Web Studio:
   ```text
   http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
   ```
2. Sign in with an **Admin** user account.
3. Access **System Admin** (`/admin`) in the top navigation.
4. In the Kaggle GPU guide banner, click **Nhập Endpoint Kaggle** (or **Thêm Khóa API Mới**):
   - **Provider**: Select `Kaggle TPU/GPU (Qwen2.5-VL / Cloudflare Tunnel)`.
   - **Alias**: `Kaggle GPU Qwen2.5-VL Slot 1`.
   - **API Key / Endpoint URL**: Paste the URL copied from Kaggle.
   - **Model Name**: `Qwen2.5-VL-7B`.
   - **Priority**: Set to `1`.
5. Click **Save API Key**.
6. The key transitions to **ACTIVE** status in the Round-Robin Key Tour registry.

---

## 3. Web Studio Operational Workflow

Once configured, end-users process documents with Kaggle GPU acceleration:

1. Open the **Studio** workspace (`/studio`).
2. In the **Model Selector (`#modelChoice`)**:
   - **Auto Hybrid Engine (Recommended)**: Auto-routes digital pages to Fast-Path (0.1s) and scanned pages to Kaggle GPU OCR.
   - **Kaggle TPU/GPU**: Routes all document pages directly to the Kaggle GPU cluster.
3. Upload PDF documents or scanned image bundles.
4. Click **Bắt đầu bóc tách & Dịch thuật**: The EC2 server forwards image payloads across Cloudflare Tunnel to Kaggle.
5. Review results in the Split-view editor and export to Microsoft Word (`.docx`), Markdown (`.md`), or PDF.

---

## 4. Automated Failover Mechanism

The platform maintains resilience via `src/backend/parsers/ocr_dispatcher.py`:
* **Graceful Failover**: If the Kaggle endpoint exceeds the 5-second timeout threshold or disconnects, the system automatically falls back to **Google Gemini Flash** to ensure zero document ingestion failures.
* **Session Renewal**: If the Kaggle kernel times out (typically after 9-12 hours), the Admin simply clicks **Run All** on Kaggle, copies the new URL, and updates the key in `/admin` in under 30 seconds.

---

## 5. Expected Outcomes

Upon completing this module, you have:
- Provisioned a zero-cost Vision OCR inference service powered by Qwen2.5-VL on Kaggle GPU.
- Established an encrypted Cloudflare Tunnel between Kaggle and AWS cloud infrastructure.
- Managed dynamic credentials through the Web Studio Admin interface.
- Adhered strictly to FinOps principles ($0.00 cloud computing expenditure).
