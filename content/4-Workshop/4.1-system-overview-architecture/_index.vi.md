---
title : "Tổng quan và kiến trúc hệ thống"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---

## 4.1 Tổng quan và kiến trúc hệ thống

### Tổng quan

CloudMenu là hệ thống gọi món điện tử sử dụng mã QR theo từng bàn, cho phép khách hàng truy cập menu và thực hiện gọi món trực tiếp bằng điện thoại mà không cần sử dụng menu giấy hoặc thiết bị gọi món chuyên dụng.

Mỗi bàn trong nhà hàng được gán một mã QR riêng. Khi khách hàng quét mã QR, hệ thống xác định thông tin bàn và mở đúng menu tương ứng. Khách hàng có thể xem danh sách món ăn, lựa chọn món, gửi yêu cầu gọi món và theo dõi trạng thái đơn hàng.

Hệ thống được thiết kế với ba giao diện chính:

- Customer Interface: Giao diện dành cho khách hàng để xem menu, lựa chọn món và gửi đơn hàng.
- Kitchen Interface: Giao diện dành cho nhân viên bếp để tiếp nhận, xử lý và cập nhật trạng thái các đơn hàng.
- Dashboard: Giao diện dành cho quản lý để theo dõi đơn hàng, doanh thu và các thống kê hoạt động của hệ thống.

### Kiến trúc

![Sơ đồ kiến trúc AWS CloudMenu](/images/AWS_CloudMenu.png)

Trong kiến trúc này, IAM đóng vai trò kiểm soát quyền truy cập của Lambda đến DynamoDB và các tài nguyên AWS cần thiết.

Kiến trúc trên giúp CloudMenu duy trì backend ở dạng serverless, giảm nhu cầu quản lý máy chủ và phù hợp với quy mô hiện tại của hệ thống. Khi hệ thống phát triển, có thể mở rộng thêm các thành phần như Amazon Cognito cho authentication/authorization, AWS WAF để tăng cường bảo mật API, CI/CD để tự động hóa quá trình triển khai frontend/backend và các cơ chế monitoring nâng cao.