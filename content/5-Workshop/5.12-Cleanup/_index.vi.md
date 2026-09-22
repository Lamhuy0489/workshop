---
title: "Dọn dẹp tài nguyên & Quản trị FinOps"
date: 2026-09-23
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

### Mục tiêu thực hành

Hướng dẫn quy trình giải phóng tài nguyên điện toán đám mây một cách khoa học, có trật tự và an toàn sau khi hoàn thành nghiệm thu đồ án, giúp bảo toàn ngân sách và đưa chi phí duy trì hàng tháng về mức 0.00 USD theo các nguyên lý quản trị FinOps.

> **LƯU Ý QUAN TRỌNG:**
> Toàn bộ các bước dọn dẹp dưới đây chỉ thực hiện sau khi hoàn tất việc đánh giá, chấm điểm và báo cáo nghiệm thu đồ án. Nếu hệ thống đang trong giai đoạn trình diễn (Demo) hoặc chấm điểm trực tiếp, vui lòng duy trì trạng thái LIVE của các dịch vụ để ban giám khảo có thể truy cập qua Application Load Balancer.

---

## 1. Trật tự giải phóng tài nguyên khuyến nghị

Để tránh lỗi phụ thuộc chéo (Dependency Violation), các tài nguyên cần được xóa theo trình tự từ tầng ngoài cùng vào tầng lõi:

1. **Application Load Balancer & Target Group** (Tầng phân phối lưu lượng - Cần giải phóng trước tiên vì ALB tính phí theo giờ chạy).
2. **Amazon EC2 Instance** (Tầng tính toán ứng dụng).
3. **Amazon S3 Bucket & Objects** (Tầng lưu trữ tài liệu).
4. **Amazon DynamoDB Table** (Tầng cơ sở dữ liệu NoSQL).
5. **AWS Lambda Function** (Tầng xử lý sự kiện Serverless).
6. **AWS Systems Manager Parameter Store** (Tầng quản lý cấu hình bí mật).
7. **Amazon CloudWatch Alarms & Log Groups** (Tầng giám sát).
8. **IAM Roles & Instance Profiles** (Tầng phân quyền bảo mật).
9. **Security Groups & VPC** (Tầng mạng cơ sở).

---

## 2. Các bước thực hiện chi tiết

### Bước 2.1: Xóa Application Load Balancer và Target Group

Application Load Balancer có chi phí cố định khoảng 0.0225 USD/giờ (~16 USD/tháng). Đây là dịch vụ đầu tiên cần giải phóng sau khi kết thúc nghiệm thu:

1. Truy cập **EC2 Console -> Load Balancing -> Load Balancers**.
2. Chọn `huylam-ocr-alb`, nhấp **Actions -> Delete load balancer**.
3. Nhập xác nhận xóa và chọn **Delete**.
4. Chuyển sang **Target Groups**, chọn `huylam-ocr-tg`, nhấp **Actions -> Delete**.

---

### Bước 2.2: Dừng hoặc Hủy máy chủ Amazon EC2

1. Truy cập **EC2 Console -> Instances**.
2. Chọn máy chủ `huylam-ocr-ec2` (`i-0566e1eedaacea52d`).
3. Nhấp **Instance state**:
   - Nếu muốn tạm dừng để tái sử dụng sau: Chọn **Stop instance**.
   - Nếu muốn xóa bỏ vĩnh viễn: Chọn **Terminate instance**.
4. Xác nhận hành động. Ổ đĩa EBS gắn kèm sẽ tự động được thu hồi.

---

### Bước 2.3: Làm rỗng và Xóa Amazon S3 Bucket

1. Truy cập **Amazon S3 -> Buckets**.
2. Chọn bucket **`huylam-ocr-documents-ap-southeast-1`**.
3. Nhấp nút **Empty** để xóa toàn bộ tài liệu trong thư mục `uploads/` và `outputs/`.
4. Nhập chuỗi `permanently delete` để xác nhận.
5. Sau khi bucket rỗng, nhấp nút **Delete**, nhập lại tên bucket và nhấn **Delete bucket**.

---

### Bước 2.4: Xóa Amazon DynamoDB Table

1. Truy cập **Amazon DynamoDB -> Tables**.
2. Chọn bảng **`document_processing_jobs`**.
3. Nhấp nút **Delete table**.
4. Bỏ chọn tạo bản sao lưu CloudWatch nếu không cần thiết, nhập `confirm` và nhấp **Delete**.

---

### Bước 2.5: Xóa hàm AWS Lambda

1. Truy cập **AWS Lambda -> Functions**.
2. Chọn hàm **`huylam-ocr-processor`**.
3. Nhấp **Actions -> Delete**.
4. Nhập xác nhận và nhấn **Delete**.

---

### Bước 2.6: Xóa cấu hình SSM Parameter Store

1. Truy cập **AWS Systems Manager -> Parameter Store**.
2. Chọn tham số **`/huylam-ocr/config`**.
3. Nhấp **Delete** và xác nhận thao tác.

---

### Bước 2.7: Xóa CloudWatch Alarm và Log Groups

1. Truy cập **Amazon CloudWatch -> Alarms**:
   - Chọn `huylam-ocr-ec2-high-cpu`, nhấp **Actions -> Delete**.
2. Chuyển sang mục **Log groups**:
   - Chọn `/aws/lambda/huylam-ocr-processor`, nhấp **Actions -> Delete log group(s)**.

---

### Bước 2.8: Xóa IAM Roles và Security Groups

1. Truy cập **IAM Console -> Roles**:
   - Xóa `huylam-ocr-ec2-role` và `huylam-ocr-lambda-role`.
2. Truy cập **VPC Console -> Security Groups**:
   - Xóa `huylam-web-sg` trước, sau đó xóa `huylam-alb-sg`.

---

### Bước 2.9: Xóa Virtual Private Cloud (huylam-vpc)

1. Truy cập **VPC Console -> Your VPCs**.
2. Chọn **`huylam-vpc`**.
3. Nhấp **Actions -> Delete VPC**.
4. Bảng điều khiển sẽ tự động hiển thị các thành phần liên kết (Subnets, Internet Gateway, Route Tables) sẽ được giải phóng đồng thời.
5. Nhập `delete` để hoàn tất thu hồi toàn bộ hạ tầng mạng.

---

## 3. Đo kiểm FinOps và Xác nhận Chi phí 0.00 USD

Sau khi hoàn tất thu hồi tài nguyên:
1. Truy cập **AWS Billing and Cost Management -> Cost Explorer**:
   - Kiểm tra mức tiêu thụ hàng ngày (Daily Spend) để đảm bảo không còn đường cong chi phí phát sinh.
2. Kiểm tra **AWS Budgets**:
   - Xác nhận ngân sách cảnh báo 10.00 USD duy trì mức sử dụng thực tế là 0.00 USD.

---

## 4. Kết quả mong đợi

Sau khi hoàn thành quy trình này:
- Toàn bộ tài nguyên phục vụ thực hành và kiểm thử được giải phóng sạch sẽ.
- Tránh hoàn toàn việc phát sinh hóa đơn ngoài ý muốn từ các dịch vụ tính phí theo giờ.
- Nắm vững chu trình vòng đời tài nguyên đám mây (Cloud Lifecycle Management) từ khởi tạo, vận hành, kiểm thử đến dọn dẹp theo chuẩn FinOps.