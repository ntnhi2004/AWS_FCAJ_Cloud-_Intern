---
title : "Backend và nền tảng xử lý"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 4.2.3 </b> "
---

## 4.2.3 Backend và nền tảng xử lý

Phần này trình bày kiến trúc backend serverless của CloudMenu, tập trung vào luồng xử lý API từ frontend đến cơ sở dữ liệu. Khác với kiến trúc backend truyền thống sử dụng EC2 hoặc container, CloudMenu sử dụng các dịch vụ AWS managed/serverless gồm Amazon API Gateway, AWS Lambda và Amazon DynamoDB, giúp giảm nhu cầu quản lý máy chủ và phù hợp với quy mô thử nghiệm của hệ thống.

Kiến trúc CloudMenu hiện tại có thể được chia thành hai luồng chính:

- Luồng triển khai và phân phối frontend:
Developer → GitHub → Amazon S3 → Amazon CloudFront → Browser/Phone.
- Luồng xử lý dữ liệu:
Customer/Kitchen/Manager → Frontend → Amazon API Gateway → AWS Lambda → Amazon DynamoDB.

### Luồng triển khai và phân phối frontend

Frontend của CloudMenu hiện có ba giao diện chính:

- Customer: khách hàng quét QR của bàn để truy cập menu và gửi đơn.
- Kitchen: nhân viên bếp theo dõi và cập nhật trạng thái đơn hàng.
- Manager: quản lý theo dõi dữ liệu và thống kê đơn hàng.

Các file frontend được lưu trữ trên Amazon S3 và phân phối đến người dùng thông qua Amazon CloudFront.

Trong giai đoạn hiện tại, GitHub → S3 chưa được tự động hóa bằng CI/CD. Developer vẫn thực hiện việc build và upload các file frontend lên S3 theo cách thủ công. GitHub trong sơ đồ thể hiện nguồn lưu trữ source code và luồng triển khai ở mức khái quát, không phải một pipeline deployment tự động.

Sau khi frontend được upload lên S3, Amazon CloudFront đóng vai trò CDN và phân phối các file frontend đến người dùng. Nhờ đó, người dùng có thể truy cập CloudMenu thông qua CloudFront URL mà không cần chạy frontend trực tiếp trên máy phát triển hoặc mở VS Code.

Lý do lựa chọn S3 + CloudFront:

- Amazon S3 phù hợp để lưu trữ các file frontend và static assets.
- CloudFront giúp phân phối nội dung đến người dùng qua mạng CDN.
- Không cần duy trì web server hoặc EC2 riêng cho frontend.
- Phù hợp với kiến trúc serverless và mục tiêu tối ưu chi phí của CloudMenu.
- Có thể mở rộng sang custom domain và HTTPS configuration trong các giai đoạn tiếp theo.

### Amazon API Gateway

Vai trò: Amazon API Gateway là điểm tiếp nhận các HTTP request từ frontend CloudMenu. Khi người dùng thực hiện một thao tác cần truy cập hoặc thay đổi dữ liệu, frontend gửi request đến API Gateway.

Một số nghiệp vụ API chính hiện tại gồm:

- Create Order: tạo đơn hàng mới khi Customer đặt món.
- Get Orders: lấy danh sách hoặc thông tin đơn hàng cho Customer, Kitchen hoặc Manager.
- Update Order Status: cập nhật trạng thái đơn hàng khi Kitchen xử lý đơn.

API Gateway sau đó định tuyến request đến AWS Lambda tương ứng.

Lý do lựa chọn:

- Không cần triển khai và quản lý API server riêng.
- Tích hợp trực tiếp với AWS Lambda.
- Có khả năng xử lý request theo nhu cầu thực tế.
- Phù hợp với backend có các API nghiệp vụ tương đối độc lập như CloudMenu.
- Giảm chi phí vận hành trong giai đoạn development/testing.

### AWS Lambda

Vai trò: AWS Lambda thực hiện logic nghiệp vụ backend của CloudMenu. Mỗi request từ API Gateway được chuyển đến Lambda function tương ứng để xử lý.

Lý do lựa chọn:

- Không cần quản lý server hoặc container.
- Tự động xử lý theo số lượng request.
- Phù hợp với các API có workload không liên tục.
- Có thể tách từng nghiệp vụ thành các function riêng để dễ phát triển và bảo trì.
- Kết hợp tốt với API Gateway và DynamoDB trong mô hình serverless.

### Amazon DynamoDB

**Vai trò:** Amazon DynamoDB là cơ sở dữ liệu chính của CloudMenu trong kiến trúc hiện tại.

Table được sử dụng là: CloudMenuOrders

Dữ liệu đơn hàng bao gồm các thông tin chính:

| Thuộc tính | Mô tả |
| :--- | :--- |
| **Order ID** | Mã định danh của đơn hàng |
| **Table Number** | Số bàn tạo đơn, ví dụ bàn 03 |
| **Items** | Danh sách món ăn trong đơn |
| **Total Amount** | Tổng tiền của đơn |
| **Status** | Trạng thái đơn hàng |
| **Created At** | Thời điểm tạo đơn |
| **Updated At** | Thời điểm cập nhật đơn |

Khi Customer tạo đơn, Lambda ghi dữ liệu vào CloudMenuOrders. Khi Kitchen hoặc Manager cần xem dữ liệu, Lambda đọc dữ liệu từ DynamoDB và trả kết quả thông qua API Gateway.

**Lý do lựa chọn:**

- Là dịch vụ database managed/serverless, không cần quản lý database server.
- Có khả năng mở rộng theo workload.
- Tích hợp trực tiếp với AWS Lambda.
- Phù hợp với dữ liệu đơn hàng có cấu trúc tương đối đơn giản.
- Phù hợp với mục tiêu tối ưu chi phí của CloudMenu trong giai đoạn thử nghiệm.

### AWS IAM

**Vai trò:** AWS IAM được sử dụng để kiểm soát quyền truy cập giữa Lambda và các dịch vụ AWS khác.

Lambda không sử dụng quyền truy cập AWS một cách tùy ý mà được cấp IAM Role với các policy cần thiết. Trong CloudMenu, quyền quan trọng là cho phép Lambda thực hiện các thao tác cần thiết trên bảng DynamoDB CloudMenuOrders.

**Lý do lựa chọn:**

- Kiểm soát quyền truy cập giữa các AWS services.
- Áp dụng nguyên tắc Least Privilege.
- Không cần lưu AWS Access Key trực tiếp trong source code của Lambda.
- Giảm nguy cơ truy cập trái phép vào tài nguyên AWS.

### Tổng quan luồng xử lý dữ liệu

Toàn bộ luồng xử lý chính của CloudMenu có thể được mô tả như sau:

![Luồng xử lý chính của CloudMenu](/images/AWS_CloudMenu_data.png)

