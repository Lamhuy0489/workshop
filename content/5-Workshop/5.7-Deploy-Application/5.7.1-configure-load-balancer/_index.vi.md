---
title: "Cấu hình Load Balancer"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

### Mục tiêu thực hành

Khởi tạo Target Group trên cổng 5000 và thiết lập Application Load Balancer (ALB) công khai Internet (Internet-facing) trải rộng trên 2 Vùng sẵn sàng (Multi-AZ) để phân phối lưu lượng truy cập cho nền tảng Web Studio.

---

## 1. Khởi tạo Target Group (huylam-ocr-tg)

Target Group định nghĩa nhóm các máy chủ đích nhận lưu lượng và cơ chế kiểm tra sức khỏe máy chủ định kỳ.

### Các bước thực hiện trên AWS Console:
1. Đăng nhập vào AWS Console tại khu vực **ap-southeast-1 (Singapore)**.
2. Truy cập: **EC2 -> Target Groups -> Create target group**.
3. **Step 1: Specify group details**:
   * **Target type**: Chọn **Instances**.
   * **Target group name**: `huylam-ocr-tg`.
   * **Protocol**: `HTTP`, **Port**: `5000`.
   * **IP address type**: `IPv4`.
   * **VPC**: Chọn đúng **`huylam-vpc`** (Rất quan trọng: Nếu để mặc định là Default VPC, danh sách máy chủ sẽ không hiển thị).
   * **Protocol version**: `HTTP1`.
4. **Health checks**:
   * **Health check protocol**: `HTTP`.
   * **Health check path**: Nhập **`/login`** (Lý do kỹ thuật: Ứng dụng Web Studio chuyển hướng 302 từ trang chủ `/` sang `/login` với mã phản hồi 200 OK. Cấu hình đường dẫn `/login` giúp Target Group đánh giá máy chủ đạt trạng thái Healthy ngay lập tức).
   * Mở rộng mục **Advanced health check settings**:
     * **Healthy threshold**: `2`.
     * **Unhealthy threshold**: `2`.
     * **Timeout**: `5 seconds`.
     * **Interval**: `30 seconds`.
     * **Success codes**: `200` (hoặc `200,302`).
5. Nhấp nút **Next**.
6. Tại **Step 2: Register targets**, tạm thời bỏ qua (chúng ta sẽ đăng ký máy chủ EC2 ở chuyên đề tiếp theo).
7. Nhấp nút **Create target group**.

---

## 2. Khởi tạo Application Load Balancer (huylam-ocr-alb)

Application Load Balancer đóng vai trò là tầng tiếp nhận lưu lượng tập trung (Layer 7 Ingress) từ Internet:

### Các bước thực hiện:
1. Truy cập **EC2 Console -> Load Balancers -> Create load balancer**.
2. Tại mục **Application Load Balancer**, nhấp **Create**.
3. **Basic configuration**:
   * **Load balancer name**: `huylam-ocr-alb`.
   * **Scheme**: **Internet-facing** (Công khai Internet).
   * **IP address type**: **IPv4**.
4. **Network mapping**:
   * **VPC**: Chọn **`huylam-vpc`**.
   * **Mappings (Tối thiểu 2 Availability Zones)**:
     * Đánh dấu chọn **`ap-southeast-1a`**, chọn Subnet: `huylam-subnet-public1-ap-southeast-1a`.
     * Đánh dấu chọn **`ap-southeast-1b`**, chọn Subnet: `huylam-subnet-public2-ap-southeast-1b`.
5. **Security groups**:
   * Xóa Security Group mặc định (`default`).
   * Chọn Security Group đã tạo riêng cho ALB: **`huylam-alb-sg`** (`sg-0dca819306a96bfdb`).
6. **Listeners and routing**:
   * **Protocol**: `HTTP`, **Port**: `80`.
   * **Default action**: Chọn **Forward to** và chọn Target Group **`huylam-ocr-tg`**.
7. Kiểm tra lại toàn bộ thông số và nhấp **Create load balancer**.

---

## 3. Kiểm tra trạng thái Load Balancer

1. Trong danh sách Load Balancers, chọn `huylam-ocr-alb`.
2. Đợi khoảng 1 - 2 phút cho đến khi **Status** chuyển từ `Provisioning` sang **Active**.
3. Tại phần **Details**, ghi lại tên miền DNS công khai của Load Balancer:

```text
DNS name: huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

Đường link này sẽ được người dùng trên toàn thế giới sử dụng để truy cập trực tiếp vào ứng dụng Web Studio qua cổng chuẩn 80.

---

## 4. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Target Group `huylam-ocr-tg` được tạo trên cổng 5000 với đường dẫn kiểm tra sức khỏe `/login`.
- Application Load Balancer `huylam-ocr-alb` ở trạng thái **Active** trên 2 Vùng sẵn sàng.
- Listener HTTP:80 chuyển tiếp lưu lượng vào Target Group.
- Tên miền DNS công khai sẵn sàng điều phối lưu lượng.