---
title: "Deploy EC2 Application Server"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.7.2. </b> "
---

### Hands-on Objective

Launch an Amazon EC2 instance named `huylam-ocr-web-server` configured with an IAM Instance Profile, establish keyless administrative access via AWS Systems Manager Session Manager (zero open SSH port 22), clone the Web Studio repository from GitHub, and register a systemd Gunicorn daemon for resilient process management.

---

## 1. Creating IAM Role for EC2 (huylam-ssm-role)

Enforcing a Zero Hardcoded Credentials operational model:
1. Navigate to: **IAM Console -> Roles -> Create role**.
2. **Trusted entity type**: Select **AWS service**, Use case: **EC2**.
3. **Permissions policies**: Search and attach three managed policies:
   * **`AmazonSSMManagedInstanceCore`**: Enables Session Manager administration.
   * **`AmazonS3FullAccess`**: Permits direct reading and writing into the S3 storage bucket.
   * **`AmazonDynamoDBFullAccess`**: Permits writing processing audit records to DynamoDB.
4. **Role name**: Enter `huylam-ssm-role`.
5. Click **Create role**.

---

## 2. Launching Amazon EC2 Host (huylam-ocr-web-server)

1. Navigate to: **EC2 Console -> Instances -> Launch an instance**.
2. **Name**: `huylam-ocr-web-server`.
3. **Application and OS Images (Amazon Machine Image)**:
   * Select **Amazon Linux**, AMI: **Amazon Linux 2023 AMI** (Free Tier eligible).
4. **Instance type**: Select **`t2.micro`** (1 vCPU, 1 GiB RAM - Free Tier eligible).
5. **Key pair (login)**: Select **Proceed without a key pair (Not recommended)**.
   * *Architectural Rationale*: All shell sessions are brokered through AWS Systems Manager Session Manager, eliminating local `.pem` key leakage risks.
6. **Network settings**:
   * Click **Edit**.
   * **VPC**: Select **`huylam-vpc`**.
   * **Subnet**: Select **`huylam-subnet-public1-ap-southeast-1a`**.
   * **Auto-assign public IP**: Select **Enable**.
   * **Firewall (security groups)**: Select **Select existing security group** and choose **`huylam-web-sg`** (`sg-0ff9ea20c6c3a8dc8`).
7. **Advanced details**:
   * **IAM instance profile**: Select **`huylam-ssm-role`**.
8. Click **Launch instance**.
9. Record the provisioned Instance ID (e.g., `i-0566e1eedaacea52d`).

---

## 3. Registering Instance into Target Group

1. Navigate to: **EC2 Console -> Target Groups -> huylam-ocr-tg**.
2. Switch to the **Targets** tab and click **Register targets**.
3. Under **Available instances**, check `huylam-ocr-web-server` (`i-0566e1eedaacea52d`).
4. Enter target port: **`5000`**.
5. Click **Include as pending below**, then click **Register pending targets**.

---

## 4. Connecting and Deploying via Systems Manager Session Manager

### Step 4.1: Initiate Session Manager Shell
1. Navigate to **EC2 Console -> Instances**, select `huylam-ocr-web-server`.
2. Click **Connect** in the top navigation bar.
3. Switch to the **Session Manager** tab and click **Connect**.
4. A web-based interactive bash shell launches immediately without requiring SSH port 22 exposure.

---

### Step 4.2: Runtime Provisioning & Repository Ingestion
Run the following shell commands sequentially:

```bash
# Elevate to root privileges
sudo su

# Update packages and install Python 3.11 and Git
dnf update -y
dnf install -y git python3.11 python3.11-pip

# Provision application path and clone repository
mkdir -p /opt/huylam-ocr && cd /opt/huylam-ocr
git clone https://github.com/Lamhuy0489/aws.git .

# Establish Python 3.11 virtual environment
python3.11 -m venv venv
source venv/bin/activate

# Install package dependencies and Gunicorn WSGI server
pip install --upgrade pip
pip install -r requirements.txt gunicorn
```

---

### Step 4.3: Configure systemd Service Daemon (huylam-ocr.service)
Create the systemd configuration file to ensure automatic respawn and boot persistence:

```bash
cat << 'EOF' > /etc/systemd/system/huylam-ocr.service
[Unit]
Description=Huylam OCR Web Studio Platform
After=network.target

[Service]
User=root
WorkingDirectory=/opt/huylam-ocr
Environment="PORT=5000"
Environment="AWS_DEFAULT_REGION=ap-southeast-1"
ExecStart=/opt/huylam-ocr/venv/bin/gunicorn -w 2 -b 0.0.0.0:5000 src.frontend.server:app
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

Reload and activate the service:

```bash
# Reload systemd manager
systemctl daemon-reload

# Enable and start daemon immediately
systemctl enable --now huylam-ocr.service

# Inspect service state
systemctl status huylam-ocr.service
```

**Checkpoint**: Terminal output displays `Active: active (running)`.

---

## 5. Validating Healthy Status in Target Group

1. Return to the AWS Console: **EC2 -> Target Groups -> huylam-ocr-tg**.
2. Select the **Targets** tab.
3. Allow 30 to 60 seconds for health check probes targeting `/login` to succeed.
4. Confirm **Health status** displays in green: **Healthy (1/1)**.

---

## 6. Expected Outcomes

Upon completing this section:
- EC2 host `huylam-ocr-web-server` running Amazon Linux 2023 is fully operational.
- Secure remote shell administration established via Session Manager with zero open SSH ports.
- Web Studio Gunicorn daemon is active on internal port 5000.
- Target Group achieves **Healthy (1/1)** status.