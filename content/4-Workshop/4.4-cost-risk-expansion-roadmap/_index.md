---
title : "Cost, risk, and expansion roadmap"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 4.4. </b> "
---

## 4.4 Cost, risk, and expansion roadmap

### Cost optimization
CloudMenu prioritizes managed and serverless AWS services to reduce operational overhead and avoid maintaining continuously running servers. The main services whose costs should be monitored include Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, and Amazon CloudWatch. If optional components are introduced, their associated costs, such as Amazon Cognito, AWS WAF, NAT Gateway, and VPC Endpoints, should also be considered.

For development and testing environments, the following cost optimization measures can be applied:

- Use S3 and Lambda according to actual requirements and avoid unnecessary resources or functions.
- Optimize the storage size and request volume of S3, while monitoring CloudFront requests and data transfer.
- Monitor and control the number of requests processed by API Gateway and Lambda.
- Design DynamoDB tables efficiently and avoid unnecessary read/write operations.
- Optimize CloudWatch Logs, particularly log retention, to avoid storing unnecessary logs for long periods.
- Enable Cognito, WAF, VPC, NAT Gateway, or VPC Endpoints only when their functionality is actually required.
- Prefer Lambda functions without VPC connectivity during the testing phase when there is no requirement to access private resources, avoiding unnecessary NAT Gateway costs.
- Use the AWS Free Tier where appropriate during the testing phase and regularly monitor costs through AWS Cost Explorer and Billing.

### Risks and mitigation
The main risks associated with CloudMenu include:

- Cost increase risk: A significant increase in requests to API Gateway, Lambda, DynamoDB, or CloudFront can increase costs based on usage.
- Configuration risks: Incorrect S3 public access, IAM policies, or API Gateway configurations may increase the risk of unauthorized access.
- Data loss or inconsistency: Errors during order processing or DynamoDB updates may affect menu information and order status.
Secret and credential exposure: Storing API keys, credentials, or sensitive information directly in source code may lead to information leakage.
- Deployment risks: Changes to the frontend, Lambda functions, or database schema may introduce errors or temporarily affect existing functionality.
- AWS service dependency: Excessive dependency on a specific AWS service may make future architectural changes more complex.

The following mitigation measures can be applied:

- Apply the Least Privilege principle to IAM users and IAM Roles.
- Do not store secrets or credentials directly in source code; use appropriate secret management services such as AWS Secrets Manager or AWS Systems Manager Parameter Store when required.
- Enable S3 Block Public Access and allow CloudFront to access the S3 origin through an appropriate access-control mechanism.
- Apply authentication and authorization to APIs when required, especially for Kitchen and Dashboard functionality.
- Use CloudWatch Logs and Metrics to detect errors and monitor abnormal system activity.
- Implement appropriate backup and recovery mechanisms for DynamoDB data.
- Use a pre-release checklist and test critical APIs before production deployment.

### Expansion roadmap
CloudMenu can be expanded gradually through the following stages:

- Standardize CI/CD: Build automated pipelines for the frontend and backend, including build, testing, and deployment stages.
- Improve testing: Add unit tests, integration tests, and smoke tests for critical APIs such as order creation, order status updates, and menu management.
- Add authentication and authorization: Integrate Amazon Cognito to manage user accounts and access control between Customer, Kitchen, and Dashboard interfaces.
- Strengthen security: Introduce AWS WAF, policy hardening, secret rotation, and advanced access-control mechanisms as the system moves toward production.
- Improve observability: Build CloudWatch Dashboards, alarms, and centralized logging to monitor API Gateway, Lambda, and DynamoDB.
Scale the architecture based on workload: Introduce VPC and VPC Endpoints when backend components require access to private resources, adding network complexity only when there is a practical requirement.
- Production readiness: As the number of restaurants, users, and orders increases, the system can be extended with database optimization, caching, asynchronous processing, and appropriate high-availability mechanisms based on actual workload requirements.

The overall goal is to keep CloudMenu simple and cost-efficient during the testing phase while maintaining a clear path toward a production-ready architecture as usage grows.