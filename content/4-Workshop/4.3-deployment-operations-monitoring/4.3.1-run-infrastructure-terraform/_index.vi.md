---
title: "Chạy hạ tầng với Terraform"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.3.1 </b> "
aliases:
  - /4-workshop/4.3-deployment-operations-monitoring/4.3.1-vpc-network/
---

## 4.3.1 Chạy hạ tầng với Terraform

Trang này trình bày quy trình sử dụng Terraform để triển khai và quản lý hạ tầng CloudMenu trên AWS. Quy trình bao gồm clone repository, cấu hình thông tin xác thực AWS, chuẩn bị cấu hình Terraform và thực hiện các lệnh init, validate, plan và apply.

Việc triển khai tuân theo kiến trúc serverless được trình bày tại Phần 4.2, với các dịch vụ AWS chính gồm Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, AWS IAM và Amazon CloudWatch.

### Điều kiện cần thiết

Trước khi triển khai Terraform, cần cài đặt các công cụ sau: Terraform, AWS CLI và Git.
- AWS CLI cần được cấu hình với tài khoản hoặc profile AWS có đủ quyền để tạo và quản lý các tài nguyên được CloudMenu sử dụng.

Có thể cấu hình AWS CLI bằng lệnh:
```bash
aws configure
```
Hoặc sử dụng AWS CLI profile hay các biến môi trường chứa thông tin xác thực AWS.

Sau khi cấu hình, kiểm tra tài khoản AWS hiện tại bằng:

```bash
aws sts get-caller-identity
```
Lệnh này giúp xác nhận Terraform sẽ triển khai tài nguyên vào đúng AWS account.

### Clone Repository

Clone source code của CloudMenu từ GitHub:

```bash
git clone https://github.com/NhuLuk/CloudMenu-AWS
cd CloudMenu-AWS
```
Sau đó mở project bằng IDE hoặc editor và xác định thư mục chứa cấu hình Terraform.

### Chuẩn bị

Trước khi triển khai, cần kiểm tra cấu hình Terraform và xác định các AWS resource sẽ được tạo. Hạ tầng CloudMenu được tổ chức theo các lớp chính:

| Lớp | AWS Services | Mục đích |
| :--- | :--- | :--- |
| **Frontend** | Amazon S3, Amazon CloudFront | Lưu trữ và phân phối giao diện Customer, Kitchen và Manager |
| **API** | Amazon API Gateway | Tiếp nhận và định tuyến các HTTP API request |
| **Compute** | AWS Lambda | Thực thi logic nghiệp vụ backend |
| **Data** | Amazon DynamoDB | Lưu trữ dữ liệu đơn hàng trong CloudMenuOrders |
| **Security** | AWS IAM | Kiểm soát quyền truy cập giữa các AWS service |
| **Monitoring** | Amazon CloudWatch | Ghi log và theo dõi hoạt động của Lambda |

Các giá trị phụ thuộc vào môi trường nên được cấu hình thông qua Terraform variables thay vì hard-code trực tiếp trong resource.

Thông tin nhạy cảm như AWS credentials, access key và secret key không được lưu trong source code hoặc commit lên GitHub.

### Khởi tạo Terraform

Di chuyển đến thư mục chứa cấu hình Terraform và thực hiện:
```bash
terraform init
```

Lệnh này khởi tạo Terraform working directory và tải các provider cần thiết cho quá trình triển khai.

### Kiểm tra cấu hình

Trước khi tạo tài nguyên AWS, kiểm tra tính hợp lệ của Terraform configuration:
```bash
terraform validate
```

Nếu validation thành công, Terraform configuration có cú pháp hợp lệ và không phát hiện các lỗi cấu trúc cơ bản.

### Triển khai hạ tầng

Sau khi xác nhận kế hoạch triển khai chính xác, thực hiện:
```bash
terraform apply
```

Terraform sẽ yêu cầu xác nhận trước khi bắt đầu tạo hoặc thay đổi tài nguyên.

Sau khi xác nhận, Terraform sẽ triển khai các AWS resource theo dependency giữa chúng.

### Kiểm tra Terraform Outputs

Sau khi triển khai thành công, sử dụng:
```bash
terraform output
```

Các output thực tế phụ thuộc vào cấu hình Terraform và AWS account. Một số output có thể bao gồm:

```bash
aws_region = "ap-southeast-1"
s3_bucket_name = "cloudmenu-frontend"
cloudfront_distribution_id = "XXXXXXXXXXXXXX"
cloudfront_domain_name = "xxxxxxxxxxxxxx.cloudfront.net"
api_gateway_endpoint = "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/..."
dynamodb_table_name = "CloudMenuOrders"
```
Các giá trị trên chỉ mang tính minh họa và sẽ khác nhau tùy theo AWS account, region và lần triển khai.

CloudFront domain name được sử dụng để truy cập frontend CloudMenu sau khi triển khai. Trong khi đó, API Gateway endpoint được frontend sử dụng để gửi request đến backend.
