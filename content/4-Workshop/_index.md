---
title: "Workshop"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 4. </b> "
---

# Workshop: SpendWiseApp Architecture on AWS

This workshop dives into the AWS infrastructure architecture of CloudMenu, covering the services used, network layers, security mechanisms, and how the system components interact with each other. Layered Terraform deployment (VPC, frontend, backend, and related components) is organized separately in the infrastructure deployment section, while the remaining AWS infrastructure components such as S3, CloudFront, API Gateway, Lambda, DynamoDB, and supporting services are covered in the architecture design section when needed.

The content is organized into the following main groups for easier reference:
- CloudMenu system overview and architecture.
- AWS infrastructure foundation, network layers, and security.
- Layered Terraform deployment, including frontend hosting with S3/CloudFront and backend deployment using a Serverless architecture.
- Operating costs, potential risks, and system scalability roadmap.

#### Content

1. [4.1 CloudMenu system overview and architecture](4.1-system-overview-architecture/)
2. [4.2 AWS infrastructure foundation, network layers, and security](4.2-aws-infrastructure-security/)
3. [4.3 Terraform deployment (by layer)](4.3-deployment-operations-monitoring/)
4. [4.4 Operating costs and potential risks](4.5-cost-risk-expansion-roadmap/)
5. [4.5 Clean up](4.6-legacy-cleanup/)
