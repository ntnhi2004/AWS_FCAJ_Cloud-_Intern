---
title : "Frontend Hosting và xác thực người dùng"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 4.2.2 </b> "
---

## 4.2.2 Frontend Hosting và xác thực người dùng

Phần này giới thiệu **Amazon S3** và **Amazon CloudFront** cho hosting và phân phối frontend của CloudMenu. Đối với xác thực người dùng, CloudMenu ưu tiên cơ chế xác thực phù hợp với từng giao diện và có thể mở rộng sang **Amazon Cognito** khi hệ thống yêu cầu quản lý tài khoản tập trung.

### Amazon S3

**Vai trò**: Lưu trữ các file frontend và static assets của CloudMenu, bao gồm các giao diện Customer, Kitchen và Dashboard. S3 đóng vai trò là storage layer cho frontend và cung cấp origin cho CloudFront.

Frontend được triển khai lên S3 thông qua quy trình deployment tự động hoặc Terraform. Các file sau khi được upload sẽ được CloudFront phân phối đến người dùng thay vì yêu cầu client truy cập trực tiếp vào S3.

**Lý do chọn**: Amazon S3 cung cấp khả năng lưu trữ có độ bền cao, chi phí thấp và không cần quản lý server. Đây là lựa chọn phù hợp với frontend dạng static của CloudMenu, đặc biệt trong giai đoạn thử nghiệm khi hệ thống cần tối ưu chi phí.

### Amazon CloudFront

**Vai trò**: Phân phối frontend và static assets từ S3 đến người dùng thông qua mạng CDN của AWS. CloudFront đóng vai trò là lớp phân phối phía trước S3, giúp giảm latency và cải thiện tốc độ tải trang cho khách hàng, nhân viên bếp và quản lý.

CloudFront cũng có thể được cấu hình để sử dụng HTTPS và hạn chế việc truy cập trực tiếp vào S3 origin. Với Origin Access Control (OAC), bucket S3 có thể được giữ ở chế độ không public và chỉ cho phép CloudFront truy cập các object cần thiết.

Luồng hosting frontend chính:

**User → CloudFront → S3**

**Lý do chọn**: CloudFront giúp CloudMenu phân phối nội dung nhanh hơn, hỗ trợ HTTPS và giảm nhu cầu expose trực tiếp S3 ra Internet. Việc kết hợp S3 và CloudFront cũng phù hợp với kiến trúc serverless và giúp giảm chi phí vận hành so với việc duy trì một web server riêng.

### Frontend Configuration

Frontend cần biết API endpoint để gửi các request đến backend. Địa chỉ API có thể được cấu hình thông qua environment variables trong quá trình build/deployment.

Ví dụ:

**API Base URL → API Gateway endpoint**

Frontend sử dụng URL này để gọi các API liên quan đến menu, bàn, đơn hàng và trạng thái đơn.

Việc tách API endpoint khỏi source code giúp dễ dàng sử dụng các môi trường khác nhau như development, testing và production mà không cần thay đổi trực tiếp mã nguồn frontend.

### Authentication và Authorization

Trong kiến trúc CloudMenu hiện tại, các giao diện Customer, Kitchen và Dashboard có yêu cầu xác thực khác nhau. Đối với các chức năng quản trị hoặc nghiệp vụ nội bộ, quyền truy cập cần được kiểm soát ở cả frontend và backend.

Nếu CloudMenu cần triển khai hệ thống tài khoản tập trung, Amazon Cognito có thể được tích hợp để cung cấp:

- User Pool cho việc đăng ký và đăng nhập.
- App Client để kết nối frontend với Cognito.
- JWT tokens để xác thực các API request.
- Phân quyền người dùng dựa trên role hoặc group.
- Tích hợp với API Gateway/Lambda để kiểm tra quyền truy cập.

**Lý do chọn**: Amazon Cognito là dịch vụ authentication managed của AWS, giúp giảm nhu cầu tự xây dựng và bảo trì hệ thống đăng nhập. Cognito hỗ trợ JWT và có thể tích hợp với API Gateway, Lambda và frontend của CloudMenu.

Trong giai đoạn thử nghiệm, nếu CloudMenu chưa yêu cầu hệ thống tài khoản phức tạp, Cognito có thể được triển khai ở giai đoạn sau thay vì trở thành dependency bắt buộc của toàn bộ hệ thống.