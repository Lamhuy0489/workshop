---
title: "Build Docker Image"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

### Hands-on Objective

Author an optimized multi-tier `Dockerfile` on `python:3.11-slim`, build the container image for the **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**, and validate container execution locally on port 5000.

---

## 1. Web Studio Dockerfile Specification

In the project root directory, `Dockerfile` is structured to leverage build layer caching and minimize artifact footprint:

```dockerfile
# Use lightweight Python 3.11 base image
FROM python:3.11-slim

# Set system environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PORT=5000 \
    PYTHONPATH=/app

# Establish container working directory
WORKDIR /app

# Install system compilation packages for PDF and image processing
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    libjpeg-dev \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency definition to leverage layer caching
COPY requirements.txt /app/requirements.txt

# Install Python package dependencies
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r /app/requirements.txt

# Copy application source tree
COPY src/ /app/src/

# Initialize local data directories
RUN mkdir -p /app/data

# Expose internal service port
EXPOSE 5000

# Start Web Studio entry point
CMD ["python3", "src/frontend/server.py"]
```

---

## 2. Configuring .dockerignore

Create `.dockerignore` in the project root to exclude local build artifacts and sensitive assets from the image:

```text
__pycache__
*.pyc
*.pyo
*.pyd
.Python
env/
venv/
.git
.gitignore
.env
.pytest_cache/
tests/
raw/
workshop/
*.md
```

---

## 3. Building the Docker Image

Run the build command from the root directory:

```bash
docker build -t huylam-ocr-web-studio:latest .
```

During build execution:
1. Docker pulls `python:3.11-slim`.
2. Installs OS build tools for compiling `PyMuPDF` and `Pillow`.
3. Installs Python dependencies from `requirements.txt`.
4. Copies the `src/` tree and tags `huylam-ocr-web-studio:latest`.

Verify image existence in your local Docker engine:

```bash
docker images | grep huylam-ocr-web-studio
```

---

## 4. Local Container Runtime Verification

Instantiate and test the container:

```bash
docker run -d --name huylam-ocr-app -p 5000:5000 huylam-ocr-web-studio:latest
```

Check container status:

```bash
docker ps
```

Open your browser and navigate to:
```text
http://localhost:5000
```

Verify that the authentication portal renders cleanly. Stop the test container when verification completes:

```bash
docker stop huylam-ocr-app && docker rm huylam-ocr-app
```

---

## 5. Expected Outcomes

Upon completing this section:
- Production `Dockerfile` and `.dockerignore` created for Python 3.11.
- Docker image `huylam-ocr-web-studio:latest` built successfully.
- Container runtime verified locally on port 5000.