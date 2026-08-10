---
title: "Triển khai Terraform (theo lớp)"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

## 4.3 Triển khai Terraform (theo lớp)

Chương này trình bày quy trình Infrastructure as Code (IaC) cho hệ thống CloudMenu bằng Terraform. Nội dung tập trung vào cách chuẩn bị môi trường, cấu hình AWS và triển khai các thành phần hạ tầng theo từng lớp của kiến trúc serverless.

Kiến trúc CloudMenu hiện tại sử dụng các dịch vụ AWS chính gồm Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, AWS IAM và Amazon CloudWatch. Vì vậy, Terraform được sử dụng để quản lý và chuẩn hóa các tài nguyên AWS thay vì triển khai thủ công từng thành phần trên AWS Management Console.

### Luồng workshop

1. **Clone repository.** Clone source code của CloudMenu từ repository GitHub: **https://github.com/NhuLuk/CloudMenu-AWS**.

```bash
git clone https://github.com/NhuLuk/CloudMenu-AWS
```

2. **Cài công cụ trên máy:** **Terraform**, **AWS CLI**, **Git**. Sau đó cấu hình thông tin xác thực AWS cho tài khoản sử dụng trong môi trường lab bằng aws configure hoặc thông qua các biến môi trường phù hợp.

3. **Mở project trong editor:** Sau khi clone repository, mở project bằng IDE/editor. Phần Terraform được tổ chức riêng với source code ứng dụng để dễ quản lý và triển khai hạ tầng.

4. **Kiểm tra cấu hình AWS:** Trước khi triển khai, cần kiểm tra AWS account và region đang được sử dụng. Các thông tin nhạy cảm như AWS credentials, access key hoặc secret key không được lưu trực tiếp trong source code hoặc commit lên GitHub.

5. **Khởi tạo Terraform:** Di chuyển đến thư mục chứa Terraform configuration và thực hiện: **terraform init** Lệnh này khởi tạo Terraform working directory và tải các provider/module cần thiết.

6. **Kiểm tra kế hoạch triển khai:** Sử dụng **terraform plan** để kiểm tra các tài nguyên AWS mà Terraform dự kiến tạo mới, thay đổi hoặc xóa.

7. **Triển khai hạ tầng:** Sau khi kiểm tra plan và xác nhận cấu hình chính xác: **terraform apply** Terraform sẽ tạo các tài nguyên theo dependency graph đã được định nghĩa trong configuration.

### Vì sao dùng Terraform

- Infrastructure as Code: Hạ tầng AWS được định nghĩa bằng code, giúp dễ kiểm tra, version control và tái triển khai.
- Tính nhất quán: Các tài nguyên như S3, CloudFront, API Gateway, Lambda và DynamoDB có thể được tạo với cùng một cấu hình giữa các môi trường.
- Giảm thao tác thủ công: Hạn chế việc phải tạo từng AWS resource trực tiếp trên AWS Management Console.
- Quản lý dependency: Terraform tự xác định thứ tự tạo tài nguyên dựa trên mối quan hệ giữa các resource.
- Dễ mở rộng: Có thể bổ sung các dịch vụ AWS khác vào Terraform khi CloudMenu phát triển.

### Nội dung

1. [4.3.1 Chạy hạ tầng với Terraform](4.3.1-run-infrastructure-terraform/)
2. [4.3.2 Hosting frontend](4.3.2-frontend-hosting/)