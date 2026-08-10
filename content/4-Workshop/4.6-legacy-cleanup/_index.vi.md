---
title : "Dọn dẹp tài nguyên"
date : 2024-01-01
weight : 99
chapter : false
pre : " <b> 4.5. </b> "
draft : false
---

## 4.5 Dọn dẹp tài nguyên

Khi không còn cần sử dụng môi trường CloudMenu trên AWS, có thể sử dụng Terraform để hủy các tài nguyên đã được tạo và quản lý bởi Terraform. Lệnh terraform destroy sẽ lập kế hoạch và xóa các resource thuộc Terraform state của môi trường đang triển khai.

Đối với CloudMenu, các tài nguyên có thể được hủy bao gồm Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, IAM và Amazon CloudWatch, tùy thuộc vào cấu hình Terraform thực tế.

### Chạy ở đâu

Di chuyển đến thư mục chứa Terraform configuration của CloudMenu, tức là thư mục đã được sử dụng khi triển khai bằng terraform apply.

Trước tiên, kiểm tra trạng thái hiện tại:
```bash
terraform state list
```
Lệnh này hiển thị danh sách các AWS resources đang được Terraform quản lý.

Sau đó thực hiện:
```bash
terraform destroy
```
Terraform sẽ tạo destruction plan và hiển thị các tài nguyên dự kiến bị xóa. Kiểm tra kỹ danh sách trước khi xác nhận.

Khi Terraform yêu cầu xác nhận:
```bash
Do you really want to destroy all resources? 
Enter a value: yes
```
nhập:
```bash
yes
```
để bắt đầu quá trình hủy tài nguyên.

### Trước khi destroy

Trước khi thực hiện terraform destroy, cần kiểm tra:

- AWS Account và AWS Region đang được sử dụng có đúng với môi trường CloudMenu cần hủy hay không.
- Terraform đang sử dụng đúng state của môi trường cần xóa.
- Kiểm tra danh sách resource bằng terraform state list.
- Nếu bảng CloudMenuOrders đang chứa dữ liệu quan trọng, cần thực hiện backup dữ liệu trước khi destroy.
- Đảm bảo không có người dùng đang sử dụng CloudMenu trong thời điểm thực hiện.

Có thể kiểm tra AWS account hiện tại bằng:
```bash
aws sts get-caller-identity
```
và kiểm tra Terraform plan trước khi destroy:
```bash
terraform plan -destroy
```
Lệnh này giúp xem trước các resource Terraform dự kiến xóa mà chưa thực hiện thao tác hủy.

### Sau khi destroy

Sau khi quá trình destroy hoàn tất, có thể kiểm tra lại Terraform state:
```bash
terraform state list
```
Nếu toàn bộ stack đã được hủy thành công, danh sách resource được Terraform quản lý sẽ không còn các tài nguyên CloudMenu tương ứng.

Có thể kiểm tra thêm trên AWS Management Console để xác nhận các thành phần như:

- Amazon S3 bucket frontend.
- Amazon CloudFront distribution.
- Amazon API Gateway.
- AWS Lambda functions.
- Amazon DynamoDB table CloudMenuOrders.
- CloudWatch Log Groups.
- IAM resources được Terraform tạo.

Nếu terraform destroy bị lỗi giữa chừng, cần đọc thông báo lỗi, xử lý nguyên nhân và chạy lại:
```bash
terraform destroy
```
cho đến khi các tài nguyên cần hủy được xử lý hoàn tất.