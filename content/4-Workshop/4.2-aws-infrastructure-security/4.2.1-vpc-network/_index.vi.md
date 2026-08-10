---
title : "VPC và mạng"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 4.2.1 </b> "
---

## 4.2.1 VPC và mạng

CloudMenu sử dụng kiến trúc mạng AWS theo hướng phân tách các tài nguyên cần bảo vệ khỏi các thành phần public-facing, đồng thời tận dụng các dịch vụ managed và serverless để giảm độ phức tạp của hạ tầng.

Khác với mô hình 3-tier truyền thống sử dụng ALB, ECS và RDS, CloudMenu sử dụng Amazon S3 + CloudFront cho frontend và API Gateway + Lambda + DynamoDB cho backend. Các dịch vụ này được AWS quản lý phần lớn hạ tầng mạng bên dưới, do đó không cần triển khai toàn bộ frontend, API Gateway hoặc DynamoDB vào các subnet của VPC.

| Thành phần | Vai trò |
| :--- | :--- |
| **VPC** | Mạng riêng ảo dùng để quản lý các tài nguyên AWS cần đặt trong network boundary riêng. Trong kiến trúc serverless hiện tại, VPC chủ yếu được sử dụng khi Lambda cần truy cập các tài nguyên private hoặc khi hệ thống mở rộng thêm các thành phần cần network isolation. |
| **Internet Gateway** | Cung cấp kết nối giữa VPC và Internet cho các tài nguyên trong public subnet khi cần. Không sử dụng làm đường truy cập trực tiếp đến DynamoDB hoặc Lambda. |
| **NAT Gateway** | Cho phép các tài nguyên trong private subnet gửi traffic outbound ra Internet mà không cần public IP. Có thể được sử dụng khi Lambda được đặt trong VPC và cần truy cập các dịch vụ bên ngoài AWS. |
| **VPC endpoint** | Cung cấp đường kết nối private từ các tài nguyên trong VPC đến một số AWS services mà không cần đi qua Internet công cộng. Có thể sử dụng để giảm phụ thuộc vào NAT Gateway khi Lambda trong VPC cần truy cập các dịch vụ AWS hỗ trợ endpoint. |
| **Security Group** |Kiểm soát traffic network cho các tài nguyên có network interface trong VPC, đặc biệt khi Lambda được kết nối vào VPC hoặc khi CloudMenu được mở rộng thêm các tài nguyên private.|

### VPC và subnets

VPC (Virtual Private Cloud) là mạng riêng ảo trên AWS, cho phép kiểm soát phạm vi địa chỉ IP, subnet, routing và các cơ chế network security. Trong kiến trúc CloudMenu hiện tại, không phải mọi AWS service đều cần nằm trong VPC.

Frontend được lưu trữ trên Amazon S3 và phân phối thông qua Amazon CloudFront. Đây là các dịch vụ managed và không cần đặt trong subnet của VPC. Tương tự, Amazon API Gateway và Amazon DynamoDB cũng được AWS quản lý network infrastructure ở phía dưới.

Nếu AWS Lambda được triển khai ngoài VPC, Lambda có thể giao tiếp với các AWS services như DynamoDB mà không cần xây dựng VPC, subnet hoặc NAT Gateway riêng. Đây là phương án phù hợp với CloudMenu trong giai đoạn thử nghiệm vì giúp giảm số lượng thành phần hạ tầng cần quản lý.

Trong trường hợp cần đưa Lambda vào VPC để truy cập các private resources, có thể triển khai các subnet theo mô hình:

| Nhóm subnet | Số subnet | Mục đích |
| :--- | :---: | :--- |
| **Public** | 2 | Một subnet public trên mỗi Availability Zone, dành cho các tài nguyên cần kết nối trực tiếp với Internet hoặc NAT Gateway khi kiến trúc được mở rộng. |
| **Private** | 2 | Một subnet private trên mỗi Availability Zone, dành cho Lambda hoặc các backend resources cần network isolation. Các tài nguyên này không nhận inbound traffic trực tiếp từ Internet. |

Đối với kiến trúc CloudMenu hiện tại, việc tạo public/private subnet chỉ thực sự cần thiết khi Lambda hoặc các backend resources có yêu cầu truy cập vào VPC. Điều này giúp tránh triển khai VPC phức tạp khi hệ thống chưa cần đến.

### Network Flow

Luồng truy cập chính của **CloudMenu** được tổ chức theo các lớp:

**Customer / Kitchen / Dashboard → CloudFront → S3**

đối với các static frontend assets, và:

**Customer / Kitchen / Dashboard → API Gateway → Lambda → DynamoDB**

đối với các request liên quan đến API và dữ liệu.

Trong luồng này:

- **CloudFront** là điểm phân phối frontend đến người dùng.
- **S3** đóng vai trò origin lưu trữ frontend và static assets.
- **API Gateway** là public API entry point của backend.
- **Lambda** xử lý business logic và thực hiện các thao tác dữ liệu.
- **DynamoDB** được truy cập thông qua backend, không được expose trực tiếp cho client.

Do đó, client không cần có quyền truy cập trực tiếp vào database hoặc các tài nguyên backend private.

### Security Group (ứng dụng và dữ liệu)

Security Group là stateful virtual firewall được sử dụng cho các tài nguyên có network interface trong VPC. Security Group chỉ cho phép các traffic được định nghĩa trong inbound và outbound rules; các traffic không được phép sẽ bị từ chối theo cơ chế mặc định.

Trong kiến trúc serverless hiện tại, CloudMenu không cần Security Group cho S3, CloudFront, API Gateway hoặc DynamoDB. Nếu Lambda được kết nối vào VPC, Security Group có thể được sử dụng để kiểm soát traffic giữa Lambda và các private resources.

Các Security Group trong hệ thống:

| Security Group | Vai trò |
| :--- | :--- |
| **Lambda Security Group** | Gắn với Lambda khi Lambda được triển khai trong VPC; kiểm soát traffic outbound từ Lambda đến các private resources cần thiết. |
| **Database/Private Resource Security Group** | Sử dụng nếu hệ thống được mở rộng với database hoặc service chạy trong VPC; chỉ cho phép traffic từ Lambda Security Group hoặc các nguồn được cấp quyền. |
| **VPC Endpoint Security Group** | Sử dụng cho Interface VPC Endpoints; cho phép HTTPS (TCP 443) từ các resources trong VPC đến AWS service endpoints. |

### VPC endpoint

VPC Endpoint cho phép tài nguyên trong VPC giao tiếp với các AWS services thông qua mạng AWS mà không cần đi qua Internet công cộng. Đây là một cơ chế hữu ích khi CloudMenu mở rộng và Lambda được triển khai trong VPC.

Hai loại endpoint chính có thể được sử dụng:

- Gateway Endpoint — thường được sử dụng cho Amazon S3 và được cấu hình thông qua route table. Traffic đến S3 có thể đi qua AWS private network thay vì NAT Gateway.
- Interface Endpoint — tạo một hoặc nhiều network interfaces trong subnet và cung cấp private connectivity đến các AWS services hỗ trợ endpoint.

Trong trường hợp CloudMenu sử dụng Lambda ngoài VPC, VPC Endpoint không bắt buộc. Nếu Lambda được đưa vào private subnet, có thể cân nhắc các endpoint phù hợp để giảm traffic đi qua NAT Gateway và tăng mức độ kiểm soát network.

| Resource | Loại | Ý nghĩa vận hành |
| :--- | :--- | :--- | :--- |
| S3 VPC Endpoint | Gateway | Cho phép resources trong VPC truy cập S3 thông qua AWS private network. |
| CloudWatch Logs Endpoint | Interface | Cho phép resources trong VPC gửi logs đến CloudWatch Logs mà không cần phụ thuộc hoàn toàn vào NAT Gateway. |
| ECR API Endpoint | Interface | Hỗ trợ truy cập ECR API nếu CloudMenu mở rộng sang container-based backend. |
| ECR DKR Endpoint | Interface | Hỗ trợ truy cập Docker registry của ECR khi sử dụng container images. |

### Định hướng Networking

Trong giai đoạn thử nghiệm, CloudMenu ưu tiên kiến trúc serverless tối giản, do đó không triển khai VPC, NAT Gateway và nhiều subnet nếu không có yêu cầu thực tế. Điều này giúp giảm chi phí và giảm số lượng thành phần cần quản lý.

Khi hệ thống mở rộng, VPC có thể được bổ sung để tạo network isolation cho các backend resources hoặc database private. Khi đó, Lambda có thể được triển khai trong private subnets và sử dụng NAT Gateway hoặc VPC Endpoints tùy theo nhu cầu truy cập Internet và AWS services.

Cách tiếp cận này giúp CloudMenu duy trì kiến trúc đơn giản ở quy mô nhỏ nhưng vẫn có khả năng chuyển sang mô hình network isolation chặt chẽ hơn khi yêu cầu về bảo mật và quy mô tăng lên.