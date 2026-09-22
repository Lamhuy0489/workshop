---
title: "Tên miền và URL công khai"
date: 2026-09-23
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

### Mục tiêu chuyên đề

Kiểm tra phân giải tên miền DNS công khai của Application Load Balancer (ALB), xác thực quy trình định tuyến lưu lượng truy cập từ Internet vào nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**, và tìm hiểu cơ chế mở rộng tên miền tùy biến với chứng chỉ HTTPS.

---

## 1. Tổng quan phân phối lưu lượng Internet

Khi Application Load Balancer được khởi tạo ở chế độ công khai (Internet-facing), AWS tự động cấp phát một bản ghi DNS chuẩn canonical (A Record phân tán) có dạng:

```text
huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
```

Ưu điểm của cơ chế phân giải tên miền ALB:
- **Tự động cân bằng tải và co giãn IP**: ALB tự động luân chuyển lưu lượng giữa các địa chỉ IP công khai của các Vùng sẵn sàng (`ap-southeast-1a` và `ap-southeast-1b`) để đảm bảo không bị quá tải.
- **Tính sẵn sàng cao (High Availability)**: Nếu một Trung tâm dữ liệu gặp sự cố, hệ thống DNS của AWS sẽ tự động loại bỏ địa chỉ IP đó khỏi danh sách phân giải chỉ trong vài giây.
- **Tương thích toàn cầu**: Mọi người dùng từ bất kỳ mạng Internet nào (mạng gia đình, 4G/5G, mạng doanh nghiệp) đều có thể kết nối trực tiếp đến Web Studio mà không cần cấu hình VPN.

---

## 2. Nội dung các bước thực hành

Chuyên đề này gồm phần thực hành chi tiết:

- **[5.8.1 Kiểm tra phân giải DNS & Truy cập qua Public URL](5.8.1-configure-public-dns/)**: Sử dụng các công cụ dòng lệnh (`dig`, `nslookup`, `curl`) và trình duyệt web để đo kiểm tính sẵn sàng của đường link thật.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chuyên đề này, bạn sẽ có:
- Tên miền công khai của ALB hoạt động ổn định và phân giải chính xác các địa chỉ IP Multi-AZ.
- Xác thực thành công chu trình tiếp nhận yêu cầu HTTP 80 -> chuyển tiếp port 5000 -> chuyển hướng 302 sang `/login` với mã phản hồi 200 OK.
- Toàn bộ người dùng bên ngoài Internet có thể trải nghiệm trực tiếp hệ thống bóc tách và dịch thuật tài liệu.