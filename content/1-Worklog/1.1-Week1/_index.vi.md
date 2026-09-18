---
title: "Worklog Tuần 1"
date: 2026-09-18
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu tuần 1:
* Đăng ký và thiết lập tài khoản AWS cá nhân mới (Free Tier 12 tháng).
* Thiết lập an toàn bảo mật tài khoản: Kích hoạt xác thực 2 bước (MFA) cho tài khoản Root.
* Quản lý phân quyền và kiểm soát truy cập với AWS IAM: Tạo IAM User `dev_admin` và Group quản trị.
* Thiết lập công cụ kiểm soát chi phí tự động với AWS Budgets để phòng ngừa rủi ro phát sinh phí.
* Cài đặt và cấu hình AWS CLI v2 trên môi trường macOS cục bộ.
* Thiết lập hệ sinh thái lưu trữ tri thức bộ não ngoại vi (Obsidian / Knowledge Brain) và khởi tạo Website Báo cáo thực tập trên GitHub Pages.

### Các công việc đã triển khai trong tuần 1:

| Thứ | Công việc | Kết quả đạt được | Nguồn tài liệu |
| :--- | :--- | :--- | :--- |
| **Thứ 2** | - Tìm hiểu nội quy thực tập FCAJ Bootcamp 2026.<br>- Nghiên cứu khung chương trình The First Cloud Journey (FCJ). | Nắm rõ nội quy tại hn-rules.awsfcaj.com và tiêu chuẩn tốt nghiệp. | https://cloudjourney.awsstudygroup.com |
| **Thứ 3** | - Đăng ký tài khoản AWS cá nhân.<br>- Cấu hình MFA cho tài khoản Root (Lab 000001). | Kích hoạt thành công Virtual MFA trên điện thoại; Root Account được bảo vệ tuyệt đối. | https://000001.awsstudygroup.com |
| **Thứ 4** | - Cấu hình AWS Budgets khóa chi phí (Lab 000007). | Tạo thành công 2 ngân sách (100 USD và 200 USD) trạng thái HEALTHY. | https://000007.awsstudygroup.com |
| **Thứ 5** | - Cấu hình phân quyền IAM (Lab 000002). | Tạo User `dev_admin`, gán quyền AdministratorAccess, tạo Access Key CLI. | https://000002.awsstudygroup.com |
| **Thứ 6** | - Cài đặt AWS CLI v2 trên máy macOS (Lab 000011).<br>- Cấu hình profile kết nối bằng lệnh `aws configure`. | AWS CLI v2.36.48 hoạt động ổn định; xác thực thành công qua `aws sts get-caller-identity`. | https://000011.awsstudygroup.com |
| **Thứ 7** | - Thiết lập Hugo Learn Theme và deploy website Báo cáo thực tập lên GitHub Pages.<br>- Soạn thảo đề xuất dự án Capstone: Enterprise Agentic RAG Platform on AWS. | Website báo cáo thực tập hoạt động trực tuyến song ngữ tại lamhuy0489.github.io/workshop. | https://github.com/AWS-First-Cloud-Journey/Workshop-template |

### Chi tiết các thông số kỹ thuật đã xác thực trên AWS:
- **AWS Account ID**: `677994024390`
- **IAM User**: `dev_admin` (`arn:aws:iam::677994024390:user/dev_admin`)
- **Default Region**: `ap-southeast-1` (Singapore)
- **Root Account MFA**: Đã kích hoạt (`MFA: 1`)
- **AWS Budgets**: `My Monthly Cost Budget` (100 USD) và `My-200$-budget` (200 USD) đang hoạt động.