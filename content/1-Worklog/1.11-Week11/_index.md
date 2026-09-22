---
title: "Week 11 Worklog"
date: 2026-09-22
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:
* Conduct end-to-end performance benchmarking and comprehensive integration validation on the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
  * **Layer 1 Fast-Path Extraction Latency**: Benchmark native digital PDF text stream parsing via PyMuPDF, validating ultra-low latency goals (0.1s - 0.3s/page) at strictly $0.00 cost.
  * **Independent AWS Native Model Option**: Integrate the **AWS Native Model (Amazon Bedrock / Nova)** into the Web Studio model selector dropdown as a standalone selectable option (non-parallel) to prevent redundant cloud token expenditure and compute overhead.
  * **Technical Translation Quality**: Validate Markdown-preserving document translation into Vietnamese, enforcing 100% fidelity on headers, nested lists, and technical data tables.
  * **Multi-Format Publication**: Benchmark export capabilities to Microsoft Word (`.docx`) and print-ready PDF formats, verifying real-world rendering compatibility on native macOS Microsoft Word.
  * **Automated Cloud Synchronization**: Validate automated data streaming to Amazon S3 (`uploads/` and `outputs/`) and metadata logging in the Amazon DynamoDB `document_processing_jobs` table.

---

### Tasks carried out this week:

| Day | Task | Achievement | Reference Material |
| :--- | :--- | :--- | :--- |
| **Monday** | - Design and establish multi-tiered document benchmarking suite.<br>- Execute parsing experiments across real-world digital documents (`cv.pdf`, `28_Bai_Bao_Hoi nghi KH_Khoa_CNTT_2026.pdf`, official circulars).<br>- Measure Layer 1 Fast-Path latency across single and multi-page documents. | `cv.pdf` parsed in 0.31s; 11-page scientific paper parsed in 3.07s (~0.28s/page), achieving 100% of performance targets. | [PyMuPDF High-Performance Text Extraction](https://pymupdf.readthedocs.io/en/latest/) |
| **Tuesday** | - Enhance Web Studio user interface: Add standalone **AWS Native Model (Amazon Bedrock / Nova)** entry to `#modelChoice` select menu.<br>- Update multilingual dictionaries `i18n.js` for Vietnamese and English locales.<br>- Update system configuration schema `AppSettings.ocr_mode` to support `"AWS_NATIVE"`. | Web Studio supports standalone AWS Native model execution without forced parallel runs, giving users complete operational control. | [AWS Bedrock Model Access](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html) |
| **Wednesday** | - Upgrade Boto3 SDK layer in `AWSStorageService`: add `image_bytes` and `image_format` arguments to `invoke_bedrock_converse` for Multimodal Converse API invocation.<br>- Implement graceful failover mechanism in `OCRDispatcher`: automatically route to Gemini Flash if AWS Bedrock policy restrictions occur. | System equipped for Amazon Bedrock Converse Vision OCR with seamless fallback resilience. | [Amazon Bedrock Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| **Thursday** | - Conduct technical translation quality tests directly on Web Studio.<br>- Translate full content of `cv.pdf` into technical Vietnamese.<br>- Verify integrity of Markdown formatting, profile summary, technical competencies, and project history. | Translated document reached 3,480 characters with 100% preservation of Markdown headers, bullet points, and semantics. | [Gemini Technical Translation Prompting](https://ai.google.dev/gemini-api/docs/prompting-strategies) |
| **Friday** | - Test multi-format publication pipeline from Web Studio: export Microsoft Word document `cv.pdf.docx` (38.2 KB).<br>- Open generated `.docx` artifact directly inside Microsoft Word on macOS.<br>- Inspect typography, line breaks, header hierarchy, and styling consistency. | Exported DOCX artifact renders cleanly with zero formatting anomalies on macOS Word. | [python-docx Documentation](https://python-docx.readthedocs.io/en/latest/) |
| **Saturday** | - Validate automated cloud synchronization via AWS Management Console and AWS CLI:<br>  * Verify generated artifact `outputs/1f8d7fe1/cv.pdf.md` (3.0 KB) in S3 bucket `huylam-ocr-documents-ap-southeast-1`.<br>  * Verify real-time progress records in DynamoDB table `document_processing_jobs` (`job_id`, `model_used = aws-bedrock`, `processing_time = 0.31s`). | End-to-end cloud persistence pipeline operates 100% autonomously with real-time auditability. | [Amazon S3 Developer Guide](https://docs.aws.amazon.com/s3/) |
| **Sunday** | - Extract and annotate 7 authentic proof screenshots with red bounding boxes surrounding Account Badge `huylam (677994024390)`.<br>- Author bilingual Week 11 worklog documentation and compile with Hugo site generator.<br>- Synchronize application codebase and documentation to GitHub repositories (`aws` and `workshop`). | 100% completion of Week 11 benchmarking and cloud integration milestones. | [AWS Free Tier Dashboard](https://aws.amazon.com/free/) |

---

### Benchmark Empirical Metrics:

| Benchmark Criterion | Test Artifact | Processing Engine | Latency | Estimated FinOps Cost | Evaluation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Digital Text Extraction (Fast-Path)** | `cv.pdf` (1 page) | Fast-Path Native (PyMuPDF) | **0.31s** | $0.00 | Outstanding, instantaneous |
| **Multi-page Digital Text Extraction** | `28_Bai_Bao_...pdf` (11 pages) | Fast-Path Native (PyMuPDF) | **3.07s** (~0.28s/page) | $0.00 | Outstanding, structure preserved |
| **Scanned Image Extraction (Vision OCR)** | `Screen Shot ... .png` (1 image) | Google Gemini Flash Lite | **2.54s** | $0.00 (Free Tier) | Full Vietnamese diacritics captured |
| **AWS Native Option Extraction** | `Screen Shot ... .png` (1 image) | AWS Bedrock (Amazon Nova) | **4.61s - 6.99s** | Pay-as-you-go | Precise tabular data extraction |
| **Markdown-Preserving Translation** | `cv.pdf` (English -> Vietnamese) | Gemini Flash Translator | **2.80s** | $0.00 (Free Tier) | 100% Markdown layout fidelity |
| **Microsoft Word (.docx) Export** | `cv.pdf` -> `cv.pdf.docx` | DocxExporter Module | **0.15s** | $0.00 | 38.2 KB file opens cleanly in Word |

---

### Empirical Proofs with Red Bounding Boxes (AWS Console & Web Studio):

> [!IMPORTANT]
> All proof screenshots feature prominent red bounding boxes highlighting the **AWS Account Badge `huylam (677994024390)`**, service identifiers, S3 bucket keys, and core Studio controls.

#### 1. Web Studio Interface with Automatic (Fast-Path Native) Mode:
Intuitive document extraction interface featuring model selector dropdown, drag-and-drop ingestion, and Layer 1 Fast-Path toggle:
![Web Studio Automatic Mode Interface](/images/week11/01-studio-model-selection-auto.png)

---

#### 2. Web Studio Interface with Standalone AWS Native Model (Amazon Bedrock / Nova):
Integrated AWS Native model selection operating independently without forced parallel overhead, minimizing cloud token costs:
![AWS Native Model Selection in Studio](/images/week11/02-studio-model-selection-aws-bedrock.png)

---

#### 3. Extraction and Translation Results for cv.pdf on Web Studio:
Extracted and translated document text displayed side-by-side with Word (`.docx`) and PDF export actions:
![Studio CV Extraction and Translation Result](/images/week11/03-studio-cv-extracted-translated.png)

---

#### 4. Microsoft Word macOS Verification for Exported cv.pdf.docx:
Generated Word artifact downloaded and verified inside macOS Microsoft Word, preserving typography, headers, and document structure:
![Word CV Exported Verification](/images/week11/04-word-cv-exported-verification.png)

---

#### 5. uploads/ Ingestion Directory in Amazon S3 Bucket huylam-ocr-documents-ap-southeast-1:
Verification of raw input documents securely ingested into `uploads/` prefix, featuring Account Badge `huylam (677994024390)`:
![S3 uploads Directory](/images/week11/05-s3-bucket-uploads-folder.png)

---

#### 6. outputs/ Artifacts Directory in Amazon S3 Bucket huylam-ocr-documents-ap-southeast-1:
Verification of automated job artifact directories created in `outputs/` prefix per document processing job ID:
![S3 outputs Directory](/images/week11/06-s3-bucket-outputs-folder.png)

---

#### 7. cv.pdf.md Output Artifact in outputs/1f8d7fe1/ on Amazon S3:
Detailed view of generated Markdown file `cv.pdf.md` (3.0 KB) created at 19:38:21 (UTC+07:00), stored in private S3 bucket:
![S3 Output Markdown File Detail](/images/week11/07-s3-output-cv-markdown-file.png)

---

### Week 11 Summary:
* Successfully achieved 100% of performance benchmarking objectives across Fast-Path digital parsing, Vision OCR, and technical translation.
* Integrated standalone **AWS Native Model (Amazon Bedrock / Nova)** option into Web Studio, adhering to enterprise cloud security and FinOps discipline.
* Validated artifact rendering fidelity with native Microsoft Word on macOS.
* Verified autonomous asynchronous cloud synchronization across Amazon S3 and Amazon DynamoDB.