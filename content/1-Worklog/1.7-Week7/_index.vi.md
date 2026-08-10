---
title: "Nhật ký Tuần 7"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu Tuần 7

- Phân tích và xác định các yêu cầu chức năng của hệ thống CloudMenu.
- Thiết kế quy trình gọi món và quản lý đơn hàng cho các nhóm người dùng.
- Nắm được kiến trúc Serverless và luồng xử lý giữa các thành phần hệ thống.
- Tìm hiểu vai trò của các dịch vụ AWS được sử dụng trong CloudMenu.

**Thời gian:** 03/08/2025 - 07/08/2025

---

### Tổng quan Nhiệm vụ Tuần

| Ngày | Hoạt động                                                                                                                                                                                                                                                                                     | Ngày bắt đầu | Ngày kết thúc | Tài liệu tham khảo                 |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ------------- | ---------------------------------- |
| 1    | - Bắt đầu viết Proposal cho đồ án CloudMenu. <br> + Hoàn thiện Executive Summary và Problem Statement về hệ thống gọi món trực tuyến tại bàn. <br> + Mô tả kiến trúc tổng quan của hệ thống (Frontend – Backend – Database – AWS Services). <br> + Nghiên cứu các dịch vụ AWS dự kiến sử dụng như S3, CloudFront, API Gateway, Lambda, DynamoDB, ElastiCache,… | 03/08/2026   | 03/08/2026    | -                   |
| 2    | - Phân tích yêu cầu hệ thống CloudMenu <br> + Phân tích các chức năng chính của CloudMenu. <br> + Xác định yêu cầu cho ba nhóm người dùng: Khách hàng, Nhân viên bếp và Admin/Manager. <br> + Phân tích quy trình gọi món từ quét QR, chọn món, gửi đơn đến theo dõi trạng thái đơn hàng. | 04/08/2026   | 04/08/2026    | -                   |
| 3    | - Thiết kế chức năng và quy trình xử lý <br> + Thiết kế quy trình quản lý Menu, Cart và Order. <br> + Xây dựng quy trình xử lý đơn hàng và cập nhật trạng thái: Đã gửi đơn → Đang chế biến → Đã hoàn thành. <br> + Xác định các thông tin cần lưu trữ và hiển thị như mã đơn, số bàn, món ăn, tổng tiền và thời gian đặt món. | 05/08/2026   | 05/08/2026    | -                   |
| 4    | - Thiết kế kiến trúc hệ thống <br> + Thiết kế kiến trúc Serverless cho CloudMenu. <br> + Phân tích luồng dữ liệu giữa Frontend, API Gateway, Lambda và DynamoDB. <br> + Thiết kế Dashboard thống kê cho Admin/Manager, bao gồm đơn hàng, doanh thu, trạng thái đơn và món được gọi nhiều nhất. | 06/08/2026   | 06/08/2026    | -                   |
| 5    | - Nghiên cứu và lựa chọn dịch vụ AWS <br> + Nghiên cứu vai trò của Amazon S3 và CloudFront trong triển khai Frontend. <br> + Tìm hiểu API Gateway, AWS Lambda và DynamoDB cho Backend Serverless. <br> + Tìm hiểu AWS IAM để quản lý quyền truy cập và bảo mật hệ thống. | 07/08/2026   | 07/08/2026    | -   |

---

### Thành tựu Tuần 7

- Xác định được các chức năng chính cho Khách hàng, Nhân viên bếp và Admin/Manager.
- Thiết kế được quy trình Menu, Cart, Order và cập nhật trạng thái đơn hàng.
- Xây dựng được kiến trúc tổng quan Serverless với S3, CloudFront, API Gateway, Lambda và DynamoDB.
- Hiểu được vai trò của IAM trong việc quản lý quyền truy cập và bảo mật hệ thống.