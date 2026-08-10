---
title: "Workshop"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 4. </b> "
---

# Workshop: Kiến trúc CloudMenu trên AWS

Phần workshop này đi sâu vào kiến trúc hạ tầng AWS của CloudMenu: các dịch vụ được sử dụng, phân lớp mạng, cơ chế bảo mật và cách các thành phần trong hệ thống kết nối với nhau. Triển khai Terraform theo từng lớp (VPC, frontend, backend và các thành phần liên quan) được tổ chức riêng trong phần triển khai hạ tầng; các thành phần còn lại của AWS infrastructure như S3, CloudFront, API Gateway, Lambda, DynamoDB và các dịch vụ hỗ trợ sẽ được trình bày trong phần thiết kế kiến trúc khi cần làm rõ.

Nội dung được gom thành các nhóm chính để thuận tiện theo dõi:
- Tổng quan và kiến trúc hệ thống CloudMenu.
- Nền tảng hạ tầng AWS, phân lớp mạng và bảo mật.
- Triển khai Terraform theo từng lớp, bao gồm hosting frontend trên S3/CloudFront và triển khai backend theo kiến trúc Serverless.
- Chi phí vận hành, các rủi ro cần lưu ý và phương án mở rộng hệ thống.

#### Nội dung

1. [4.1 Tổng quan và kiến trúc hệ thống CloudMenu](4.1-system-overview-architecture/)
2. [4.2 Nền tảng hạ tầng AWS, phân lớp mạng và bảo mật](4.2-aws-infrastructure-security/)
3. [4.3 Triển khai Terraform (theo lớp)](4.3-deployment-operations-monitoring/)
4. [4.4 Chi phí vận hành, các rủi ro và định hướng mở rộng](4.5-cost-risk-expansion-roadmap/)
5. [4.5 Dọn dẹp tài nguyên](4.6-legacy-cleanup/)