---
title: "Worklog Tuần 12"
date: 2026-10-25
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

> [!NOTE] Thời gian thực hiện
> **Từ ngày 19/10/2026 đến ngày 25/10/2026**

### Mục tiêu tuần 12:
* Triển khai kiến trúc mạng doanh nghiệp 3 tầng (Three-Tier Enterprise Cloud Architecture) trên AWS phục vụ nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
  * **Thiết kế phân tầng mạng ảo VPC Multi-AZ**: Tận dụng hạ tầng mạng VPC tùy biến `huylam-vpc` (dải mạng `10.0.0.0/16`) với 2 Public Subnet trải rộng trên 2 Vùng sẵn sàng độc lập (`ap-southeast-1a` và `ap-southeast-1b`), tích hợp Internet Gateway `huylam-igw` và bảng định tuyến công khai.
  * **Thiết lập an ninh mạng phân tầng (Security Group Chaining)**: Khởi tạo và liên kết 2 tầng Security Groups độc lập:
    * `huylam-alb-sg`: Tiếp nhận lưu lượng HTTP cổng 80 từ toàn bộ người dùng Internet (`0.0.0.0/0`).
    * `huylam-web-sg`: Bảo vệ máy chủ ứng dụng, chỉ chấp nhận lưu lượng đến cổng 5000 khi và chỉ khi bắt nguồn từ chính Security Group của ALB (`huylam-alb-sg`), loại bỏ hoàn toàn rủi ro lộ cổng ứng dụng ra Internet.
  * **Phân quyền bảo mật IAM Instance Profile**: Cấu hình IAM Role `huylam-ssm-role` đính kèm chính sách `AmazonSSMManagedInstanceCore` (quản trị an toàn từ xa qua AWS Systems Manager Session Manager mà không cần mở cổng SSH), `AmazonS3FullAccess` và `AmazonDynamoDBFullAccess` để ứng dụng trực tiếp tương tác với kho lưu trữ tài liệu S3 và cơ sở dữ liệu DynamoDB.
  * **Khởi chạy và thiết lập máy chủ ứng dụng Amazon EC2**: Khởi chạy phiên bản EC2 `huylam-ocr-web-server` (`i-0566e1eedaacea52d`, Amazon Linux 2023, t2.micro), triển khai mã nguồn Web Studio từ GitHub, thiết lập môi trường ảo Python 3.11, và cấu hình daemon systemd `huylam-ocr.service` chạy máy chủ WSGI Gunicorn trên cổng 5000.
  * **Cấu hình Target Group và Application Load Balancer (ALB)**: Khởi tạo Target Group `huylam-ocr-tg`, cấu hình đường dẫn kiểm tra sức khỏe (Health Check Path) tối ưu tại `/login`, và thiết lập Application Load Balancer `huylam-ocr-alb` công khai Internet (Internet-facing) với cơ chế cân bằng tải Multi-AZ.
  * **Cấp phát đường link thật công khai (Public DNS URL) và nghiệm thu truy cập**: Kiểm thử thực tế từ trình duyệt Safari qua đường link công khai `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`, xác thực độ ổn định, khả năng chuyển tiếp lưu lượng và hiển thị giao diện Web Studio trên toàn cầu.
  * **Thu thập bộ 10 ảnh minh chứng thực tế có viền đỏ chuẩn xác**: Đóng khung đỏ làm nổi bật AWS Account Badge `huylam (677994024390)`, thông số hạ tầng VPC, Security Groups, trạng thái Healthy của Target Group và URL ALB thực tế.

---

### Các công việc đã triển khai trong tuần 12:

| Thứ | Công việc triển khai | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2 (19/10/2026)** | - Phân tích yêu cầu triển khai Production cho Web Studio trên AWS.<br>- Thiết kế mô hình mạng 3 tầng kết hợp Application Load Balancer và EC2 trong VPC Multi-AZ.<br>- Lập kế hoạch cấu hình Security Group theo phương thức chuỗi bảo mật (Chaining). | Hoàn thiện bản vẽ kiến trúc mạng phân tầng và ma trận quy hoạch cổng kết nối cho hạ tầng đám mây. | [AWS VPC Architecture Design](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) |
| **Thứ 3 (20/10/2026)** | - Người dùng tự tay thao tác trên AWS Management Console tạo Security Group cho ALB: `huylam-alb-sg` (`sg-0dca819306a96bfdb`), mở cổng HTTP 80 cho `0.0.0.0/0`.<br>- Tạo Security Group cho Web Server: `huylam-web-sg` (`sg-0ff9ea20c6c3a8dc8`), cấu hình Inbound Rule chỉ cho phép cổng TCP 5000 từ `huylam-alb-sg`. | Thiết lập thành công lá chắn an ninh phân tầng, ngăn chặn tuyệt đối các cuộc tấn công quét cổng trực tiếp vào máy chủ ứng dụng. | [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html) |
| **Thứ 4 (21/10/2026)** | - Cấu hình IAM Role `huylam-ssm-role` đính kèm các chính sách quyền hạn: `AmazonSSMManagedInstanceCore`, `AmazonS3FullAccess`, `AmazonDynamoDBFullAccess`.<br>- Khởi chạy máy chủ EC2 `huylam-ocr-web-server` (`i-0566e1eedaacea52d`, AMI AL2023, loại t2.micro) đặt trong subnet công khai `subnet-0efa7c3a5818035dc` (`ap-southeast-1a`). | Máy chủ ảo khởi chạy thành công, sẵn sàng nhận quyền tương tác với S3 và DynamoDB mà không cần lưu trữ Access Key trên máy chủ. | [AWS Systems Manager Role](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) |
| **Thứ 5 (22/10/2026)** | - Kết nối vào máy chủ EC2 thông qua AWS Systems Manager Session Manager.<br>- Khắc phục sự cố Outbound Rules trong Security Group cho phép truy cập Internet tải gói.<br>- Cài đặt Python 3.11, Git, kéo kho mã nguồn từ GitHub `Lamhuy0489/aws`.<br>- Thiết lập môi trường ảo và tạo systemd service `huylam-ocr.service` quản lý tiến trình Gunicorn. | Dịch vụ Web Studio kích hoạt thành công trên cổng nội bộ 5000, tự động khởi chạy cùng hệ điều hành (`enabled`). | [Gunicorn Systemd Deployment](https://docs.gunicorn.org/en/stable/deploy.html) |
| **Thứ 6 (23/10/2026)** | - Khởi tạo Target Group `huylam-ocr-tg` (`arn:aws:...:targetgroup/huylam-ocr-tg/9840edd6b7d65bf3`) cổng 5000 thuộc `huylam-vpc`.<br>- Đăng ký máy chủ `i-0566e1eedaacea52d` vào Target Group.<br>- Khởi tạo Application Load Balancer `huylam-ocr-alb` công khai Internet, đính kèm 2 Subnet Multi-AZ (`ap-southeast-1a` và `ap-southeast-1b`) và Security Group `huylam-alb-sg`.<br>- Cấu hình Listener HTTP:80 chuyển tiếp (Forward) lưu lượng vào Target Group `huylam-ocr-tg`. | Application Load Balancer chuyển sang trạng thái **Active**, cấp phát tên miền DNS công khai hoạt động trên toàn cầu. | [Application Load Balancers Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) |
| **Thứ 7 (24/10/2026)** | - Tinh chỉnh Health Check Path của Target Group sang `/login` để khớp với mã phản hồi HTTP 200 OK của ứng dụng.<br>- Target Group ghi nhận trạng thái kiểm tra sức khỏe máy chủ đạt **Healthy (1/1)**.<br>- Thử nghiệm truy cập trực tiếp từ trình duyệt Safari qua Public DNS URL của ALB.<br>- Giao diện đăng nhập Web Studio hiển thị mượt mà, phản hồi tức thì với tốc độ cao. | Hoàn thành mục tiêu triển khai hệ thống thật ra ngoài Internet, sẵn sàng cho người dùng truy cập và trải nghiệm. | [Target Groups Health Checks](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html) |
| **Chủ Nhật**| - Thu thập toàn bộ 10 ảnh chụp màn hình minh chứng các bước triển khai trên AWS Console và Web Studio.<br>- Xử lý tự động đóng khung viền đỏ chuẩn xác bao quanh Account Badge `huylam (677994024390)` và các thông số kỹ thuật cốt lõi.<br>- Biên soạn tài liệu Worklog Tuần 12 song ngữ trên website Hugo.<br>- Cập nhật lộ trình dự án trong `ROADMAP.md` và đồng bộ mã nguồn lên GitHub. | Hoàn thành xuất sắc toàn bộ mục tiêu triển khai hạ tầng đám mây doanh nghiệp Tuần 12. | [AWS Free Tier Guidelines](https://aws.amazon.com/free/) |

---

### Bảng thông số hạ tầng triển khai thực tế (Infrastructure Specifications):

| Thành phần tài nguyên | Tên định danh (Resource Name) | Định danh duy nhất (ID / ARN) | Thông số cấu hình kỹ thuật | Trạng thái hoạt động |
| :--- | :--- | :--- | :--- | :--- |
| **Virtual Private Cloud** | `huylam-vpc` | `vpc-0125f4d6db3fbffa6` | CIDR `10.0.0.0/16`, gắn Internet Gateway `huylam-igw` | Hoạt động (Available) |
| **Public Subnet 1** | `huylam-subnet-public1-ap-southeast-1a` | `subnet-0efa7c3a5818035dc` | CIDR `10.0.8.0/21`, Vùng sẵn sàng `ap-southeast-1a` | Hoạt động (Available) |
| **Public Subnet 2** | `huylam-subnet-public2-ap-southeast-1b` | `subnet-0e07eb2fd44d1ac91` | CIDR `10.0.16.0/21`, Vùng sẵn sàng `ap-southeast-1b` | Hoạt động (Available) |
| **ALB Security Group** | `huylam-alb-sg` | `sg-0dca819306a96bfdb` | Inbound: HTTP 80 (`0.0.0.0/0`), Outbound: All traffic | Đang áp dụng cho ALB |
| **Web Security Group** | `huylam-web-sg` | `sg-0ff9ea20c6c3a8dc8` | Inbound: Custom TCP 5000 (Source: `huylam-alb-sg`), SSH 22 | Đang áp dụng cho EC2 |
| **IAM Instance Role** | `huylam-ssm-role` | `arn:aws:iam::677994024390:role/huylam-ssm-role` | Policies: `AmazonSSMManagedInstanceCore`, `AmazonS3FullAccess`, `AmazonDynamoDBFullAccess` | Đã đính kèm EC2 Profile |
| **Máy chủ ứng dụng EC2** | `huylam-ocr-web-server` | `i-0566e1eedaacea52d` | AMI Amazon Linux 2023, t2.micro, Private IP `10.0.8.15`, Public IP `54.254.141.192` | Đang chạy (Running, 2/2 passed) |
| **Target Group** | `huylam-ocr-tg` | `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:targetgroup/huylam-ocr-tg/9840edd6b7d65bf3` | Port 5000, Protocol HTTP1, Health check path: `/login` | Khỏe mạnh (Healthy 1/1) |
| **Application Load Balancer** | `huylam-ocr-alb` | `arn:aws:elasticloadbalancing:ap-southeast-1:677994024390:loadbalancer/app/huylam-ocr-alb/68ac5f1a04e35614` | Scheme: Internet-facing, IPv4, Multi-AZ (`1a` và `1b`), Listener HTTP:80 | Đang hoạt động (Active) |
| **Public DNS URL** | Đường link truy cập thật | `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com` | Phân giải tự động lưu lượng Internet về máy chủ Web Studio | Trực tiếp trên Internet (LIVE) |

---

### Phân tích kiến trúc an ninh 3 tầng (Three-Tier Enterprise Cloud Architecture):

Kiến trúc triển khai thực tế trên AWS được thiết kế dựa trên các tiêu chuẩn an toàn cao nhất của AWS Well-Architected Framework:

1. **Tầng phân phối và cân bằng tải (Ingress Layer)**:
   * Application Load Balancer `huylam-ocr-alb` đóng vai trò cổng tiếp nhận tập trung (Single Point of Entry) từ Internet.
   * Được bảo vệ bởi Security Group `huylam-alb-sg`, chỉ chấp nhận lưu lượng HTTP trên cổng tiêu chuẩn 80.
   * Cơ chế Multi-AZ bảo đảm khi một Trung tâm dữ liệu (Availability Zone) gặp sự cố, lưu lượng truy cập lập tức được điều hướng mượt mà sang vùng sẵn sàng còn lại.

2. **Tầng ứng dụng và xử lý logic (Application Tier)**:
   * Máy chủ EC2 `huylam-ocr-web-server` chạy dịch vụ Web Studio được bảo vệ nghiêm ngặt bên trong Security Group `huylam-web-sg`.
   * **Nguyên tắc phân tầng an ninh (Least Privilege Security Chaining)**: Cổng ứng dụng 5000 không mở ra Internet, mà chỉ chấp nhận các gói tin bắt nguồn từ mã định danh Security Group của ALB (`sg-0dca819306a96bfdb`). Do đó, bất kỳ nỗ lực truy cập trực tiếp từ Internet vào cổng 5000 của máy chủ đều bị tường lửa cấp hạt nhân của AWS chặn đứng.
   * **Quản trị an toàn qua AWS Systems Manager**: Quản trị viên kết nối trực tiếp vào máy chủ qua Session Manager mà không cần mở cổng SSH 22 ra ngoài Internet và không cần quản lý SSH Key pair cục bộ.

3. **Tầng dữ liệu và dịch vụ lưu trữ đám mây (Data & Cloud Storage Tier)**:
   * Máy chủ EC2 được cấp quyền thông qua IAM Instance Profile `huylam-ssm-role`. Khi ứng dụng thực hiện các tác vụ lưu tệp tải lên vào Amazon S3 (`huylam-ocr-documents-ap-southeast-1`) hoặc ghi trạng thái tiến trình vào Amazon DynamoDB (`document_processing_jobs`), AWS SDK Boto3 tự động lấy thông tin xác thực tạm thời từ AWS STS (Security Token Service), loại bỏ hoàn toàn nguy cơ rò rỉ Access Key / Secret Key mã hóa cứng trong mã nguồn.

---

### Minh chứng thực tế có khung viền đỏ kiểm tra trên AWS Console & Web Studio:

> [!IMPORTANT]
> Toàn bộ 10 ảnh chụp màn hình minh chứng đều được xử lý gắn khung viền đỏ nổi bật bao quanh **AWS Account Badge `huylam (677994024390)`**, tên dịch vụ, định danh tài nguyên, trạng thái hoạt động và đường link công khai thật của hệ thống.

#### 1. Khởi tạo Security Group huylam-alb-sg cho Application Load Balancer:
Security Group dành cho ALB được cấu hình mở cổng HTTP 80 cho phép toàn bộ người dùng từ Internet (`0.0.0.0/0`) truy cập:
![Khởi tạo Security Group cho ALB](/images/week12/01-alb-security-group-created.png)

---

#### 2. Cấu hình Security Group huylam-web-sg bảo vệ máy chủ ứng dụng:
Security Group của máy chủ Web Studio chỉ chấp nhận kết nối cổng TCP 5000 khi lưu lượng xuất phát từ Security Group của ALB (`huylam-alb-sg`):
![Cấu hình Security Group cho Web Server](/images/week12/02-web-security-group-created.png)

---

#### 3. Đính kèm quyền IAM Role huylam-ssm-role cho máy chủ EC2:
IAM Role được gán đầy đủ các quyền quản trị Systems Manager (`AmazonSSMManagedInstanceCore`), lưu trữ đối tượng S3 (`AmazonS3FullAccess`) và cơ sở dữ liệu (`AmazonDynamoDBFullAccess`):
![Đính kèm quyền IAM Role cho EC2](/images/week12/03-iam-role-ssm-s3-dynamodb.png)

---

#### 4. Khởi chạy máy chủ EC2 huylam-ocr-web-server thành công:
Máy chủ ảo Amazon EC2 (`i-0566e1eedaacea52d`) khởi chạy thành công trên nền Amazon Linux 2023 tại khu vực ap-southeast-1a:
![Khởi chạy máy chủ EC2 thành công](/images/week12/04-ec2-launch-instance-success.png)

---

#### 5. Khởi tạo Target Group huylam-ocr-tg trên cổng 5000:
Target Group thuộc VPC `huylam-vpc` được thiết lập trên cổng 5000 và đã đăng ký máy chủ ứng dụng `i-0566e1eedaacea52d`:
![Khởi tạo Target Group thành công](/images/week12/05-target-group-created.png)

---

#### 6. Application Load Balancer huylam-ocr-alb đạt trạng thái Active:
Application Load Balancer công khai Internet được tạo thành công, cấu hình Multi-AZ trên cả 2 Subnet và cung cấp tên miền DNS công khai:
![Application Load Balancer trạng thái Active](/images/week12/06-alb-created-active.png)

---

#### 7. Triển khai mã nguồn và kích hoạt dịch vụ qua SSM Session Manager:
Quản trị viên kết nối an toàn qua AWS Systems Manager Session Manager, triển khai mã nguồn từ GitHub và kích hoạt systemd service `huylam-ocr.service`:
![Triển khai dịch vụ qua SSM Session Manager](/images/week12/07-ssm-session-manager-deployment.png)

---

#### 8. Target Group xác thực máy chủ đạt trạng thái Healthy:
Sau khi tinh chỉnh đường dẫn kiểm tra sức khỏe thành `/login`, Target Group ghi nhận máy chủ phản hồi hoàn hảo và chuyển sang trạng thái **Healthy (1/1)**:
![Target Group đạt trạng thái Healthy](/images/week12/08-target-group-healthy-status.png)

---

#### 9. Trình duyệt truy cập cổng đăng nhập qua Public DNS URL của ALB:
Truy cập thực tế từ trình duyệt Safari qua đường link thật `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`, hệ thống tự động định tuyến đến trang đăng nhập Web Studio:
![Trình duyệt truy cập qua URL ALB](/images/week12/09-browser-alb-public-dns-login.png)

---

#### 10. Hệ thống Web Studio vận hành ổn định trên nền tảng AWS:
Giao diện ứng dụng bóc tách tài liệu và dịch thuật kỹ thuật trực tuyến hoạt động trơn tru qua đường link công khai, sẵn sàng phục vụ người dùng cuối:
![Web Studio vận hành trực tiếp trên AWS](/images/week12/10-browser-alb-studio-live.png)

---

#### 11. Khởi chạy máy chủ thị giác OCR trên Kaggle GPU (Qwen2.5-VL-7B):
Khởi chạy tiến trình nhận diện hình ảnh chuyên sâu trên máy ảo GPU T4 x 2 của Kaggle, mở đường hầm Cloudflare Tunnel công khai HTTPS kết nối ra ngoài Internet:
![Màn hình thực thi Notebook trên Kaggle và URL Cloudflare Tunnel](/images/week12/11-kaggle-gpu-notebook-run.png)

---

#### 12. Quản trị viên tích hợp Endpoint Kaggle vào Web Studio:
Đăng nhập vào Bảng điều khiển Quản trị (`/admin`), nạp URL Cloudflare Tunnel vào cụm khóa API Pool và xác nhận trạng thái **ACTIVE (Connected)** với chi phí 0.00 USD:
![Màn hình quản trị Web Studio kết nối thành công Kaggle GPU OCR](/images/week12/12-web-studio-admin-gpu-connected.png)

---

### Tổng kết Tuần 12 & Quản trị tài chính FinOps:
* Đã triển khai thành công 100% mô hình kiến trúc mạng 3 tầng (Three-Tier Enterprise Cloud Architecture) trên AWS với Application Load Balancer (ALB) và máy chủ EC2 Web Studio.
* Áp dụng triệt để nguyên tắc an ninh tối thiểu (Principle of Least Privilege) và chuỗi liên kết Security Groups, cô lập an toàn máy chủ ứng dụng khỏi Internet.
* Hệ thống được cấp phát đường link thật hoạt động trực tiếp trên toàn cầu qua ALB Public DNS: `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`.
* Quản trị viên quản lý máy chủ từ xa an toàn qua AWS Systems Manager Session Manager mà không cần duy trì SSH Key hay mở cổng 22.
* **Kế hoạch FinOps Teardown**: Để bảo toàn ngân sách 0.00 USD trong suốt kỳ thực tập, Application Load Balancer (có mức phí duy trì ~0.0225 USD/giờ) và máy chủ EC2 sẽ được ghi nhận hình ảnh minh chứng đầy đủ phục vụ bảo vệ đồ án, sau đó người dùng có thể chủ động xóa ALB và dừng máy chủ EC2 khi hoàn tất phiên nghiệm thu để đảm bảo không phát sinh chi phí ngoài ý muốn.