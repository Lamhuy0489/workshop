---
title: "Kiểm tra phân giải DNS và Truy cập Public URL"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.8.1. </b> "
---

### Mục tiêu thực hành

Kiểm tra phân giải tên miền DNS công khai của Application Load Balancer, thực hiện kiểm thử kết nối hai chiều từ Internet bằng công cụ dòng lệnh (`dig`, `curl`) và xác nhận hoạt động của giao diện Web Studio trên trình duyệt Safari.

---

## 1. Kiểm tra phân giải tên miền DNS của Load Balancer

Application Load Balancer được gán một tên miền chuẩn canonical từ AWS:
```text
huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

### Bước 1.1: Sử dụng công cụ dig để tra cứu DNS
Mở Terminal trên máy trạm và thực thi lệnh tra cứu bản ghi A:

```bash
dig huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com +short
```

**Kết quả kỳ vọng**: Lệnh trả về danh sách các địa chỉ IP công khai đại diện cho các node cân bằng tải tại `ap-southeast-1a` và `ap-southeast-1b`.

---

## 2. Đo kiểm phản hồi HTTP từ dòng lệnh (curl)

### Bước 2.1: Kiểm tra phản hồi tại đường dẫn gốc (/)
Thực hiện lệnh gửi yêu cầu HTTP HEAD đến ALB:

```bash
curl -I http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

**Kết quả ghi nhận thực tế**:
```http
HTTP/1.1 302 FOUND
Date: Wed, 23 Sep 2026 01:41:24 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 199
Connection: keep-alive
Server: gunicorn
Location: /login
Vary: Cookie
```

**Phân tích kỹ thuật**:
- `HTTP/1.1 302 FOUND`: ALB định tuyến thành công vào máy chủ EC2, ứng dụng Flask kiểm tra phiên làm việc chưa xác thực và phát lệnh chuyển hướng an toàn sang cổng đăng nhập.
- `Server: gunicorn`: Chứng minh phản hồi bắt nguồn trực tiếp từ máy chủ WSGI Gunicorn trên máy chủ EC2 `huylam-ocr-web-server`.

---

### Bước 2.2: Kiểm tra phản hồi tại cổng đăng nhập (/login)
Thực hiện kiểm tra đường dẫn `/login`:

```bash
curl -I http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com/login
```

**Kết quả ghi nhận thực tế**:
```http
HTTP/1.1 200 OK
Date: Wed, 23 Sep 2026 01:41:34 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 6483
Connection: keep-alive
Server: gunicorn
Vary: Cookie
```

**Checkpoint**: Mã phản hồi đạt chuẩn `200 OK` với dung lượng HTML đầy đủ.

---

## 3. Kiểm thử truy cập thực tế trên trình duyệt Web

1. Mở trình duyệt web (Safari hoặc Chrome) trên máy tính hoặc thiết bị di động kết nối mạng ngoài.
2. Nhập địa chỉ:
   ```text
   http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
   ```
3. Xác nhận giao diện hiển thị:
   * Thanh tiêu đề thương hiệu: **HYBRID OCR ENTERPRISE**.
   * Cổng đăng nhập doanh nghiệp với biểu mẫu tài khoản và mật khẩu.
   * Nút chuyển đổi giao diện Sáng / Tối và bộ chọn ngôn ngữ (VI / EN).
   * Tốc độ tải trang tức thì dưới 50 ms.

![Giao diện đăng nhập qua Public DNS URL của ALB](/images/week12/09-browser-alb-public-dns-login.png)

4. Đăng nhập vào không gian làm việc chính của Web Studio:
   * Giao diện SPA tải tức thì, tích hợp các tùy chọn tải tài liệu, bộ chọn mô hình AI (Auto Hybrid, Bedrock, Gemini Flash) và màn hình Split-view.

![Giao diện Studio trực tiếp trên ALB](/images/week12/10-browser-alb-studio-live.png)

---

## 4. Hướng dẫn mở rộng tên miền riêng và chứng chỉ HTTPS (Tùy chọn)

Nếu bạn sở hữu tên miền riêng (ví dụ: `ocr.huylam.dev`), bạn có thể dễ dàng thiết lập kết nối mã hóa SSL/TLS:
1. **AWS Certificate Manager (ACM)**: Yêu cầu chứng chỉ công khai miễn phí cho tên miền qua DNS Validation.
2. **Amazon Route 53**: Tạo bản ghi **A Record (Alias)** trỏ tên miền trực tiếp vào Application Load Balancer `huylam-ocr-alb`.
3. **ALB HTTPS Listener**: Thêm Listener trên cổng 443, đính kèm chứng chỉ ACM và chuyển tiếp lưu lượng vào Target Group `huylam-ocr-tg`.

---

## 5. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ có:
- Tên miền công khai của ALB hoạt động ổn định và sẵn sàng phục vụ lưu lượng truy cập từ Internet.
- Luồng chuyển hướng HTTP 302 -> 200 OK hoạt động trơn tru.
- Toàn bộ người dùng có thể truy cập Web Studio trực tiếp mà không cần thiết lập môi trường phức tạp.