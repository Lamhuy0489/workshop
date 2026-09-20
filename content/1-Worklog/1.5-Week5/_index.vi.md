---
title: "Worklog Tuần 5"
date: 2026-09-20
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:
* Nghiên cứu và làm chủ ba trụ cột quan sát hệ thống (Three Pillars of Observability: Metrics, Logs, Alarms & Traces) trên nền tảng Amazon Web Services (AWS).
* Khai thác dịch vụ giám sát Amazon CloudWatch: Thu thập, phân tích chỉ số định lượng phần cứng của các máy chủ ảo Amazon EC2 (`CPUUtilization`, `NetworkIn`, `NetworkOut`).
* Ứng dụng kỹ thuật tính toán chỉ số động (CloudWatch Metric Math): Viết biểu thức `(m2 + m3) / 1024` chuyển đổi tổng thông lượng mạng vao/ra sang đơn vị Kilobytes (KB) phục vụ trực quan hóa theo thời gian thực.
* Quản trị nhật ký tập trung với CloudWatch Logs: Khởi tạo các nhóm nhật ký Log Groups (`/huylam/cloudwatch/system-logs`, `/huylam/cloudwatch/httpd-access`), phân luồng Log Streams và đồng bộ dữ liệu sự kiện hệ thống.
* Sử dụng ngôn ngữ truy vấn CloudWatch Logs Insights: Thực thi câu lệnh truy vấn cấu trúc nhằm trích xuất và lọc bản ghi sự kiện mang thông tin định danh sinh viên Lâm Quang Huy (MSSV: `0212267`).
* Thiết lập kênh phân phối thông báo khẩn cấp Amazon Simple Notification Service (Amazon SNS): Tạo Topic `huylam-cw-alarms`, cấu hình đăng ký nhận thư qua hộp thư điện tử `huyngu127@gmail.com` và xác thực liên kết (Subscription Confirmed).
* Cấu hình cơ chế cảnh báo tự động CloudWatch Alarms: Thiết lập ngưỡng tĩnh Static Threshold (`>= 70%`) cho chỉ số CPU trên phiên bản máy chủ `i-048fa1b4099b74bb7`, chu kỳ đánh giá 1 phút (60 giây), tự động kích hoạt thông báo SNS khi hệ thống quá tải.
* Kiểm nghiệm thực tế và mô phỏng tải cao (Stress Testing): Sử dụng công cụ ép tải vi xử lý đạt 100% để xác thực quy trình chuyển trạng thái từ `OK` sang `ALARM` (chạm mốc 93.86%), tiếp nhận email cảnh báo và tự động phục hồi về `OK` (6.46%).
* Thiết kế bảng điều khiển vận hành tập trung CloudWatch Dashboard (`huylam-monitoring-dashboard`): Tích hợp 4 loại widget chuyên sâu gồm trạng thái cảnh báo, chỉ số đơn lẻ, biểu đồ đường biến động CPU kèm vạch ngưỡng và biểu đồ tính toán lưu lượng mạng.
* Thực hành văn hóa quản trị chi phí FinOps: Đo kiểm bảng điều khiển Billing and Cost Management, phân tích chi phí lũy kế Month-to-date (0.10 USD), kiểm tra 2 chỉ tiêu ngân sách AWS Budgets đạt chuẩn Healthy và giải phóng toàn bộ tài nguyên tính toán sau kiểm nghiệm.

---

### Các công việc đã triển khai trong tuần 5:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2** | - Nghiên cứu lý thuyết Observability: Phân biệt Metrics, Logs, Traces.<br>- Khảo sát kiến trúc thu thập dữ liệu của Amazon CloudWatch.<br>- Tìm hiểu chu kỳ thu thập dữ liệu mặc định (Basic Monitoring: 5 phút vs Detailed Monitoring: 1 phút). | Nắm vững nguyên lý hoạt động của CloudWatch và cách thức AWS giám sát hạ tầng đám mây. | [AWS CloudWatch Concepts](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html) |
| **Thứ 3** | - Tìm hiểu CloudWatch Metrics và không gian tên (Namespaces).<br>- Thu thập các thông số vận hành của EC2 (`CPUUtilization`, `NetworkIn`, `NetworkOut`).<br>- Xây dựng biểu thức toán học Metric Math `(m2 + m3) / 1024` tính tổng băng thông mạng theo KB. | Thiết lập biểu đồ trực quan hóa dữ liệu hiệu năng mạng kết hợp từ nhiều luồng chỉ số độc lập. | [CloudWatch Metric Math Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) |
| **Thứ 4** | - Tìm hiểu cơ chế quản lý nhật ký CloudWatch Logs: Log Groups, Log Streams, Retention Policies.<br>- Khởi tạo Log Groups `/huylam/cloudwatch/system-logs` và `/huylam/cloudwatch/httpd-access`.<br>- Viết câu truy vấn Logs Insights trích xuất thông tin định danh sinh viên. | Trích xuất thành công 8 bản ghi sự kiện hệ thống xác thực danh tính sinh viên Lâm Quang Huy (MSSV: 0212267). | [CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) |
| **Thứ 5** | - Nghiên cứu dịch vụ phân phối thông báo Amazon SNS.<br>- Khởi tạo SNS Topic `huylam-cw-alarms` và cấu hình Email Subscription tới `huyngu127@gmail.com`.<br>- Xác nhận liên kết đăng ký qua email (Subscription Confirmed). | Thiết lập thành công hạ tầng truyền dẫn cảnh báo tự động từ CloudWatch tới quản trị viên. | [Amazon SNS Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/welcome.html) |
| **Thứ 6** | - Khởi tạo CloudWatch Alarm `huylam-ec2-high-cpu-alarm` với ngưỡng Static Threshold >= 70%.<br>- Gán hành động gửi thông báo qua SNS Topic `huylam-cw-alarms`.<br>- Thực hiện kiểm tra trạng thái ban đầu của Alarm (trạng thái OK). | Hoàn tất cấu hình luật giám sát tự động bảo vệ máy chủ EC2 trước nguy cơ tràn tải vi xử lý. | [Lab 000008](https://000008.awsstudygroup.com) |
| **Thứ 7** | - Triển khai bài đo kiểm chịu tải cao (CPU Stress Test) trên máy chủ `i-048fa1b4099b74bb7`.<br>- Ghi nhận vi xử lý tăng vọt lên 93.86%, Alarm chuyển sang trạng thái ALARM.<br>- Xác thực email cảnh báo gửi về Gmail, sau đó theo dõi hệ thống tự phục hồi về OK (6.46%). | Kiểm chứng thực nghiệm thành công 100% vòng đời phát hiện sự cố, gửi thông báo và tự phục hồi. | [AWS Systems Manager Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/) |
| **Chủ Nhật**| - Xây dựng CloudWatch Dashboard `huylam-monitoring-dashboard` với 4 widgets chuyên sâu.<br>- Kiểm tra báo cáo FinOps: Billing Dashboard ghi nhận MTD 0.10 USD, 2 Budgets đạt trạng thái Healthy.<br>- Thực hiện quy trình FinOps Cleanup: Xóa toàn bộ Alarms, Dashboards, Log Groups, SNS Topics và terminate EC2 instances.<br>- Hoàn thiện hồ sơ minh chứng kỹ thuật và cập nhật tài liệu. | Hoàn thành toàn diện bài thực hành Lab 000008, bảo toàn 100% định mức ngân sách AWS Free Tier. | [FCJ Curriculum](file:///Users/huylam/Downloads/aws/raw/labs/fcj-cloud-journey-curriculum.md) |

---

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:

#### 1. Định danh tài khoản & Khu vực (Identity & Region):
- **AWS Account ID**: `677994024390`
- **Tên tài khoản (Account Name)**: `huylam`
- **IAM User thực thi**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **AWS Region**: `ap-southeast-1` (Asia Pacific - Singapore)
- **VPC trực thuộc**: `huylam-vpc` (`vpc-0125f4d6db3fbffa6`)

#### 2. Hạ tầng tính toán máy chủ ảo (Compute Infrastructure):
* **Máy chủ kiểm nghiệm 1**: `i-010014c0c84c08ef6` (Amazon Linux 2023, `t3.micro`)
* **Máy chủ kiểm nghiệm 2 (Máy chủ thực thi Stress Test)**:
  - Instance ID: `i-048fa1b4099b74bb7`
  - Phân hạng phần cứng: `t3.micro` (2 vCPU, 1 GiB RAM)
  - Hệ điều hành: Amazon Linux 2023 Kernel 6.1 x86_64
  - Mục đích: Đo kiểm tải CPU, kích hoạt quy trình chuyển đổi trạng thái Alarm và đo kiểm độ trễ thông báo SNS.

#### 3. Kênh phân phối thông báo sự cố Amazon SNS:
- **Tên SNS Topic**: `huylam-cw-alarms`
- **Topic ARN**: `arn:aws:sns:ap-southeast-1:677994024390:huylam-cw-alarms`
- **Phương thức chuyển tiếp (Protocol)**: Email
- **Đích tiếp nhận thông báo**: `huyngu127@gmail.com`
- **Trạng thái xác thực thuê bao**: `Confirmed`

#### 4. Thu thập chỉ số và Biểu thức toán học CloudWatch Metrics & Metric Math:
- **Không gian tên (Namespace)**: `AWS/EC2`
- **Các luồng chỉ số cơ sở (Base Metrics)**:
  - Metric `m1`: `CPUUtilization` trên phiên bản `i-048fa1b4099b74bb7` (Đơn vị: Percent, Chu kỳ: 60s)
  - Metric `m2`: `NetworkIn` trên phiên bản `i-048fa1b4099b74bb7` (Đơn vị: Bytes, Chu kỳ: 60s)
  - Metric `m3`: `NetworkOut` trên phiên bản `i-048fa1b4099b74bb7` (Đơn vị: Bytes, Chu kỳ: 60s)
- **Biểu thức toán học Metric Math**:
  - Mã định danh biểu thức (Expression ID): `e1`
  - Biểu thức tính toán: `(m2 + m3) / 1024`
  - Nhãn hiển thị (Label): `Total Network KB`
  - Tác dụng: Tự động cộng tổng lượng dữ liệu mạng vào và ra của máy chủ rồi chia cho 1024 để quy đổi ra đơn vị Kilobytes, giúp các kỹ sư vận hành dễ dàng theo dõi lưu lượng mạng tức thời mà không cần tính toán thủ công.

#### 5. Quản lý tập trung nhật ký hệ thống CloudWatch Logs:
- **Danh sách Log Groups khởi tạo**:
  - `/huylam/cloudwatch/system-logs`: Thu thập nhật ký vận hành nhân hệ điều hành và thông điệp dịch vụ.
  - `/huylam/cloudwatch/httpd-access`: Thu thập nhật ký truy cập máy chủ web HTTP.
- **Log Stream phụ trách**: `ec2-system-stream`
- **Ngôn ngữ truy vấn CloudWatch Logs Insights**:
  ```sql
  fields @timestamp, @message
  | sort @timestamp desc
  | limit 20
  ```
- **Kết quả đo kiểm truy vấn**: Hệ thống trả về 8 bản ghi sự kiện có cấu trúc, chứa thông tin định danh sinh viên Lâm Quang Huy (MSSV: `0212267`) tại thời điểm thực thi.

#### 6. Cơ chế cảnh báo tự động CloudWatch Alarm:
- **Tên cảnh báo (Alarm Name)**: `huylam-ec2-high-cpu-alarm`
- **Alarm ARN**: `arn:aws:cloudwatch:ap-southeast-1:677994024390:alarm:huylam-ec2-high-cpu-alarm`
- **Mô tả cấu hình**: `CloudWatch Alarm for student Lam Quang Huy (MSSV: 0212267) - High CPU Utilization on i-048fa1b4099b74bb7`
- **Chỉ số theo dõi**: `AWS/EC2` -> `CPUUtilization` trên `i-048fa1b4099b74bb7`
- **Điều kiện ngưỡng (Alarm Conditions)**:
  - Ngưỡng đánh giá (Threshold Type): Static Threshold
  - Điều kiện kích hoạt: `CPUUtilization >= 70%`
  - Số lượng điểm dữ liệu vi phạm: 1 trong 1 chu kỳ đánh giá gần nhất (1 out of 1 datapoints)
  - Chu kỳ đánh giá (Evaluation Period): 60 giây (1 phút)
  - Phương pháp xử lý dữ liệu khuyết thiếu: `missing` (Treat missing data as missing)
- **Hành động phản ứng (Actions)**:
  - Trạng thái kích hoạt: `In ALARM`
  - Hành động thực thi: Gửi thông báo tới SNS Topic `huylam-cw-alarms` (`arn:aws:sns:ap-southeast-1:677994024390:huylam-cw-alarms`)

#### 7. Bảng điều khiển giám sát CloudWatch Dashboard:
- **Tên bảng điều khiển**: `huylam-monitoring-dashboard`
- **Cấu trúc bố cục 4 Widgets hoàn chỉnh**:
  - Widget 1 (Alarm Status): Hiển thị trạng thái màu xanh trực quan của Alarm `huylam-ec2-high-cpu-alarm` (trạng thái `OK`).
  - Widget 2 (Single Value): Hiển thị giá trị vi xử lý tức thời hiện tại của máy chủ EC2.
  - Widget 3 (Line Graph - CPU Utilization): Biểu đồ đường liên tục thể hiện lịch sử sử dụng CPU, tích hợp đường giới hạn đỏ tĩnh tại mức 70% và đỉnh tải kiểm nghiệm đạt gần 100%.
  - Widget 4 (Line Graph - Metric Math Network): Biểu đồ đường thể hiện chỉ số Metric Math `(m2 + m3) / 1024` tính bằng đơn vị KB.

#### 8. Báo cáo quản trị chi phí AWS Billing, Cost Explorer & Budgets:
- **Chi phí lũy kế trong tháng (Month-to-date - MTD)**: `0.10 USD`
- **Dự báo chi phí cuối tháng (Month-end Forecast)**: `0.14 USD`
- **Tình trạng AWS Budgets**: 2 ngân sách cảnh báo (5 USD và 10 USD) đang hoạt động ổn định ở trạng thái `HEALTHY`.
- **Thực hành FinOps**: Chi phí phát sinh được tối ưu hóa ở mức tối thiểu tuyệt đối, bảo toàn toàn vẹn hạn mức AWS Free Tier cho các bài học tiếp theo.

---

### Hình ảnh minh chứng triển khai thực tế trên AWS:

Tất cả các hình ảnh minh chứng dưới đây đều được chụp trực tiếp từ giao diện điều khiển AWS Management Console và hộp thư email thông báo thực tế. Các vị trí trọng yếu gồm huy hiệu định danh tài khoản `huylam (677994024390)`, khu vực Singapore `ap-southeast-1` và các thông số kỹ thuật cốt lõi đều được đóng khung viền đỏ nổi bật, chuẩn xác:

#### 1. Thiết lập biểu thức toán học Metric Math trên CloudWatch Metrics:
- **Mô tả**: Giao diện CloudWatch Metrics theo dõi đồng thời ba chỉ số `CPUUtilization` (m1), `NetworkIn` (m2), `NetworkOut` (m3) và áp dụng biểu thức toán học `(m2 + m3) / 1024` (Id: `e1`, Label: `Total Network KB`) để tổng hợp lưu lượng mạng theo thời gian thực.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, khu vực chọn dải thời gian hiển thị (1 giờ qua) và bảng danh sách chỉ số cùng biểu thức Metric Math `(m2 + m3) / 1024`.

![Cấu hình Metric Math trên CloudWatch](/images/week5/01-cloudwatch-metrics-math.png)

---

#### 2. Danh sách các nhóm nhật ký CloudWatch Log Groups:
- **Mô tả**: Quản lý tập trung các nhóm nhật ký hệ thống trên CloudWatch Logs, bao gồm Log Group `/huylam/cloudwatch/system-logs` phục vụ thu thập log nhân Linux và `/huylam/cloudwatch/httpd-access` thu thập log dịch vụ web.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và danh mục các Log Groups mang tên tiền tố `/huylam/cloudwatch/*`.

![Danh sách Log Groups](/images/week5/02-cloudwatch-log-groups.png)

---

#### 3. Chi tiết Log Group `/huylam/cloudwatch/system-logs` và Log Stream:
- **Mô tả**: Kiểm tra chi tiết cấu hình của nhóm nhật ký `/huylam/cloudwatch/system-logs`, ARN định danh dịch vụ và luồng nhật ký phụ trách `ec2-system-stream`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, bảng tóm tắt Log group ARN, chính sách lưu trữ (Retention period: Never expire) và danh sách Log streams.

![Chi tiết Log Group và Log Streams](/images/week5/03-cloudwatch-log-group-details.png)

---

#### 4. Truy vấn phân tích nhật ký với CloudWatch Logs Insights:
- **Mô tả**: Thực thi câu lệnh truy vấn cấu trúc SQL-like trên nhóm `/huylam/cloudwatch/system-logs`, trích xuất thành công 8 dòng nhật ký hệ thống mang thông điệp định danh sinh viên Lâm Quang Huy (MSSV: `0212267`).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, khung soạn thảo câu lệnh truy vấn Logs Insights và bảng kết quả hiển thị 8 bản ghi sự kiện có cấu trúc.

![Truy vấn CloudWatch Logs Insights](/images/week5/04-cloudwatch-logs-insights-query.png)

---

#### 5. Lựa chọn chỉ số giám sát CPUUtilization cho máy chủ EC2:
- **Mô tả**: Khởi tạo CloudWatch Alarm bằng cách định vị phiên bản máy chủ `i-048fa1b4099b74bb7` trong không gian tên `AWS/EC2 > Per-Instance Metrics` và lựa chọn chỉ số `CPUUtilization`.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, hàng chỉ số của Instance `i-048fa1b4099b74bb7` và nút bấm xác nhận chọn chỉ số (Select metric).

![Chọn chỉ số CPUUtilization](/images/week5/05-cloudwatch-alarm-select-metric.png)

---

#### 6. Cấu hình điều kiện cảnh báo Static Threshold:
- **Mô tả**: Thiết lập quy tắc kích hoạt cảnh báo khi chỉ số `CPUUtilization` vượt quá hoặc bằng ngưỡng tĩnh 70% trong khoảng thời gian đánh giá 1 phút (60 giây), đồ thị trực quan hiển thị đường chỉ báo ngưỡng màu đỏ.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, biểu đồ đo lường với đường ngưỡng 70%, cùng khung điều kiện Threshold type (Static) và giá trị ngưỡng 70.

![Cấu hình điều kiện Static Threshold](/images/week5/06-cloudwatch-alarm-conditions.png)

---

#### 7. Gán hành động thông báo Amazon SNS và đặt tên Alarm:
- **Mô tả**: Cấu hình phản ứng khi hệ thống rơi vào trạng thái `In ALARM`, tự động chuyển tiếp thông báo tới SNS Topic `huylam-cw-alarms`, đồng thời đặt tên cảnh báo kèm thông tin định danh sinh viên.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, phần cấu hình Notification gán SNS Topic `huylam-cw-alarms` và trường Alarm description chứa tên sinh viên Lâm Quang Huy (MSSV: `0212267`).

![Gán hành động SNS và mô tả Alarm](/images/week5/07-cloudwatch-alarm-actions-details.png)

---

#### 8. Thông báo khởi tạo CloudWatch Alarm thành công:
- **Mô tả**: AWS xác nhận khởi tạo thành công cảnh báo `huylam-ec2-high-cpu-alarm`, cảnh báo bắt đầu đi vào chu kỳ theo dõi định kỳ trên tài nguyên máy chủ ảo.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thanh thông báo tạo thành công màu xanh lục và hàng hiển thị tên `huylam-ec2-high-cpu-alarm`.

![Tạo Alarm thành công](/images/week5/08-cloudwatch-alarm-created-success.png)

---

#### 9. Trạng thái hoạt động bình thường (OK) của Alarm sau bài kiểm tra tải:
- **Mô tả**: Sau khi quá trình ép tải CPU kết thúc, mức độ sử dụng vi xử lý hạ nhiệt xuống 6.46%, hệ thống tự động đưa cảnh báo `huylam-ec2-high-cpu-alarm` trở về trạng thái màu xanh an toàn (`OK`).
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và hàng chi tiết cảnh báo hiển thị trạng thái OK với giá trị đo đạc 6.46% (thấp hơn ngưỡng 70%).

![Alarm trở về trạng thái OK](/images/week5/09-cloudwatch-alarm-status-ok.png)

---

#### 10. Email cảnh báo tức thời gửi từ AWS SNS tới hộp thư quản trị viên:
- **Mô tả**: Ảnh chụp chi tiết thư điện tử nhận từ `AWS Notifications <no-reply@sns.amazonaws.com>` tại hộp thư `huyngu127@gmail.com` khi CPU chạm mốc 93.86%, thông báo rõ trạng thái chuyển dịch từ `OK -> ALARM` cùng định danh sinh viên Lâm Quang Huy (MSSV: `0212267`) và AWS Account `677994024390`.
- **Vùng khoanh đỏ**: Tiêu đề email `ALARM: "huylam-ec2-high-cpu-alarm" in Asia Pacific (Singapore)`, thông tin người gửi AWS Notifications kèm thời gian gửi, và toàn bộ khối nội dung cảnh báo chi tiết xác nhận vượt ngưỡng 93.86% và tài khoản `677994024390`.

![Email cảnh báo từ AWS SNS](/images/week5/10-cloudwatch-alarm-email-notification.png)

---

#### 11. Bảng điều khiển tổng hợp CloudWatch Dashboard (`huylam-monitoring-dashboard`):
- **Mô tả**: Bảng điều khiển trực quan gồm 4 widgets chuyên nghiệp: Widget trạng thái cảnh báo Alarm Status, Widget giá trị số CPU tức thời, Widget đồ thị đường CPU thể hiện rõ đỉnh tải ép vi xử lý vượt ngưỡng 70%, và Widget biểu thức toán học Metric Math tổng hợp lưu lượng mạng theo KB.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)` và 4 khung viền đỏ bao quanh chính xác từng widget trên bảng điều khiển.

![Bảng điều khiển CloudWatch Dashboard](/images/week5/11-cloudwatch-monitoring-dashboard.png)

---

#### 12. Quản trị chi phí và Kiểm tra ngân sách trên AWS Billing:
- **Mô tả**: Kiểm tra tổng thể bảng điều khiển Billing and Cost Management, ghi nhận chi phí tích lũy trong tháng chỉ dừng lại ở mức 0.10 USD (dự báo 0.14 USD), đồng thời 2 chỉ tiêu ngân sách Budgets đều ở trạng thái Healthy tuyệt đối.
- **Vùng khoanh đỏ**: Huy hiệu tài khoản `huylam (677994024390)`, thẻ Cost summary hiển thị Month-to-date spend 0.10 USD và thẻ Budgets hiển thị 2 active budgets ở trạng thái Healthy.

![Báo cáo Billing và Budgets](/images/week5/12-aws-billing-cost-budgets.png)

---

### Kiểm nghiệm thực tế và đo kiểm chỉ số kỹ thuật:

#### 1. Kiểm nghiệm kích hoạt tải cao (CPU Stress Test) và chu kỳ chuyển trạng thái:
Để kiểm chứng năng lực phát hiện sự cố của CloudWatch Alarm, một tập lệnh mô phỏng tải CPU cực hạn đã được kích hoạt trên phiên bản `i-048fa1b4099b74bb7`:
```bash
# Cài đặt công cụ stress-ng trên Amazon Linux 2023
sudo dnf install -y stress-ng

# Kích hoạt ép tải toàn bộ 2 nhân vCPU trong vòng 300 giây
stress-ng --cpu 2 --timeout 300s --metrics-brief
```

*Diễn biến đo kiểm thu được*:
1. **Giai đoạn trước khi ép tải (T = 0s)**: Mức tiêu thụ CPU duy trì ổn định dưới `5%`. Cảnh báo `huylam-ec2-high-cpu-alarm` ở trạng thái `OK`.
2. **Giai đoạn phát sinh quá tải (T = 60s -> 120s)**: Cả 2 vCPU chạm ngưỡng 100% công suất tính toán. CloudWatch thu thập điểm dữ liệu tại chu kỳ kế tiếp ghi nhận giá trị CPUUtilization trung bình đạt `93.858%` (`93.86%`), vượt xa ngưỡng cảnh báo `70%`.
3. **Giai đoạn kích hoạt cảnh báo (T = 120s)**: CloudWatch thực hiện đối chiếu điều kiện đánh giá (1 out of 1 datapoints >= 70%) và lập tức chuyển trạng thái Alarm từ `OK` sang `ALARM`.
4. **Giai đoạn chuyển tiếp thông điệp (T = 125s)**: Sự kiện chuyển trạng thái kích hoạt hành động phát thông điệp tới Amazon SNS Topic `huylam-cw-alarms`. Hệ thống SNS tiến hành đẩy thư điện tử thông báo tới hộp thư `huyngu127@gmail.com` với độ trễ dưới 5 giây.
5. **Giai đoạn tự phục hồi (T = 300s -> 360s)**: Lệnh stress-ng kết thúc, CPU hạ nhiệt xuống mức `6.46%`. Sau chu kỳ đánh giá tiếp theo, CloudWatch Alarm tự động chuyển trạng thái từ `ALARM` về `OK`.

#### 2. Đo kiểm truy vấn cấu trúc nhật ký với CloudWatch Logs Insights:
Thực hiện chạy câu lệnh truy vấn cấu trúc trên Log Group `/huylam/cloudwatch/system-logs`:
```sql
fields @timestamp, @message
| sort @timestamp desc
| limit 20
```

*Kết quả phân tích truy vấn*:
```text
---------------------------------------------------------------------------------------------------------------------
| @timestamp               | @message                                                                               |
+--------------------------+----------------------------------------------------------------------------------------+
| 2026-09-20T16:04:15.000Z | [SYSTEM_EVENT] Student: Lam Quang Huy (MSSV: 0212267) - Node i-048fa1b4099b74bb7 OK    |
| 2026-09-20T16:04:10.000Z | [SYSTEM_EVENT] CloudWatch Logs Agent health status verified. System healthy.          |
| 2026-09-20T16:04:05.000Z | [SYSTEM_EVENT] Student: Lam Quang Huy (MSSV: 0212267) - Monitoring initialized        |
| 2026-09-20T16:04:00.000Z | [SYSTEM_EVENT] HTTP Server started listening on port 80.                              |
| 2026-09-20T16:03:55.000Z | [SYSTEM_EVENT] Memory buffer allocation checked: 1024 MB available.                   |
| 2026-09-20T16:03:50.000Z | [SYSTEM_EVENT] Student: Lam Quang Huy (MSSV: 0212267) - Kernel 6.1 loaded successfully|
| 2026-09-20T16:03:45.000Z | [SYSTEM_EVENT] Network interface ens5 initialized. DHCP lease acquired.                |
| 2026-09-20T16:03:40.000Z | [SYSTEM_EVENT] System boot completed for instance i-048fa1b4099b74bb7.                |
---------------------------------------------------------------------------------------------------------------------
```
*Đánh giá*: Logs Insights cho phép phân tích hàng triệu dòng log với tốc độ cao, khả năng trích xuất trường dữ liệu linh hoạt giúp việc điều tra nguyên nhân gốc rễ (Root Cause Analysis - RCA) trở nên chuẩn xác và nhanh chóng.

#### 3. Phân tích lưu lượng mạng đa chiều với CloudWatch Metric Math:
Bằng cách sử dụng biểu thức Metric Math `(m2 + m3) / 1024`, đồ thị tổng hợp mạng mang lại những ưu thế vận hành vượt trội:
- Không cần sửa đổi mã nguồn ứng dụng hay cài đặt thêm tác nhân bên ngoài, CloudWatch tự động tổng hợp 2 luồng chỉ số chuẩn `NetworkIn` và `NetworkOut`.
- Quy đổi dữ liệu tức thời từ Bytes sang KB giúp kỹ sư hạ tầng nắm bắt lưu lượng băng thông một cách tự nhiên mà không bị nhầm lẫn bởi các con số hàng triệu Bytes.
- Giảm thiểu số lượng widget cần theo dõi trên Dashboard từ 2 widget riêng lẻ xuống còn 1 widget duy nhất, tối ưu hóa không gian hiển thị trên màn hình điều hành NOC (Network Operations Center).

---

### Ba trụ cột quan sát trên AWS (Three Pillars of Observability):

| Tiêu chí | Chỉ số định lượng (Metrics) | Nhật ký sự kiện (Logs) | Dấu vết & Cảnh báo (Traces / Alarms) |
| :--- | :--- | :--- | :--- |
| **Bản chất dữ liệu** | Dữ liệu chuỗi thời gian dạng số (Time-series numerical data). | Dòng sự kiện văn bản phi cấu trúc hoặc bán cấu trúc kèm dấu thời gian. | Đường đi của luồng yêu cầu qua các dịch vụ phân tán hoặc trạng thái ngưỡng. |
| **Dịch vụ đại diện trên AWS** | Amazon CloudWatch Metrics. | Amazon CloudWatch Logs, CloudWatch Logs Insights. | AWS X-Ray, CloudWatch ServiceLens, CloudWatch Alarms. |
| **Mục đích sử dụng** | Theo dõi sức khỏe tổng thể, xu hướng tải, phát hiện bất thường và kích hoạt tự động co giãn. | Truy vết chi tiết lỗi, kiểm tra nguyên nhân gốc rễ, rà soát an ninh và đối soát giao dịch. | Xác định nút thắt cổ chai (bottlenecks), giám sát độ trễ vi dịch vụ và cảnh báo tức thời. |
| **Độ trễ cập nhật** | 1 phút (Detailed) hoặc 5 phút (Basic). | Gần thời gian thực (vài giây sau khi sự kiện phát sinh). | Dưới 1 phút (khi điểm dữ liệu vi phạm được đối chiếu). |
| **Chi phí lưu trữ** | Miễn phí cho các chỉ số cơ bản của AWS; tính phí cho Custom Metrics. | Tính phí theo dung lượng nạp vào (Ingestion: ~0.50 USD/GB) và dung lượng lưu trữ (~0.03 USD/GB/tháng). | Miễn phí 10 standard alarms đầu tiên; 0.10 USD/alarm/tháng đối với các alarm tiếp theo. |

---

### Quản trị chi phí và Thực hành FinOps (FinOps Best Practices):

1. **Hiểu rõ mô hình chi phí của bộ giải pháp Giám sát AWS CloudWatch**:
   - **Chỉ số mặc định (Basic Metrics)**: Được AWS cung cấp miễn phí ở tần suất 5 phút cho hầu hết các dịch vụ cốt lõi như EC2, EBS, RDS. Bật giám sát chi tiết (Detailed Monitoring - chu kỳ 1 phút) sẽ phát sinh thêm chi phí.
   - **Nhật ký sự kiện (Logs)**: Được miễn phí 5 GB dung lượng nạp và 5 GB dung lượng lưu trữ hàng tháng trong gói Free Tier. Để tránh phát sinh chi phí khi hết hạn Free Tier, luôn cấu hình Retention Policy (ví dụ: 7 ngày hoặc 30 ngày) thay vì để mặc định `Never expire`.
   - **Cảnh báo (Alarms)**: Miễn phí 10 cảnh báo chuẩn mỗi tháng. Cảnh báo tổng hợp (Composite Alarms) hoặc cảnh báo độ phân giải cao (High-resolution Alarms) có biểu phí riêng.
   - **Bảng điều khiển (Dashboards)**: Miễn phí tối đa 3 bảng điều khiển với tối đa 50 chỉ số mỗi tháng trong AWS Free Tier.

2. **Quy trình giải phóng tài nguyên triệt để sau kiểm nghiệm (Resource Teardown)**:
   Sau khi hoàn tất bài thực hành và thu thập đầy đủ 12 minh chứng kỹ thuật, toàn bộ các tài nguyên thử nghiệm đã được dọn dẹp theo trình tự sau:
   - Xóa CloudWatch Alarm: `aws cloudwatch delete-alarms --alarm-names huylam-ec2-high-cpu-alarm`
   - Xóa CloudWatch Dashboard: `aws cloudwatch delete-dashboards --dashboard-names huylam-monitoring-dashboard`
   - Xóa các CloudWatch Log Groups:
     - `aws logs delete-log-group --log-group-name /huylam/cloudwatch/system-logs`
     - `aws logs delete-log-group --log-group-name /huylam/cloudwatch/httpd-access`
   - Xóa Amazon SNS Topic: `aws sns delete-topic --topic-arn arn:aws:sns:ap-southeast-1:677994024390:huylam-cw-alarms`
   - Thu hồi và đóng máy chủ ảo EC2: `aws ec2 terminate-instances --instance-ids i-010014c0c84c08ef6 i-048fa1b4099b74bb7`

3. **Kết quả đạt được về mặt FinOps**:
   - Chi phí thực tế ghi nhận trong tháng: `0.10 USD` (MTD).
   - Dự báo cả tháng: `0.14 USD` (nằm trọn vẹn trong vùng an toàn tuyệt đối).
   - Hai hạn mức AWS Budgets (5 USD và 10 USD) duy trì trạng thái `HEALTHY`.
   - Toàn bộ tài nguyên tính toán và lưu trữ rác được giải phóng 100%, bảo vệ an toàn ngân sách cho tài khoản đám mây.

---

### Bài học kinh nghiệm & Kết luận:

1. **Chuyển dịch từ giám sát thụ động sang cảnh báo chủ động (Proactive Monitoring)**: Việc dựa vào việc người dùng báo lỗi để xử lý là cách tiếp cận lỗi thời. Thiết lập CloudWatch Alarms kết hợp với Amazon SNS giúp đội ngũ kỹ sư nhận diện và can thiệp sự cố ngay từ khi hệ thống mới bắt đầu có dấu hiệu quá tải, trước khi dịch vụ bị gián đoạn hoàn toàn.
2. **Khai thác tối đa sức mạnh của CloudWatch Metric Math và Logs Insights**: Việc làm chủ các công cụ phân tích tích hợp sẵn giúp loại bỏ nhu cầu phải đầu tư hạ tầng giám sát bên ngoài tốn kém (như Datadog hay Splunk) trong giai đoạn đầu của dự án, đồng thời tăng tốc độ xử lý sự cố nhờ sự đồng bộ và liên kết chặt chẽ trong hệ sinh thái AWS.
3. **FinOps là kỷ luật thường trực trong kỹ nghệ đám mây**: Việc thiết lập cảnh báo tài nguyên không chỉ phục vụ mục tiêu kỹ thuật (CPU, RAM, Network) mà còn phải song hành cùng việc kiểm soát chi phí (Billing & Budgets). Dọn dẹp tài nguyên ngay sau khi nghiệm thu là nguyên tắc bắt buộc đối với một kỹ sư đám mây chuyên nghiệp.