---
title : "VPC and networking"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 4.2.1 </b> "
---

## 4.2.1 VPC and networking

CloudMenu uses an AWS network architecture that separates resources that require protection from public-facing components, while taking advantage of managed and serverless services to reduce infrastructure complexity.

Unlike the traditional three-tier architecture used by SpendWiseApp with ALB, ECS, and RDS, CloudMenu uses Amazon S3 + CloudFront for the frontend and API Gateway + Lambda + DynamoDB for the backend. These services are largely managed by AWS at the underlying network level, so the frontend, API Gateway, and DynamoDB do not need to be deployed inside VPC subnets.

| Component | Role |
| :--- | :--- |
| **VPC** | A private virtual network used to manage AWS resources that require a dedicated network boundary. In the current serverless architecture, the VPC is mainly required when Lambda needs to access private resources or when additional network-isolated components are introduced. |
| **Internet Gateway** |Provides connectivity between the VPC and the public Internet for resources in public subnets when required. It is not used as a direct access path to DynamoDB or Lambda. |
| **NAT Gateway** | Allows resources in private subnets to send outbound traffic to the Internet without assigning public IP addresses. It may be required when Lambda is deployed inside a VPC and needs to access external services. |
| **VPC endpoint** | Provides private connectivity from VPC resources to supported AWS services without routing traffic through the public Internet. This can reduce dependency on NAT Gateway for AWS service traffic.|
| **Security Group** | Controls network traffic for resources with network interfaces inside the VPC, particularly when Lambda is connected to a VPC or when CloudMenu is extended with private resources.|


### VPC and subnets

A Virtual Private Cloud (VPC) is a private virtual network in AWS that provides control over IP address ranges, subnets, routing, and network security mechanisms. In the current CloudMenu architecture, not every AWS service needs to be placed inside a VPC.

The frontend is stored in Amazon S3 and distributed through Amazon CloudFront. These are managed services and do not need to be placed in VPC subnets. Similarly, Amazon API Gateway and Amazon DynamoDB are managed by AWS at the underlying network infrastructure level.

If AWS Lambda is deployed outside a VPC, it can communicate with AWS services such as DynamoDB without requiring a dedicated VPC, subnet, or NAT Gateway. This approach is suitable for CloudMenu during the testing phase because it reduces the number of infrastructure components that need to be managed.

If Lambda needs to access private resources, the VPC can be configured with subnets such as:

| Subnet group | Count | Purpose |
| :--- | :---: | :--- |
| **Public** | 2 | One public subnet per Availability Zone for resources that require direct Internet connectivity or for NAT Gateway deployment as the architecture expands. |
| **Private (application)** | 2 | One private subnet per Availability Zone for Lambda or other backend resources that require network isolation. These resources do not receive direct inbound traffic from the Internet. |

For the current CloudMenu architecture, public and private subnets are only required if Lambda or other backend resources need VPC connectivity. This avoids introducing unnecessary VPC complexity when it is not required.

### Network Flow

The main CloudMenu access flows are organized as follows:

**Customer / Kitchen / Dashboard → CloudFront → S3**

for static frontend assets, and:

**Customer / Kitchen / Dashboard → API Gateway → Lambda → DynamoDB**

for API and data-related requests.

Within these flows:

- CloudFront serves as the frontend distribution layer.
- S3 acts as the origin for frontend files and static assets.
- API Gateway serves as the public API entry point.
- Lambda processes business logic and performs data operations.
- DynamoDB is accessed through the backend and is not directly exposed to clients.

Therefore, clients do not require direct access to the database or private backend resources.

### Security Group (application and data)

A Security Group is a stateful virtual firewall used by resources with network interfaces inside a VPC. Security Groups define allowed inbound and outbound traffic, while traffic that is not explicitly permitted is denied by default.

In the current serverless architecture, CloudMenu does not require Security Groups for S3, CloudFront, API Gateway, or DynamoDB. If Lambda is connected to a VPC, Security Groups can be used to control traffic between Lambda and private resources.

For example:

| Security Group | Role |
| :--- | :--- |
| Lambda Security Group | Attached to Lambda when deployed inside a VPC; controls outbound traffic from Lambda to required private resources. |
| Database/Private Resource Security Group | Used if the system is extended with a database or other services running inside the VPC; only allows traffic from the Lambda Security Group or other authorized sources. |
| VPC Endpoint Security Group | Used for Interface VPC Endpoints; allows HTTPS (TCP 443) traffic from VPC resources to AWS service endpoints. |

### VPC endpoint

A VPC Endpoint allows resources inside a VPC to communicate with supported AWS services through the AWS private network without routing traffic through the public Internet. This becomes useful when CloudMenu is expanded and Lambda is deployed inside a VPC.

Two main endpoint types can be used:

- Gateway Endpoint — commonly used for Amazon S3 and configured through route tables.
- Traffic to S3 can use the AWS private network instead of a NAT Gateway.
- Interface Endpoint — creates one or more network interfaces inside a subnet and provides private connectivity to supported AWS services.

When CloudMenu uses Lambda outside a VPC, VPC Endpoints are not required. If Lambda is moved into private subnets, appropriate endpoints can be introduced to reduce traffic through the NAT Gateway and improve network control.

| Resource | Type | Subnet / route table | Operational note |
| :--- | :--- | :--- | :--- |
| S3 VPC Endpoint | Gateway | Allows VPC resources to access S3 through the AWS private network. |
| CloudWatch Logs Endpoint | Interface | Allows VPC resources to send logs to CloudWatch Logs without fully relying on a NAT Gateway. |
| ECR API Endpoint | Interface | Provides access to the ECR API if CloudMenu is extended to a container-based backend. |
| ECR DKR Endpoint | Interface |Provides access to the ECR Docker registry when container images are used.|

### Networking Direction

During the testing phase, CloudMenu prioritizes a minimal serverless architecture, avoiding the deployment of VPCs, NAT Gateways, and multiple subnets unless there is a specific requirement. This reduces both infrastructure costs and operational complexity.

As the system grows, a VPC can be introduced to provide network isolation for backend resources or private databases. Lambda can then be deployed in private subnets and use NAT Gateway or VPC Endpoints depending on its Internet and AWS service connectivity requirements.

This approach allows CloudMenu to maintain a simple architecture at a small scale while providing a path toward stronger network isolation as security and scalability requirements increase.