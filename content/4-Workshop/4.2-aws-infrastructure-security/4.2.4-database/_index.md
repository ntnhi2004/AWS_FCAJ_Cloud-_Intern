---
title : "Database"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 4.2.4 </b> "
---

## 4.2.4 Database

CloudMenu uses Amazon DynamoDB as the primary database for the system. DynamoDB is selected as a serverless database and is directly integrated with AWS Lambda to store and retrieve order data.

Unlike a traditional architecture using RDS PostgreSQL, CloudMenu does not require a dedicated database server, private subnet, database Security Group, or Bastion Host. Lambda communicates directly with DynamoDB through an IAM Role, while Lambda activities are recorded and monitored through Amazon CloudWatch.

### Data Security

Data in DynamoDB is accessed through AWS services that are authorized using IAM. CloudMenu does not provide direct access from the browser to DynamoDB.

Therefore, the frontend communicates only with API Gateway, while direct access to DynamoDB is restricted to Lambda through the appropriate IAM permissions. This approach helps prevent unauthorized direct access to the database and follows the least privilege principle.

### Database Expansion Roadmap

At the current development stage, the CloudMenuOrders structure is sufficient to support the core operations of the system. As CloudMenu expands, additional entities/tables can be introduced to support restaurant management, such as:

- Menu Items: stores menu item information, including prices, images, and availability status.
- Tables: stores table information and the corresponding QR codes.
- Orders: extends order information and order processing history.
- Restaurants: supports multiple restaurants if the system evolves toward a multi-tenant architecture.
- Order History: stores historical order data for reporting and statistical analysis.

In addition, mechanisms such as DynamoDB Streams, caching, and AWS data analytics services can be introduced as the system's requirements for statistics, data processing, and scalability increase.
