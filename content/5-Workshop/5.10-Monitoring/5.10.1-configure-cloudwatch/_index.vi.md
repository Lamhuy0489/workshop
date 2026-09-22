---
title: "Cấu hình Amazon CloudWatch"
date: 2026-09-23
weight: 1
chapter: false
pre: " <b> 5.10.1. </b> "
---

### Mục tiêu thực hành

Khám phá và thiết lập hệ thống quan sát tập trung với Amazon CloudWatch cho nền tảng Document OCR & Translation: Kiểm tra nhóm nhật ký CloudWatch Logs của AWS Lambda, theo dõi trạng thái sức khỏe thời gian thực của Target Group, đo kiểm chỉ số tài nguyên của máy chủ EC2, và khởi tạo CloudWatch Alarm cảnh báo quá tải CPU.

---

## 1. Kiểm tra CloudWatch Logs của AWS Lambda

Hàm AWS Lambda `huylam-ocr-processor` tự động đẩy toàn bộ thông điệp nhật ký chuẩn (standard output và standard error) lên CloudWatch Logs nhờ chính sách thực thi `AWSLambdaBasicExecutionRole`.

### Các bước kiểm tra nhật ký:

1. Truy cập **AWS Console -> Amazon CloudWatch -> Log groups**.
2. Tìm và chọn nhóm nhật ký: **`/aws/lambda/huylam-ocr-processor`**.

![CloudWatch Log Group Overview](/images/week11/14-cloudwatch-log-group-overview.png)

3. Tại mục **Log streams**, nhấp vào luồng sự kiện mới nhất được tạo khi có tệp tải lên Amazon S3.
4. Kiểm tra các dòng nhật ký chi tiết:
   - Thông báo tiếp nhận payload từ S3: `Nhan su kien moi tu Amazon S3 Event Notification`.
   - Bóc tách đường dẫn đối tượng: `Phat hien tep moi: s3://huylam-ocr-documents-ap-southeast-1/uploads/...`.
   - Kết quả ghi vào cơ sở dữ liệu: `Da luu tien trinh vao DynamoDB (job_id: auto-...)`.
   - Báo cáo tổng kết thực thi (REPORT):
     - **Duration**: `214.28 ms`
     - **Billed Duration**: `215 ms`
     - **Memory Size**: `128 MB`
     - **Max Memory Used**: `88 MB`

![CloudWatch Log Events Execution](/images/week11/15-cloudwatch-log-events-execution.png)

Thời gian xử lý chỉ 214 ms chứng minh kiến trúc Event-Driven đạt hiệu quả tối ưu về độ trễ và chi phí.

---

## 2. Giám sát trạng thái sức khỏe Target Group (huylam-ocr-tg)

Application Load Balancer liên tục gửi các yêu cầu kiểm tra sức khỏe (Health Check) tới cổng 5000 của máy chủ EC2 thông qua đường dẫn `/login`.

1. Truy cập **EC2 Console -> Load Balancing -> Target Groups**.
2. Chọn Target Group **`huylam-ocr-tg`**.
3. Tại thẻ **Targets**, xác nhận mục **Target health**:
   - **Healthy**: `1`
   - **Unhealthy**: `0`
   - **Target ID**: `i-0566e1eedaacea52d:5000`
   - **Health status details**: `Target is healthy (Received response code: 200/302)`.

![Target Group Healthy Status](/images/week12/08-target-group-healthy-status.png)

Khi Target ở trạng thái Healthy, ALB sẽ định tuyến 100% lưu lượng truy cập từ người dùng internet vào máy chủ web một cách thông suốt.

---

## 3. Theo dõi chỉ số tài nguyên máy chủ EC2

1. Truy cập **EC2 Console -> Instances -> huylam-ocr-ec2 (`i-0566e1eedaacea52d`)**.
2. Mở thẻ **Monitoring** để quan sát các chỉ số tích hợp sẵn từ CloudWatch:
   - **CPUUtilization**: Dao động dưới 5% ở trạng thái nghỉ, tăng nhẹ lên 15% - 25% khi bóc tách tài liệu lớn.
   - **NetworkIn / NetworkOut**: Phản ánh chính xác lưu lượng tải tệp lên và phản hồi trang web.
   - **StatusCheckFailed (Instance / System)**: Luôn đạt giá trị `0` (Hệ thống phần cứng và hệ điều hành hoạt động hoàn hảo).

---

## 4. Khởi tạo CloudWatch Alarm cảnh báo quá tải CPU

Để tự động phát hiện tình trạng tải cao bất thường hoặc nguy cơ suy giảm hiệu năng trên máy chủ EC2, ta thiết lập một CloudWatch Alarm.

### Các bước tạo Alarm:

1. Truy cập **CloudWatch Console -> Alarms -> All alarms -> Create alarm**.
2. Nhấp **Select metric -> EC2 -> Per-Instance Metrics**:
   - Chọn metric: **`CPUUtilization`** cho Instance ID `i-0566e1eedaacea52d`.
3. Cấu hình điều kiện cảnh báo:
   - **Statistic**: `Average`.
   - **Period**: `5 minutes`.
   - **Threshold type**: `Static`.
   - **Whenever CPUUtilization is**: `Greater/Equal` (`>=`).
   - **than**: `80`.
4. Cấu hình hành động thông báo (Notification):
   - Trạng thái kích hoạt: **In alarm**.
   - Có thể cấu hình gửi cảnh báo về Amazon SNS Topic khi cần.
5. Đặt tên Alarm:
   - **Alarm name**: `huylam-ocr-ec2-high-cpu`.
   - **Alarm description**: `Canh bao khi muc su dung CPU cua may chu EC2 huylam-ocr vuot qua 80% trong 5 phut`.
6. Xem lại thông số và nhấp **Create alarm**.

7. Sau khi khởi tạo, danh sách Alarms hiển thị trạng thái **OK**, xác nhận mức tiêu thụ CPU của máy chủ `huylam-ocr-ec2` đang nằm hoàn toàn trong ngưỡng an toàn dưới 80%.

---

## 5. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn đã thiết lập thành công:

- Hệ thống thu thập nhật ký tự động từ Lambda Function trên CloudWatch Logs với độ trễ thấp và số liệu tiêu thụ bộ nhớ rõ ràng.
- Giám sát trạng thái hoạt động liên tục của ứng dụng EC2 thông qua ALB Health Check.
- Cảnh báo tự động CloudWatch Alarm sẵn sàng phản ứng khi máy chủ có dấu hiệu quá tải tài nguyên.
- Nâng cao năng lực sẵn sàng vận hành và hỗ trợ giải quyết sự cố theo chuẩn điện toán đám mây AWS.