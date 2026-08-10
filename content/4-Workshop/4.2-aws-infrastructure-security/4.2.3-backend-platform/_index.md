---
title : "Backend and Processing Platform"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 4.2.3 </b> "
---

## 4.2.3 Backend and Processing Platform

This section presents the serverless backend architecture of CloudMenu, focusing on the API processing flow from the frontend to the database. Unlike traditional backend architectures based on EC2 instances or containerized applications, CloudMenu currently uses managed/serverless AWS services including Amazon API Gateway, AWS Lambda, and Amazon DynamoDB. This approach reduces server management overhead and is suitable for the current development and testing scale of the system.

The current CloudMenu architecture can be divided into two main flows:

- Frontend deployment and delivery flow: Developer → GitHub → Amazon S3 → Amazon CloudFront → Browser/Phone.
- Data processing flow: Customer/Kitchen/Manager → Frontend → Amazon API Gateway → AWS Lambda → Amazon DynamoDB.

### Frontend Deployment and Delivery Flow

The CloudMenu frontend currently consists of three main interfaces:

- Customer: Customers scan the QR code assigned to a table to access the menu and place orders.
- Kitchen: Kitchen staff monitor and update order statuses.
- Manager: Managers monitor order data and statistics.

The frontend files are stored in Amazon S3 and distributed to users through Amazon CloudFront.

At the current stage, the GitHub → S3 process is not automated through CI/CD. Developers build the frontend and manually upload the generated files to S3. GitHub in the diagram represents the source code repository and the high-level deployment flow, rather than an automated deployment pipeline.

After the frontend files are uploaded to S3, Amazon CloudFront acts as the CDN and distributes the frontend content to users. Users can therefore access CloudMenu through the CloudFront URL without running the frontend directly on a development machine or opening VS Code.

Reasons for choosing S3 + CloudFront:

- Amazon S3 is suitable for storing frontend files and static assets.
- CloudFront provides CDN-based content delivery.
- No dedicated web server or EC2 instance is required for frontend hosting.
- The architecture is consistent with CloudMenu's serverless approach and cost-optimization goals.
- The architecture can later be extended with a custom domain and additional HTTPS configuration.

### Amazon API Gateway

Role: Amazon API Gateway serves as the entry point for HTTP requests from the CloudMenu frontend. When a user performs an operation that requires accessing or modifying data, the frontend sends a request to API Gateway.

The main API operations currently include:

- Create Order: Creates a new order when a customer places an order.
- Get Orders: Retrieves order information for Customer, Kitchen, or Manager interfaces.
- Update Order Status: Updates the order status when the Kitchen processes an order.

API Gateway then routes each request to the corresponding AWS Lambda function.

Reasons for choosing:

- No dedicated API server needs to be deployed and managed.
- Native integration with AWS Lambda.
- Requests can be processed according to actual workload.
- Suitable for independent business APIs such as those used by CloudMenu.
- Reduces operational costs during the development and testing phase.

### AWS Lambda

**Role:** AWS Lambda executes the backend business logic of CloudMenu. Requests received through API Gateway are forwarded to the corresponding Lambda functions for processing.

**Why we chose it:** 
- No server or container infrastructure needs to be managed.
- Automatically handles requests according to workload.
- Suitable for APIs with intermittent workloads.
- Individual business operations can be separated into different functions for easier development and maintenance.
- Integrates well with API Gateway and DynamoDB in a serverless architecture.

### Amazon DynamoDB

**Role:** Amazon DynamoDB is the primary database for CloudMenu's current architecture.

The current table is: CloudMenuOrders

The order data contains the following main attributes:

| Attribute | Description |
| :--- | :--- |
| **Order ID** | Unique identifier of the order |
| **Table Number** | Table number associated with the order, e.g. Table 03 |
| **Items** | List of ordered menu items |
| **Total Amount** | Total order amount |
| **Status** | Current order status |
| **Created At** | Order creation timestamp |
| **Updated At** | Last update timestamp |

### AWS IAM

**Role:** AWS IAM controls access between Lambda and other AWS services.

Lambda does not use unrestricted AWS permissions. Instead, it is assigned an IAM Role with the required policies. For CloudMenu, one of the main permissions is allowing Lambda to perform the required operations on the DynamoDB CloudMenuOrders table.

**Why we chose it:**

- Controls access between AWS services.
- Applies the Least Privilege principle.
- Avoids storing AWS Access Keys directly in Lambda source code.
- Reduces the risk of unauthorized access to AWS resources.

### Overall Data Processing Flow

The overall CloudMenu data processing flow can be summarized as follows:

![Overall Data Processing Flow](/images/AWS_CloudMenu_data.png)
