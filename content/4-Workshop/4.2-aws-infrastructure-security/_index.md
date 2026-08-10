---
title : "AWS infrastructure and security foundation"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---

## 4.2 AWS infrastructure and security foundation

Infrastructure and security foundation. CloudMenu is deployed on AWS Cloud using a layered architecture that prioritizes managed services and serverless technologies to simplify operations, improve scalability, and maintain cost efficiency during the testing phase:

Presentation tier — user interfaces and frontend hosting are provided through Amazon S3 and distributed to users via Amazon CloudFront. This tier includes the customer interface, kitchen interface, and management Dashboard.
Application tier — APIs and business logic are handled by Amazon API Gateway and AWS Lambda, providing functions such as menu management, order processing, and order status updates.
Data tier — application data is stored in Amazon DynamoDB, including menu items, table information, orders, and other related data.

For security, CloudMenu uses AWS IAM to manage access permissions and IAM Roles to allow AWS services to securely access the resources they require. Direct access from clients to backend resources and the database is restricted. S3 Block Public Access, HTTPS/TLS, and API access controls are also applied to reduce the risk of unauthorized access and protect data in transit.

The implementation details are divided into technical layers, covering networking, frontend, backend, database, and security, corresponding to the following modules:

1. [4.2.1 VPC and networking](4.2.1-vpc-network/).
2. [4.2.2 Frontend Hosting and Content Distribution](4.2.2-client-facing/) 
3. [4.2.3 Backend and Processing Platform](4.2.3-backend-platform/)
4. [4.2.4 Database and Data Security](4.2.4-database/).