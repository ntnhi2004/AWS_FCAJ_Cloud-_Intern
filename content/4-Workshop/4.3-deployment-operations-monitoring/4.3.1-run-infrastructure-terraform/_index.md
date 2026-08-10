---
title: "Run infrastructure with Terraform"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.3.1 </b> "
aliases:
  - /4-workshop/4.3-deployment-operations-monitoring/4.3.1-vpc-network/
---

## 4.3.1 Run infrastructure with Terraform

This page presents the process of using Terraform to deploy and manage the CloudMenu infrastructure on AWS. The process includes cloning the repository, configuring AWS credentials, preparing the Terraform configuration, and running the init, validate, plan, and apply commands.

The deployment follows the serverless architecture described in Section 4.2, with the main AWS services used by CloudMenu including Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, AWS IAM, and Amazon CloudWatch.

### Prerequisites

Before deploying the Terraform configuration, the following tools should be installed: Terraform, AWS CLI, and Git

The AWS CLI must be configured with an AWS account or profile that has sufficient permissions to create and manage the resources used by CloudMenu.

AWS CLI can be configured using:
```bash
aws configure
```
Alternatively, an AWS CLI profile or environment variables containing the required AWS credentials can be used.

After configuration, verify the current AWS account with:
```bash
aws sts get-caller-identity
```
This command helps confirm that Terraform will deploy resources to the intended AWS account.

### Clone the Repository

Clone the CloudMenu source code from GitHub:
```bash
git clone https://github.com/NhuLuk/CloudMenu-AWS
cd CloudMenu-AWS
```
Then open the project in an IDE or editor and locate the directory containing the Terraform configuration.

### Preparation

Before deployment, review the Terraform configuration and identify the AWS resources that will be created.

The CloudMenu infrastructure is organized into the following main layers:

| Lớp | AWS Services | Mục đích |
| :--- | :--- | :--- |
| **Frontend** | Amazon S3, Amazon CloudFront | Store and distribute the Customer, Kitchen, and Manager interfaces |
| **API** | Amazon API Gateway | Receive and route HTTP API requests |
| **Compute** | AWS Lambda | Execute backend business logic |
| **Data** | Amazon DynamoDB | Store order data in the CloudMenuOrders table |
| **Security** | AWS IAM | Control permissions between AWS services |
| **Monitoring** | Amazon CloudWatch | Collect Lambda logs and monitor system activity |

Environment-specific values should be configured through Terraform variables rather than hard-coded directly into AWS resources.

Sensitive information such as AWS credentials, access keys, and secret keys must not be stored in the source code or committed to GitHub.

### Initialize Terraform

Navigate to the directory containing the Terraform configuration and run:
```bash
terraform init
```
This command initializes the Terraform working directory and downloads the required providers for the deployment.

### Validate the Configuration

Before creating AWS resources, validate the Terraform configuration:
```bash
terraform validate
```
If validation succeeds, the Terraform configuration has valid syntax and no basic structural errors have been detected.

### Review the Deployment Plan

Before applying the configuration, use:
```bash
terraform plan
```
The terraform plan command shows which AWS resources Terraform plans to create, modify, or remove. This step is useful for reviewing the deployment before making actual changes to the AWS environment.

### Deploy the Infrastructure

After confirming that the deployment plan is correct, run:
```bash
terraform apply
```
Terraform will request confirmation before creating or modifying the AWS resources.

After confirmation, Terraform deploys the required AWS resources according to the dependencies defined in the configuration.

For CloudMenu, the deployment may include resources such as:

- Amazon S3 bucket for frontend files
- Amazon CloudFront distribution
- Amazon API Gateway APIs
- AWS Lambda functions for order operations
- Amazon DynamoDB CloudMenuOrders table
- IAM roles and policies
- Amazon CloudWatch log groups

### Check Terraform Outputs

After a successful deployment, use:
```bash
terraform output
```
The actual outputs depend on the Terraform configuration and AWS account. Example outputs may include:
```bash
aws_region = "ap-southeast-1"
s3_bucket_name = "cloudmenu-frontend"
cloudfront_distribution_id = "XXXXXXXXXXXXXX"
cloudfront_domain_name = "xxxxxxxxxxxxxx.cloudfront.net"
api_gateway_endpoint = "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/..."
dynamodb_table_name = "CloudMenuOrders"
```
The values above are illustrative and will vary depending on the AWS account, region, and deployment.

The CloudFront domain name can be used to access the deployed CloudMenu frontend. The frontend contains three main interfaces: Customer, Kitchen, and Manager.

