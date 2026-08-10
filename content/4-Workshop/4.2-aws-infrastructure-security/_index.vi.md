---
title : "Nền tảng hạ tầng AWS và bảo mật"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---

## 4.2 Nền tảng hạ tầng AWS, phân lớp mạng và bảo mật

Nền tảng hạ tầng và bảo mật. CloudMenu được triển khai trên AWS Cloud theo kiến trúc phân lớp, ưu tiên managed services và serverless nhằm đơn giản hóa vận hành, tăng khả năng mở rộng và kiểm soát chi phí trong giai đoạn thử nghiệm:

Tầng trình bày (Presentation tier) — giao diện người dùng và hosting frontend trên Amazon S3, được phân phối đến người dùng thông qua Amazon CloudFront. Tầng này bao gồm giao diện khách hàng, giao diện bếp và Dashboard quản lý.
Tầng ứng dụng (Application tier) — API và logic nghiệp vụ được xử lý thông qua Amazon API Gateway và AWS Lambda, cung cấp các chức năng như quản lý menu, xử lý đơn hàng và cập nhật trạng thái đơn.
Tầng dữ liệu (Data tier) — lưu trữ dữ liệu ứng dụng trên Amazon DynamoDB, bao gồm thông tin món ăn, bàn, đơn hàng và các dữ liệu liên quan.

Về bảo mật, CloudMenu áp dụng AWS IAM để quản lý quyền truy cập, sử dụng IAM Roles cho các AWS services và hạn chế quyền truy cập trực tiếp từ client đến các tài nguyên backend và database. S3 Block Public Access, HTTPS/TLS và các cơ chế kiểm soát API được sử dụng để giảm nguy cơ truy cập trái phép và bảo vệ dữ liệu trong quá trình truyền tải.

Chi tiết triển khai được chia theo các lớp kỹ thuật, từ network và frontend đến backend, database và security, tương ứng với các module:

1. [4.2.1 VPC và mạng](4.2.1-vpc-network/).
2. [4.2.2 Frontend Hosting và phân phối nội dung](4.2.2-client-facing/).
3. [4.2.3 Backend và nền tảng xử lý](4.2.3-backend-platform/).
4. [4.2.4 Cơ sở dữ liệu và bảo mật dữ liệu](4.2.4-database/).