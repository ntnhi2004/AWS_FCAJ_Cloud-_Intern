---
title : "Cơ sở dữ liệu"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 4.2.4 </b> "
---

## 4.2.4 Cơ sở dữ liệu

CloudMenu sử dụng Amazon DynamoDB làm cơ sở dữ liệu chính cho hệ thống. DynamoDB được lựa chọn theo mô hình serverless, kết hợp trực tiếp với AWS Lambda để lưu trữ và truy xuất dữ liệu đơn hàng.

Khác với mô hình truyền thống sử dụng RDS PostgreSQL, CloudMenu không cần triển khai database server, private subnet, Security Group cho database hoặc Bastion Host. Lambda giao tiếp trực tiếp với DynamoDB thông qua IAM Role, trong khi các hoạt động của Lambda được ghi nhận và theo dõi thông qua Amazon CloudWatch.

### Bảo mật dữ liệu

Dữ liệu trong DynamoDB được truy cập thông qua các AWS service được cấp quyền bằng IAM. CloudMenu không cung cấp quyền truy cập trực tiếp từ trình duyệt đến DynamoDB.

Do đó, frontend chỉ giao tiếp với API Gateway, trong khi quyền truy cập trực tiếp đến DynamoDB được giới hạn cho Lambda thông qua IAM.

### Định hướng mở rộng cơ sở dữ liệu

Trong giai đoạn phát triển hiện tại, cấu trúc CloudMenuOrders đáp ứng các nghiệp vụ chính của hệ thống. Khi CloudMenu mở rộng, có thể bổ sung thêm các entity/table phục vụ quản lý nhà hàng, chẳng hạn:

- Menu Items: thông tin món ăn, giá, hình ảnh và trạng thái món.
- Tables: thông tin bàn và QR code tương ứng.
- Orders: mở rộng thông tin đơn hàng và lịch sử xử lý.
- Restaurants: hỗ trợ quản lý nhiều nhà hàng nếu hệ thống phát triển theo hướng multi-tenant.
- Order History: lưu trữ lịch sử và phục vụ báo cáo thống kê.

Ngoài ra, có thể bổ sung các cơ chế như DynamoDB Streams, caching hoặc các dịch vụ phân tích AWS khi nhu cầu về thống kê và xử lý dữ liệu tăng lên.
