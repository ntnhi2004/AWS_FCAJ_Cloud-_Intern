---
title : "Frontend Hosting and user authentication"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 4.2.2 </b> "
---

## 4.2.2 Frontend Hosting and user authentication

This section introduces Amazon S3 and Amazon CloudFront for frontend hosting and content distribution in CloudMenu. For user authentication, CloudMenu can adopt an authentication mechanism appropriate for each interface and can be extended with Amazon Cognito when centralized user management is required.

### Amazon S3

**Role:** Stores CloudMenu frontend files and static assets, including the Customer, Kitchen, and Dashboard interfaces. S3 acts as the frontend storage layer and provides the origin for CloudFront.

The frontend can be deployed to S3 through an automated deployment process or Terraform. Once the files are uploaded, CloudFront distributes them to users instead of requiring clients to access the S3 bucket directly.

**Why we chose it:** Amazon S3 provides highly durable, low-cost storage without requiring server management. It is well suited for CloudMenu's static frontend, particularly during the testing phase where infrastructure cost optimization is important.

### Amazon CloudFront

**Role:** Distributes the frontend and static assets from S3 to users through the AWS CDN. CloudFront acts as the distribution layer in front of S3, helping reduce latency and improve page loading performance for customers, kitchen staff, and managers.

CloudFront can also be configured to use HTTPS and restrict direct access to the S3 origin. With Origin Access Control (OAC), the S3 bucket can remain private while allowing CloudFront to retrieve the required objects.

The main frontend hosting flow is:

**User → CloudFront → S3**

**Why we chose it:** CloudFront improves content delivery performance, provides HTTPS support, and reduces the need to expose S3 directly to the Internet. The combination of S3 and CloudFront is also well aligned with CloudMenu's serverless architecture and reduces operational overhead compared with maintaining a dedicated web server.

### Frontend Configuration

The frontend needs to know the API endpoint used to communicate with the backend. The API endpoint can be configured through environment variables during the build or deployment process.

For example:

**API Base URL → API Gateway endpoint**

The frontend uses this URL to access APIs related to menus, tables, orders, and order statuses.

Separating the API endpoint from the source code makes it easier to support different environments such as development, testing, and production without modifying the frontend source code directly.

### Authentication and Authorization

In the current CloudMenu architecture, the Customer, Kitchen, and Dashboard interfaces have different authentication and authorization requirements. Administrative and internal business functions should be protected at both the frontend and backend levels.

If CloudMenu requires centralized account management, Amazon Cognito can be integrated to provide:

- User Pools for user registration and authentication.
- App Clients for connecting the frontend to Cognito.
- JWT tokens for authenticating API requests.
- User authorization based on roles or groups.
- Integration with API Gateway and Lambda for access control.

**Reason for selection:** Amazon Cognito is a managed AWS authentication service that reduces the need to build and maintain a custom authentication system. Cognito supports JWT-based authentication and can integrate with API Gateway, Lambda, and the CloudMenu frontend.

During the testing phase, if CloudMenu does not require a complex account management system, Cognito can be introduced in a later stage rather than becoming a mandatory dependency for the entire system.