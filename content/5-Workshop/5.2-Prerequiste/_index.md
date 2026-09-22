---
title: "Prerequisites"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Module Objective

Establish and verify the local development environment and AWS Management Console access, ensuring all required runtimes, developer tooling, SDKs, and source repositories for the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform** are prepared prior to cloud infrastructure provisioning.

---

## 1. AWS Account & IAM Authorization Requirements

1. **Active AWS Account**:
   * Operational AWS account (e.g., `huylam`, Account ID: `677994024390`).
   * Primary deployment region: **`ap-southeast-1` (Asia Pacific - Singapore)**.
   * Active AWS Budgets notifications to enforce a strict $0.00 spend threshold.
2. **IAM Principal**:
   * Administered IAM user (`dev_admin`) possessing administrative policies to provision VPC, EC2, Application Load Balancers, S3, DynamoDB, Systems Manager, Lambda, and CloudWatch.
   * Avoid using root account credentials for daily deployment procedures according to AWS Well-Architected Framework security principles.

![Verified AWS Account huylam in Singapore Region](/images/week1/01-account-huylam.png)

![AWS Budgets Zero Spend Budget Configuration](/images/week1/03-aws-budgets.png)

---

## 2. Local Workstation Development Tooling

Verify that your local workstation has the following developer tools installed:

| Tool | Recommended Version | Architectural Purpose |
| :--- | :--- | :--- |
| **Python** | Python 3.11+ | Primary runtime environment for backend APIs, parsing, and translation engines |
| **pip & venv** | Bundled with Python 3.11 | Package dependency management and isolated virtual environments |
| **Git** | 2.40+ | Version control and repository synchronization with GitHub |
| **Docker Desktop** | 24.0+ | OCI Container packaging for the Web Studio application |
| **AWS CLI v2** | 2.15+ | Command-line validation and automated resource inspection |
| **Web Browser** | Safari, Chrome, Firefox | AWS Management Console operation and Web Studio validation |

---

## 3. Environment Verification Steps

### Step 3.1: Verify Local Tooling Versions
Open your terminal and run the verification commands:

```bash
# Verify Python and Pip
python3 --version
pip3 --version

# Verify Git
git --version

# Verify Docker
docker --version

# Verify AWS CLI
aws --version
```

**Checkpoint**: All commands output valid version numbers without errors.

---

### Step 3.2: Verify AWS CLI Identity & Credentials
Confirm that AWS CLI credentials authenticate successfully into `ap-southeast-1`:

```bash
# Verify caller identity
aws sts get-caller-identity
```

**Expected Output**:
```json
{
    "UserId": "AIDA...DEVADMIN",
    "Account": "677994024390",
    "Arn": "arn:aws:iam::677994024390:user/dev_admin"
}
```

![AWS CLI STS get-caller-identity verification](/images/week1/04-aws-cli-verified.png)

---

### Step 3.3: Clone Project Repository & Install Dependencies
Clone the official GitHub repository and set up your virtual environment:

```bash
# Clone repository
git clone https://github.com/Lamhuy0489/aws.git
cd aws

# Initialize Python 3.11 virtual environment
python3.11 -m venv venv
source venv/bin/activate

# Install requirements
pip install --upgrade pip
pip install -r requirements.txt
```

---

## 4. Expected Outcomes

Upon completing this prerequisite module, you have:
- Verified AWS Console access in the `ap-southeast-1` region.
- Configured local AWS CLI authentication for `dev_admin` on Account `677994024390`.
- Prepared Python 3.11 runtime and Docker Desktop virtualization environments.
- Cloned the `Lamhuy0489/aws` repository and installed all required software dependencies.