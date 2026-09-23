---
title: "Cấu hình Quản trị viên và Kết nối Kaggle GPU OCR"
date: 2026-09-23
weight: 3
chapter: false
pre: " <b> 5.7.3. </b> "
---

### Mục tiêu thực hành

Cấu hình tài khoản Quản trị viên (Admin), khởi chạy máy chủ thị giác OCR chuyên sâu mô hình **Qwen2.5-VL-7B-Instruct** trên môi trường máy ảo GPU miễn phí của Kaggle, mở kết nối Internet công khai qua **Cloudflare Tunnel** và tích hợp Endpoint vào Web Studio để vận hành Tầng 2 (Selective Vision OCR) với chi phí 0.00 USD.

---

## 1. Vai trò của Kaggle GPU OCR trong kiến trúc bóc tách lai

Trong nền tảng **Serverless Hybrid Document OCR, Parsing & Technical Translation Platform**:
* **Tầng 1 (Fast-Path Native)**: Trích xuất trực tiếp văn bản số và bảng biểu trong 0.1s - 0.3s/trang trên máy chủ EC2 bằng PyMuPDF với chi phí 0.00 USD.
* **Tầng 2 (Selective Vision OCR)**: Khi gặp các trang là ảnh chụp scan, biểu mẫu vẽ tay hoặc tài liệu phức tạp, hệ thống tự động phân loại và chuyển tiếp trang đó sang máy chủ Kaggle GPU để nhận dạng bằng mô hình Vision LLM chuyên sâu.

```text
                     [ Web Studio / ALB / EC2 ]
                                 │
                 ┌───────────────┴───────────────┐
                 ▼ (Trang chữ số)                ▼ (Trang scan phức tạp)
       [ Tầng 1: Fast-Path ]           [ Tầng 2: Selective Vision OCR ]
       PyMuPDF trên EC2                Gửi qua Cloudflare Tunnel (HTTPS)
       Độ trễ: 0.1s - 0.3s                       │
       Chi phí: 0.00 USD                         ▼
                                       ┌──────────────────────────────────┐
                                       │   Kaggle GPU OCR Server          │
                                       │   Model: Qwen2.5-VL-7B-Instruct  │
                                       │   GPU: NVIDIA T4 x 2 / P100      │
                                       │   FastAPI + Cloudflare Tunnel    │
                                       └──────────────────────────────────┘
```

---

## 2. Quy trình thiết lập từng bước dành cho Admin

### Bước 2.1: Truy cập Notebook trên Kaggle
Admin có thể sử dụng trực tiếp Notebook chính thức của dự án trên Kaggle:
* **Đường link Kaggle Notebook**: [https://www.kaggle.com/code/lamhuy8904/qwen2-5-vl-ocr-server](https://www.kaggle.com/code/lamhuy8904/qwen2-5-vl-ocr-server)
* Hoặc tải tệp `src/kaggle/qwen_ocr_server.ipynb` trong mã nguồn dự án để nạp vào Kaggle.

---

### Bước 2.2: Thiết lập cấu hình GPU và Internet
Tại giao diện làm việc của Kaggle Notebook, nhìn sang bảng **Session options** ở thanh bên phải:
1. **Accelerator**: Chọn **GPU T4 x 2** (hoặc **GPU P100**).
2. **Language**: Python.
3. **Internet**: Bật **Internet on** (Bắt buộc để tải các gói thư viện và kích hoạt đường hầm Cloudflare Tunnel).

---

### Bước 2.3: Thực thi toàn bộ Notebook (Run All)
Nhấp vào nút **Run All** trên thanh công cụ phía trên (hoặc nhấn tổ hợp phím `Ctrl + F9`):
1. **Cài đặt thư viện**: Tự động cài đặt `transformers>=4.49.0`, `accelerate`, `qwen-vl-utils`, `pycloudflared`, `fastapi`, `uvicorn`.
2. **Nạp mô hình**: Nạp trọng số mô hình `Qwen2.5-VL-7B-Instruct` vào VRAM của GPU (16 GB).
3. **Khởi chạy máy chủ**: Tạo ứng dụng FastAPI nhận ảnh Base64 và dịch sang cú pháp Markdown giữ nguyên bảng biểu.
4. **Mở đường hầm Cloudflare**: Kích hoạt `pycloudflared` tạo URL công khai HTTPS ra ngoài Internet.

---

### Bước 2.4: Tìm Log và Sao chép đường link Tunnel
1. Cuộn xuống ô chạy cuối cùng (Cell 5) của Notebook.
2. Tại phần đầu ra (Output Log), tìm khối thông báo:
   ```text
   ======================================================================
   MAY CHU KAGGLE OCR DA SAN SANG HOAT DONG!
   URL Endpoint: https://random-subdomain.trycloudflare.com/ocr
   URL Health:   https://random-subdomain.trycloudflare.com/health
   ======================================================================
   Sao chep URL Endpoint tren va dan vao trang Admin / Settings!
   ```
3. Sao chép toàn bộ chuỗi đường dẫn:
   ```text
   https://random-subdomain.trycloudflare.com/ocr
   ```
   *(Kiểm tra nhanh trên trình duyệt qua link `/health` sẽ trả về `{"status": "healthy", "device": "cuda"}`).*

![Màn hình thực thi Notebook trên Kaggle và URL Cloudflare Tunnel](/images/week12/11-kaggle-gpu-notebook-run.png?width=100%&classes=border,shadow)

---

### Bước 2.5: Dán link vào Web Studio và Kích hoạt Khóa
1. Truy cập vào giao diện Web Studio:
   ```text
   http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com
   ```
2. Đăng nhập bằng tài khoản Quản trị viên (**Admin**).
3. Chọn mục **Quản Trị Hệ Thống** (`/admin`) trên thanh điều hướng.
4. Tại thẻ hướng dẫn Kaggle, nhấp nút **Nhập Endpoint Kaggle** (hoặc nút **Thêm Khóa API Mới**):
   - **Nhà cung cấp (Provider)**: Chọn `Kaggle TPU/GPU (Qwen2.5-VL / Cloudflare Tunnel)`.
   - **Tên gợi nhớ (Alias)**: `Kaggle GPU Qwen2.5-VL Slot 1`.
   - **Giá trị API Key / Endpoint URL**: Dán link URL vừa sao chép từ Kaggle.
   - **Tên mô hình (Model Name)**: `Qwen2.5-VL-7B`.
   - **Độ ưu tiên (Priority)**: Đặt là `1` (ưu tiên cao nhất để tận dụng tài nguyên GPU miễn phí).
5. Nhấp nút **Lưu Khóa API**.
6. Khóa mới xuất hiện trong bảng danh sách **Cụm Khóa Kết Nối API** với trạng thái **ACTIVE**.

![Màn hình quản trị Web Studio kết nối thành công Kaggle GPU OCR](/images/week12/12-web-studio-admin-gpu-connected.png?width=100%&classes=border,shadow)

---

## 3. Hướng dẫn sử dụng trên Giao diện Web Studio

Sau khi Admin cấu hình xong, tất cả người dùng hệ thống có thể sử dụng Kaggle GPU OCR:

1. Chuyển sang trang **Studio Bóc Tách** (`/studio`).
2. Tại bộ chọn **Mô hình bóc tách (Model)**:
   - **Auto Hybrid Engine (Khuyến nghị)**: Tự động phân luồng. Trang văn bản số bóc tách bằng PyMuPDF (0.1s), trang scan tự động gọi Kaggle GPU OCR.
   - **Kaggle TPU/GPU (Qwen2.5-VL)**: Cưỡng bức gửi toàn bộ tài liệu sang Kaggle GPU.
3. Kéo thả tệp PDF tài liệu (hợp đồng scan, hồ sơ thầu, bài báo khoa học) hoặc ảnh tài liệu vào khung tải lên.
4. Nhấn **Bắt đầu bóc tách & Dịch thuật**: Máy chủ EC2 sẽ đọc cấu hình từ cụm khóa, gửi ảnh qua Cloudflare Tunnel tới Kaggle GPU.
5. Xem kết quả đối chiếu trên màn hình Split-view và xuất bản tệp ra định dạng Word (`.docx`), Markdown (`.md`) hoặc PDF.

---

## 4. Cơ chế Chuyển đổi Dự phòng Tự động (Failover)

Hệ thống được thiết kế cơ chế tự bảo vệ và phục hồi trong mã nguồn `src/backend/parsers/ocr_dispatcher.py`:
* **Xử lý khi Kaggle hết phiên**: Nếu máy chủ Kaggle tắt hoặc phản hồi quá thời gian chờ (5 giây), hệ thống tự động phát hiện và chuyển đổi dự phòng sang **Google Gemini Flash** để tài liệu của người dùng được bóc tách hoàn chỉnh mà không bị ngắt quãng.
* **Khôi phục dịch vụ**: Khi phiên Kaggle hết hạn (thường sau 9 - 12 giờ), Admin chỉ cần bấm **Run All** lại trên Kaggle, lấy link Tunnel mới và dán đè vào mục Quản trị `/admin` trong chưa đầy 30 giây.

---

## 5. Kết quả mong đợi

Sau khi hoàn thành bài thực hành này, bạn đã:
- Khởi chạy thành công máy chủ bóc tách tài liệu AI Qwen2.5-VL trên GPU miễn phí của Kaggle.
- Mở đường hầm bảo mật Cloudflare Tunnel kết nối máy chủ Kaggle với đám mây AWS.
- Cấu hình và quản lý khóa kết nối linh hoạt trên giao diện Web Studio Admin.
- Tối ưu hóa toàn diện hiệu năng và chi phí vận hành (0.00 USD) theo tiêu chuẩn FinOps.
