---
title: "Workshop"
date: 2026-09-18
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng Hệ thống Enterprise Agentic RAG trên AWS

#### Tổng quan
Trong workshop này, chúng ta sẽ xây dựng và triển khai một nền tảng **Enterprise Agentic RAG** hoàn chỉnh trên AWS dựa trên kiến trúc Serverless và Cloud-Native.

Hệ thống kết hợp giữa khả năng truy xuất dữ liệu nâng cao (RAG) và trí tuệ nhân tạo tác nhân (AI Agents) có khả năng tự suy luận và thực thi công cụ nghiệp vụ:
- **Tầng Điều phối Agent**: Triển khai trên **AWS Lambda** (Python) với cơ chế ReAct (Reasoning + Acting).
- **Tầng Bảo mật API Key**: Quản lý tập trung trong **AWS Systems Manager Parameter Store (SecureString)**.
- **Tầng Lưu trữ Tri thức & Dữ liệu**: Tài liệu thô lưu trên **Amazon S3**, bộ nhớ phiên hội thoại lưu trên **Amazon DynamoDB** với tính năng tự động dọn dẹp TTL.
- **Tầng Giao tiếp & Phân phối**: Cung cấp API qua **Amazon API Gateway** và giao diện người dùng phân phối toàn cầu qua **Amazon S3 + CloudFront**.
- **Giám sát & Vận hành**: Đo lường độ trễ, số lượng token và log thực thi qua **Amazon CloudWatch**.

#### Nội dung chi tiết các bước

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Điều kiện chuẩn bị](5.2-Prerequisite/)
3. [Lưu trữ tài liệu tri thức với Amazon S3](5.3-Knowledge-Base-S3/)
4. [Quản lý bộ nhớ phiên với Amazon DynamoDB](5.4-DynamoDB-Memory/)
5. [Quản lý khóa bí mật với AWS Systems Manager](5.5-SSM-Secrets/)
6. [Triển khai Agent Controller với AWS Lambda](5.6-Lambda-Agent/)
7. [Cấu hình Cổng giao tiếp Amazon API Gateway](5.7-API-Gateway/)
8. [Phân phối giao diện với CloudFront và S3](5.8-Frontend-CDN/)
9. [Giám sát hệ thống với Amazon CloudWatch](5.9-Monitoring/)
10. [Kiểm thử các kịch bản thực tế](5.10-Testing/)
11. [Dọn dẹp tài nguyên](5.11-Cleanup/)