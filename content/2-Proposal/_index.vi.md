---
title: "Bản đề xuất"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Đề xuất triển khai CloudMenu trên AWS

## 1. Tổng quan dự án

CloudMenu là hệ thống gọi món trực tuyến tại bàn, cho phép khách hàng sử dụng điện thoại quét QR Code được gán cho từng bàn để truy cập thực đơn, lựa chọn món và gửi đơn trực tiếp đến khu vực bếp.

Hệ thống gồm ba nhóm người dùng chính:

- Khách hàng: xem menu, tìm kiếm/lọc món, quản lý giỏ hàng, đặt món và theo dõi trạng thái đơn hàng.
- Nhân viên bếp: tiếp nhận đơn, xem thông tin đơn hàng và cập nhật trạng thái chế biến.
- Admin/Manager: theo dõi Dashboard thống kê về số lượng đơn, doanh thu, trạng thái đơn hàng, doanh thu theo bàn và các món được gọi nhiều nhất.

CloudMenu được đề xuất triển khai theo mô hình Serverless trên AWS, nhằm giảm chi phí vận hành, dễ mở rộng và phù hợp với đặc điểm lưu lượng truy cập không cố định của hệ thống.

## 2. Vấn đề và giải pháp

### Vấn đề hiện tại

Mô hình gọi món truyền thống có thể phát sinh một số hạn chế:

- Khách hàng phải chờ nhân viên đến nhận order.
- Dễ xảy ra sai sót khi ghi nhận món và số lượng.
- Nhân viên bếp khó theo dõi và cập nhật trạng thái đơn hàng theo thời gian thực.
- Quản lý khó tổng hợp doanh thu và thống kê tình hình kinh doanh.
- Hệ thống cần có khả năng đáp ứng lượng truy cập tăng cao vào các khung giờ cao điểm.

### Giải pháp

CloudMenu sử dụng QR Code cho từng bàn để khách hàng trực tiếp truy cập menu và gửi đơn. Các yêu cầu được xử lý thông qua kiến trúc Serverless:

QR Code → Frontend → API Gateway → Lambda → DynamoDB

Giải pháp giúp:

- Tự động xác định số bàn thông qua QR Code.
- Giảm thao tác thủ công và hạn chế sai sót khi đặt món.
- Tập trung dữ liệu đơn hàng trên DynamoDB.
- Tự động mở rộng khả năng xử lý thông qua Lambda.
- Cung cấp Dashboard giúp quản lý theo dõi hoạt động kinh doanh.

## 3. Kiến trúc giải pháp

![Sơ đồ kiến trúc AWS CloudMenu](/images/AWS_CloudMenu.png)

Kiến trúc đề xuất gồm các thành phần chính:

Frontend
- Amazon S3: lưu trữ các file HTML, CSS, JavaScript của CloudMenu.
- Amazon CloudFront: phân phối nội dung frontend thông qua mạng CDN, giúp giảm độ trễ truy cập.

Backend
- Amazon API Gateway: cung cấp RESTful API và tiếp nhận request từ frontend.
- AWS Lambda: xử lý nghiệp vụ như lấy menu, tạo đơn hàng, cập nhật trạng thái và truy vấn thống kê.
- Amazon DynamoDB: lưu trữ dữ liệu menu, đơn hàng, bàn và trạng thái đơn hàng.

Security: AWS IAM: quản lý quyền truy cập giữa các AWS Services và kiểm soát quyền quản trị hệ thống.

## 4. Timeline (8 tuần)

- **Tuần 1–2 — Phân tích & nền tảng AWS**
  - **Tuần 1:** Thống nhất yêu cầu chức năng và NFR của CloudMenu; phân tích các nhóm người dùng và quy trình QR → Menu → Order → Kitchen → Dashboard.
  - **Tuần 2:** Hoàn thiện thiết kế kiến trúc Serverless; thiết lập môi trường AWS và phân quyền IAM; xác định vai trò của S3, CloudFront, API Gateway, Lambda và DynamoDB.

- **Tuần 3–4 — Triển khai Frontend & Backend Serverless**
  - **Tuần 3:** Triển khai Frontend lên Amazon S3 và phân phối qua CloudFront; cấu hình domain và kiểm tra khả năng truy cập.
  - **Tuần 4:** Xây dựng và triển khai API Gateway + AWS Lambda; triển khai các API chính cho menu, giỏ hàng và đơn hàng; kết nối Lambda với DynamoDB.

- **Tuần 5–6 — Dữ liệu, bảo mật & chức năng nghiệp vụ**
  - **Tuần 5:** Thiết kế và hoàn thiện cấu trúc dữ liệu DynamoDB; triển khai các chức năng quản lý đơn hàng, cập nhật trạng thái và Dashboard thống kê.
  - **Tuần 6:** Rà soát IAM, API access và security; tối ưu quyền theo nguyên tắc least privilege; kiểm tra luồng xử lý giữa API Gateway, Lambda và DynamoDB.

- **Tuần 7–8 — Kiểm thử & hoàn thiện**
  - **Tuần 7:** Kiểm thử end-to-end các luồng Customer, Kitchen Staff và Admin/Manager; sử dụng CloudWatch để theo dõi log và lỗi Lambda/API.
  - **Tuần 8:** Đánh giá hiệu năng, khả năng mở rộng và chi phí; hoàn thiện tài liệu kiến trúc, hướng dẫn triển khai và báo cáo CloudMenu trên AWS.
  
## 5. Ngân sách

CloudMenu được định hướng sử dụng các dịch vụ AWS Serverless và Free Tier trong giai đoạn thử nghiệm nhằm giảm chi phí triển khai.

Các dịch vụ chính cần xem xét chi phí:

| Dịch vụ AWS | Thành phần / Sử dụng | Chi phí (USD/tháng) |
|---|---|---:|
| Amazon S3 | Frontend hosting + static assets | $0 - $3 |
| Amazon CloudFront | CDN + data transfer | $2 - $15 |
| Amazon API Gateway | REST API + API requests | $0 - $10 |
| AWS Lambda | Backend functions + invocations | $0 - $8 |
| Amazon DynamoDB | PMenu + Orders + Table data | $0 - $10 |
| AWS IAM | Users / Roles / Policies | Không phí AWS trực tiếp |
| **TỔNG CHI PHÍ AWS** |  | **$2 - $50** |

Đề xuất kiểm soát chi phí:
- Tận dụng AWS Free Tier trong giai đoạn phát triển và thử nghiệm.
- Thiết lập AWS Budgets để cảnh báo khi chi phí vượt mức ngân sách đặt ra.
- Tối ưu dung lượng S3 và sử dụng Lifecycle Policy khi dữ liệu tăng.
- Kiểm tra và xóa các tài nguyên thử nghiệm không còn sử dụng.
- Ưu tiên kiến trúc Serverless để tránh chi phí máy chủ chạy liên tục.

## 6. Rủi ro

- Chi phí AWS tăng ngoài dự kiến
  *Giảm thiểu*: Thiết lập Budget và theo dõi Cost Management

- Lượng truy cập tăng đột biến
  *Giảm thiểu*: Sử dụng kiến trúc Serverless có khả năng tự động mở rộng

- Truy cập trái phép API.
  *Giảm thiểu*: Sử dụng IAM và các cơ chế xác thực/phân quyền phù hợp

- Mất hoặc xóa nhầm dữ liệu.
  *Giảm thiểu*: Thiết lập Backup/Point-in-Time Recovery cho DynamoDB

- QR Code bị sử dụng sai bàn
  *Giảm thiểu*: Gắn định danh bàn vào từng QR Code và kiểm tra thông tin bàn khi tạo order

- Thao tác đặt món bị gửi nhiều lần
  *Giảm thiểu*: Thiết kế cơ chế kiểm tra và xử lý duplicate request