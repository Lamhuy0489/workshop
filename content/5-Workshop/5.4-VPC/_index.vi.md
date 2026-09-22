---
title: "Hạ tầng mạng"
date: 2026-09-23
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Mục tiêu chuyên đề

Thiết kế và triển khai hạ tầng mạng ảo Virtual Private Cloud (Amazon VPC) chuẩn doanh nghiệp, hỗ trợ kiến trúc sẵn sàng cao đa vùng độc lập (Multi-AZ) và chuỗi tường lửa phân tầng Security Group Chaining để bảo vệ máy chủ ứng dụng Web Studio.

---

## 1. Tổng quan kiến trúc mạng

Hạ tầng mạng là nền móng cốt lõi đảm bảo khả năng mở rộng, tính sẵn sàng cao và an toàn bảo mật cho nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:

- **Mạng ảo VPC (`huylam-vpc`)**: Dải mạng CIDR `10.0.0.0/16` cung cấp không gian địa chỉ riêng biệt hoàn toàn cô lập trên đám mây AWS.
- **Phân bổ đa vùng sẵn sàng (Multi-AZ)**:
  - Subnet công khai 1: `huylam-subnet-public1-ap-southeast-1a` (`10.0.8.0/21`) tại `ap-southeast-1a`.
  - Subnet công khai 2: `huylam-subnet-public2-ap-southeast-1b` (`10.0.16.0/21`) tại `ap-southeast-1b`.
- **Cổng Internet (Internet Gateway `huylam-igw`)**: Cho phép các tài nguyên trong VPC kết nối hai chiều với Internet.
- **Bảng định tuyến (`huylam-rtb-public`)**: Chuyển tiếp toàn bộ lưu lượng ngoại vi `0.0.0.0/0` qua Internet Gateway.
- **Chuỗi Security Group Chaining**:
  - `huylam-alb-sg`: Tiếp nhận lưu lượng HTTP cổng 80 từ toàn cầu.
  - `huylam-web-sg`: Chỉ chấp nhận kết nối TCP cổng 5000 bắt nguồn từ chính Security Group của ALB (`huylam-alb-sg`), ngăn chặn triệt để nguy cơ quét cổng hoặc tấn công trực tiếp vào máy chủ EC2 từ Internet.

---

## 2. Nội dung các bước thực hành

Chuyên đề này gồm 2 phần thực hành chi tiết:

- **[5.4.1 Khởi tạo Amazon VPC Multi-AZ](5.4.1-create-vpc/)**: Hướng dẫn tạo VPC `huylam-vpc` và phân chia các Subnets trên nhiều Vùng sẵn sàng.
- **[5.4.2 Cấu hình định tuyến & Chuỗi Security Groups](5.4.2-configure-network/)**: Đính kèm Internet Gateway, cấu hình Route Table và thiết lập chuỗi quy tắc Inbound/Outbound phân tầng cho ALB và Web Server.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn sẽ có:
- Một Amazon VPC `huylam-vpc` hoạt động trên dải mạng `10.0.0.0/16`.
- 2 Public Subnet Multi-AZ phủ rộng qua `ap-southeast-1a` và `ap-southeast-1b`.
- Internet Gateway `huylam-igw` được liên kết và định tuyến công khai.
- Bộ đôi Security Groups `huylam-alb-sg` và `huylam-web-sg` tạo thành hàng rào an ninh phân tầng vững chắc.