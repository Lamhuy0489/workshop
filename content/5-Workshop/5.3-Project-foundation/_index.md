---
title: "Project Foundation"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Module Objective

Understand the modular architecture of the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**, configure environment variables connecting to AWS services, launch the Web Studio application locally, and execute automated test suites.

---

## 1. Modular Source Code Architecture

The `Lamhuy0489/aws` repository is designed according to clean service-oriented principles, segregating core backend processing engines from the frontend Single-Page Application (SPA):

```text
aws/
├── src/
│   ├── backend/
│   │   ├── parsers/               # Multi-tier hybrid extraction engines
│   │   │   ├── fast_parser.py     # Layer 1: Fast-Path via PyMuPDF (0.1s - 0.3s/page)
│   │   │   ├── ocr_dispatcher.py  # Layer 2: Selective OCR (Kaggle GPU / Gemini Failover)
│   │   │   └── hybrid_engine.py   # Unified hybrid orchestration engine
│   │   ├── llm/                   # Translation and key governance
│   │   │   ├── translator.py      # Markdown-preserving technical translation engine
│   │   │   └── key_tour_manager.py# Round-robin API key tour manager
│   │   ├── exporters/             # Multi-format publication suite
│   │   │   ├── docx_exporter.py   # Microsoft Word (.docx) publication
│   │   │   ├── pdf_exporter.py    # Print-ready A4 PDF publication
│   │   │   └── markdown_exporter.py # Raw Markdown (.md) publication
│   │   └── aws/                   # AWS SDK Boto3 integration layer
│   │       ├── storage_service.py # S3, DynamoDB, and SSM client abstraction
│   │       └── lambda_s3_trigger.py # Event-driven AWS Lambda ingestion logic
│   └── frontend/                  # Single-Page Application (SPA) Web Studio
│       ├── server.py              # Flask WSGI application hosting API and web views
│       ├── static/                # Client-side JavaScript and CSS assets
│       │   ├── js/studio.js       # Drag-and-drop file ingestion, split-view layout
│       │   ├── js/spa_router.js   # Zero-flicker single-page router
│       │   └── js/i18n.js         # Multilingual dictionaries (Vietnamese & English)
│       └── templates/             # Web Studio HTML templates
├── tests/                         # Comprehensive pytest test suite
├── Dockerfile                     # Standard OCI Container specification
├── requirements.txt               # Python package dependencies
```

### Hybrid Processing Engine Architecture Blueprint:

![Hybrid Document OCR & Technical Translation Processing Engine Blueprint](/images/architecture/aws-hybrid-ocr-engine-architecture.png?width=100%&classes=border,shadow)

> [!NOTE] Hybrid Engine Diagram File Formats
> * **High-Resolution Render**: `/images/architecture/aws-hybrid-ocr-engine-architecture.png` (Retina 1380x840)
> * **Scalable Vector Graphic**: `/images/architecture/aws-hybrid-ocr-engine-architecture.svg`
> * **Editable Source Diagram**: `/images/architecture/aws-hybrid-ocr-engine-architecture.drawio` (Directly importable into [diagrams.net](https://app.diagrams.net/) with official AWS4 stencils).

---

## 2. Environment Configuration (.env)

Create a `.env` configuration file in the project root from the template:

```bash
cp .env.example .env
```

Populate the required environment parameters corresponding to your AWS infrastructure:

```ini
# Server network configuration
PORT=5000
FLASK_ENV=production
FLASK_SECRET_KEY=huylam-super-secret-key-2026

# AWS Region and cloud resource parameters
AWS_DEFAULT_REGION=ap-southeast-1
AWS_S3_BUCKET_DOCUMENTS=huylam-ocr-documents-ap-southeast-1
DYNAMODB_JOBS_TABLE=document_processing_jobs
SSM_PARAMETER_CONFIG=/huylam-ocr/config

# AI Engine parameters (Local development)
GEMINI_API_KEY=your-gemini-api-key-here
KAGGLE_TUNNEL_ENDPOINT=https://your-kaggle-tunnel.trycloudflare.com
OCR_MODE=AUTO
```

> [!NOTE]
> When executing on Amazon EC2 with IAM Instance Profile (`huylam-ssm-role`), Boto3 acquires temporary STS tokens and loads settings dynamically from SSM Parameter Store without requiring static access keys in `.env`.

---

## 3. Launching Web Studio Locally

Activate your virtual environment and launch the Flask development server:

```bash
# Activate virtual environment
source venv/bin/activate

# Launch with Python
python -m src.frontend.server
```

Alternatively, run with the production Gunicorn WSGI server:

```bash
gunicorn -w 2 -b 0.0.0.0:5000 src.frontend.server:app
```

Open your browser and navigate to:
```text
http://localhost:5000
```

The system automatically redirects visitors to the Web Studio authentication endpoint (`/login`).

---

## 4. Executing Automated Test Suite

Run the automated test suite using `pytest` to verify all parser, translation, exporter, and AWS integration modules:

```bash
pytest -v tests/
```

**Checkpoint**: All test modules (`tests/test_fast_parser.py`, `tests/test_translator.py`, `tests/test_docx_exporter.py`, `tests/test_aws_storage.py`) achieve **100% PASSED**.

---

## 5. Expected Outcomes

Upon completing this module, you have:
- Mastered the modular source code architecture of the OCR and Translation platform.
- Configured local environment variables aligned with AWS cloud resources.
- Successfully executed Web Studio on port 5000 using both Flask and Gunicorn.
- Confirmed system integrity via the comprehensive `pytest` automated suite.