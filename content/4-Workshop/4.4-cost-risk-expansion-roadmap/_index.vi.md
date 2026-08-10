---
title : "Chi phí, rủi ro và định hướng mở rộng"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 4.4. </b> "
---

## 4.4 Chi phí, rủi ro và định hướng mở rộng

### Tối ưu chi phí
CloudMenu sử dụng kiến trúc serverless và managed services nhằm giảm chi phí vận hành và không phải duy trì máy chủ liên tục. Các dịch vụ chính cần theo dõi chi phí gồm Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB và Amazon CloudWatch.

Đối với môi trường development và testing, có thể áp dụng các biện pháp tối ưu sau:

- Sử dụng Amazon S3 để lưu trữ frontend và theo dõi dung lượng lưu trữ, số lượng request cũng như data transfer.
- Theo dõi lượng request và data transfer của Amazon CloudFront, đặc biệt khi số lượng người dùng truy cập tăng.
- Tối ưu số lượng và thời gian thực thi của AWS Lambda để hạn chế chi phí phát sinh do invocation và compute time.
- Thiết kế DynamoDB phù hợp với workload thực tế của CloudMenu, hạn chế các thao tác đọc dữ liệu không cần thiết và tối ưu cách truy vấn bảng CloudMenuOrders.
- Theo dõi số lượng request của Amazon API Gateway, đặc biệt đối với các API được gọi thường xuyên như Get Orders.
- Tối ưu CloudWatch Logs, thiết lập thời gian lưu trữ log phù hợp để tránh lưu trữ dữ liệu log không cần thiết trong thời gian dài.
- Sử dụng AWS Free Tier trong phạm vi phù hợp với môi trường thử nghiệm và thường xuyên kiểm tra chi phí thông qua AWS Billing và Cost Explorer.
- Không triển khai thêm các dịch vụ như VPC, NAT Gateway, WAF hoặc Cognito khi hệ thống chưa có yêu cầu thực tế, nhằm giữ kiến trúc đơn giản và tiết kiệm chi phí.

Do CloudMenu sử dụng Lambda và DynamoDB theo mô hình serverless, chi phí có thể tăng hoặc giảm theo mức độ sử dụng thay vì phải duy trì chi phí cố định cho application server.

### Rủi ro và giảm thiểu

Một số rủi ro chính đối với CloudMenu bao gồm:

Rủi ro tăng chi phí: số lượng request đến API Gateway, Lambda, DynamoDB hoặc CloudFront tăng cao có thể làm chi phí tăng theo mức sử dụng.

Rủi ro cấu hình sai: cấu hình S3, IAM policy, API Gateway hoặc CloudFront không phù hợp có thể dẫn đến truy cập trái phép hoặc làm hệ thống hoạt động không đúng.

Rủi ro mất hoặc sai lệch dữ liệu: lỗi trong quá trình tạo đơn hàng, truy xuất đơn hàng hoặc cập nhật trạng thái có thể làm sai lệch thông tin trong bảng CloudMenuOrders.

Rủi ro về credentials: việc lưu AWS access key, secret key hoặc các thông tin nhạy cảm trực tiếp trong source code có thể dẫn đến rò rỉ thông tin.

Rủi ro khi triển khai frontend thủ công: CloudMenu hiện upload frontend lên S3 thủ công, do đó có thể xảy ra lỗi khi thiếu file, upload nhầm phiên bản hoặc chưa phân phối đúng phiên bản mới qua CloudFront.

Rủi ro khi cập nhật Lambda: thay đổi logic backend hoặc cấu hình API Gateway có thể làm ảnh hưởng đến các giao diện Customer, Kitchen và Manager.

Rủi ro phụ thuộc vào AWS services: CloudMenu phụ thuộc vào nhiều AWS managed services, do đó việc thay đổi kiến trúc trong tương lai cần xem xét khả năng tương thích giữa các dịch vụ.

Các biện pháp giảm thiểu bao gồm:

- Áp dụng nguyên tắc Least Privilege cho IAM Roles và policies.
- Không lưu AWS credentials hoặc secret trực tiếp trong source code hoặc commit lên GitHub.
- Cấu hình quyền truy cập S3 phù hợp và hạn chế quyền ghi/xóa bucket.
- Không cho phép frontend truy cập trực tiếp vào DynamoDB; mọi thao tác dữ liệu phải đi qua API Gateway và Lambda.
- Sử dụng CloudWatch Logs và Metrics để phát hiện lỗi và theo dõi hoạt động bất thường.
- Thiết lập cơ chế backup và recovery phù hợp cho dữ liệu DynamoDB.
- Kiểm thử các API quan trọng trước khi triển khai phiên bản mới.
- Sử dụng checklist khi upload frontend và cập nhật Lambda để hạn chế lỗi triển khai thủ công.

### Lộ trình mở rộng
CloudMenu có thể được mở rộng theo từng giai đoạn tùy theo quy mô hệ thống.

- Chuẩn hóa CI/CD: xây dựng pipeline tự động từ GitHub để build và deploy frontend lên S3, đồng thời tự động triển khai các Lambda function.
- Tăng cường kiểm thử: bổ sung unit test, integration test và smoke test cho các nghiệp vụ quan trọng như tạo đơn hàng, lấy danh sách đơn hàng và cập nhật trạng thái.
- Bổ sung authentication và authorization: tích hợp Amazon Cognito khi hệ thống cần quản lý tài khoản và phân quyền giữa Customer, Kitchen và Manager.
- Tăng cường bảo mật API: có thể bổ sung authentication, authorization, rate limiting và AWS WAF khi CloudMenu được đưa vào môi trường production với lượng truy cập lớn.
- Cải thiện khả năng quan sát: xây dựng CloudWatch Dashboard và Alarms để theo dõi API Gateway, Lambda, DynamoDB và phát hiện lỗi hoặc mức sử dụng bất thường.
- Mở rộng mô hình dữ liệu: bổ sung các bảng hoặc entity như MenuItems, Tables, Restaurants và OrderHistory khi hệ thống cần quản lý đầy đủ hơn các nghiệp vụ nhà hàng.
- Hỗ trợ nhiều nhà hàng: nếu CloudMenu phát triển theo hướng multi-tenant, có thể mở rộng mô hình dữ liệu và cơ chế phân quyền để mỗi nhà hàng quản lý menu, bàn và đơn hàng riêng biệt.
- Xử lý bất đồng bộ: khi xuất hiện các nghiệp vụ cần xử lý nền như gửi thông báo, cập nhật báo cáo hoặc đồng bộ dữ liệu, có thể bổ sung các dịch vụ event-driven của AWS.
- Tối ưu hiệu năng: khi số lượng request và dữ liệu tăng, có thể xem xét caching, DynamoDB Streams hoặc các dịch vụ phân tích phù hợp với workload thực tế.
- Tăng cường network security: chỉ bổ sung VPC, VPC Endpoints hoặc các thành phần network phức tạp khi backend có yêu cầu truy cập private resources hoặc cần kiểm soát network ở mức cao hơn.

Mục tiêu của lộ trình là duy trì CloudMenu ở trạng thái đơn giản, serverless và tiết kiệm chi phí trong giai đoạn phát triển, đồng thời tạo nền tảng để mở rộng thành một hệ thống production-ready khi số lượng nhà hàng, người dùng và đơn hàng tăng lên.