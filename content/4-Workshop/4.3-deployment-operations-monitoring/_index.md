---
title: "Terraform Deployment (Layered Approach)"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

## 4.3 Terraform Deployment (Layered Approach)

This chapter presents the Infrastructure as Code (IaC) deployment process for the CloudMenu system using Terraform. The content focuses on environment preparation, AWS configuration, and deployment of infrastructure components according to the layers of the serverless architecture.

The current CloudMenu architecture uses the following main AWS services: Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, AWS IAM, and Amazon CloudWatch. Therefore, Terraform is used to manage and standardize AWS resources instead of manually configuring each component through the AWS Management Console.

### Workshop flow

1. **Clone the repository.** Clone the CloudMenu source code from the GitHub repository: **https://github.com/NhuLuk/CloudMenu-AWS**.

```bash
git clone https://github.com/NhuLuk/CloudMenu-AWS.
```

2. **Install the required tools** Terraform, AWS CLI, and Git. Then configure AWS credentials for the lab account using aws configure or appropriate environment variables.

3. **Open the project in an editor:** After cloning the repository, open the project using an IDE/editor. The Terraform configuration is organized separately from the application source code to simplify infrastructure management and deployment.

4. **Verify the AWS configuration:** Before deployment, verify the AWS account and region being used. Sensitive information such as AWS credentials, access keys, or secret keys must not be stored directly in the source code or committed to GitHub.

5. **Initialize Terraform:** Navigate to the directory containing the Terraform configuration and run: 'terraform init' . This command initializes the Terraform working directory and downloads the required providers and modules.

6. **Review the deployment plan:** Run: 'terraform plan' to review the AWS resources that Terraform plans to create, modify, or destroy.

7. **Deploy the infrastructure: **After reviewing the plan and confirming that the configuration is correct, run: 'terraform apply' . Terraform then creates the required resources according to the dependency graph defined in the configuration.

### Why use Terraform?

- Infrastructure as Code: AWS infrastructure is defined as code, making it easier to review, version-control, and redeploy.
- Consistency: Resources such as S3, CloudFront, API Gateway, Lambda, and DynamoDB can be provisioned using consistent configurations across different environments.
- Reduced Manual Operations: Terraform reduces the need to manually create and configure each AWS resource through the AWS Management Console.
- Dependency Management: Terraform automatically determines the appropriate resource creation order based on dependencies between resources.
- Scalability: Additional AWS services can be incorporated into the Terraform configuration as CloudMenu evolves.

### Content

1. [4.3.1 Run infrastructure with Terraform](4.3.1-run-infrastructure-terraform/)
2. [4.3.2 Frontend hosting](4.3.2-frontend-hosting/)