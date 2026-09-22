---
title: "Đẩy Image lên Amazon ECR"
date: 2026-09-23
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

### Mục tiêu thực hành

Khởi tạo kho lưu trữ riêng tư **Amazon Elastic Container Registry (Amazon ECR)** mang tên `huylam-web-app` tại khu vực `ap-southeast-1`, thực hiện xác thực bảo mật Docker CLI thông qua AWS STS Token và đẩy container image lên đám mây AWS.

---

## 1. Khởi tạo Amazon ECR Private Repository

Amazon ECR là dịch vụ lưu trữ container image được quản lý hoàn toàn bởi AWS, tích hợp sẵn tính năng kiểm tra lỗ hổng bảo mật và phân quyền truy cập thông qua AWS IAM.

### Các bước thực hiện trên AWS Console:
1. Đăng nhập vào AWS Console, chuyển sang khu vực **ap-southeast-1 (Singapore)**.
2. Tìm kiếm dịch vụ: **Elastic Container Registry -> Repositories -> Create repository**.
3. Cấu hình thông số:

| Thuộc tính (Attribute) | Giá trị cấu hình (Configured Value) | Ý nghĩa kỹ thuật |
| :--- | :--- | :--- |
| **Visibility settings** | **Private** | Chỉ cho phép các thực thể IAM được cấp quyền truy cập |
| **Repository name** | `huylam-web-app` | Tên kho lưu trữ image của nền tảng Web Studio |
| **Tag immutability** | Disabled | Cho phép ghi đè thẻ `latest` trong các lần cập nhật |
| **Scan on push** | Enabled | Tự động quét lỗ hổng bảo mật (CVE) khi đẩy image |
| **KMS encryption** | AES-256 | Mã hóa dữ liệu image ở trạng thái nghỉ |

4. Nhấp nút **Create repository**.

![Danh sách kho lưu trữ Amazon ECR](/images/week8/01-ecr-repositories-list-initial.png)

![Cấu hình tạo kho lưu trữ riêng tư huylam-web-app trên Amazon ECR](/images/week8/02-ecr-create-repository.png)

---

## 2. Xác thực Docker CLI với Amazon ECR

Để đẩy image từ máy trạm lên Amazon ECR, Docker client cần được xác thực thông qua mã token tạm thời do AWS STS cấp:

```bash
# Lấy token xác thực và đăng nhập Docker vào ECR
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 677994024390.dkr.ecr.ap-southeast-1.amazonaws.com
```

**Kết quả kỳ vọng**:
```text
Login Succeeded
```

---

## 3. Gắn thẻ và Đẩy Image lên Amazon ECR

### Bước 3.1: Gắn thẻ Image (docker tag)
Gắn địa chỉ URI của kho lưu trữ ECR vào image cục bộ vừa build:

```bash
docker tag huylam-ocr-web-studio:latest 677994024390.dkr.ecr.ap-southeast-1.amazonaws.com/huylam-web-app:latest
```

### Bước 3.2: Đẩy Image lên ECR (docker push)
Thực thi lệnh đẩy toàn bộ các layer của image lên đám mây:

```bash
docker push 677994024390.dkr.ecr.ap-southeast-1.amazonaws.com/huylam-web-app:latest
```

Docker sẽ lần lượt tải các lớp nén lên máy chủ Amazon ECR. Nhờ sử dụng base image `python:3.11-slim` và tối ưu `.dockerignore`, quá trình tải diễn ra nhanh chóng và tiết kiệm băng thông.

---

## 4. Kiểm tra trên AWS Console

1. Truy cập **Amazon ECR -> Repositories -> huylam-web-app**.
2. Xác nhận thẻ **`latest`** xuất hiện trong danh sách kèm định danh Image URI và kích thước nén.
3. Kiểm tra kết quả quét bảo mật **Vulnerabilities**: Ghi nhận không có lỗ hổng nghiêm trọng (0 Critical).

![Chi tiết kho lưu trữ Amazon ECR huylam-web-app](/images/week8/03-ecr-repository-details-empty.png)

---

## 5. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Kho lưu trữ Amazon ECR `huylam-web-app` được tạo an toàn ở chế độ Private.
- Docker CLI xác thực thành công với registry của AWS.
- Container Image được đẩy thành công lên Amazon ECR, sẵn sàng phục vụ triển khai trên Amazon ECS hoặc máy chủ Amazon EC2.