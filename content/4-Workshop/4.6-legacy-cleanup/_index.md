---
title : "Clean up"
date : 2024-01-01
weight : 99
chapter : false
pre : " <b> 4.5. </b> "
draft : false
---

## 4.5 Clean up

When the CloudMenu environment is no longer needed on AWS, Terraform can be used to destroy the resources that were created and managed by Terraform. The terraform destroy command creates a destruction plan and removes the resources recorded in the Terraform state for the current environment.

For CloudMenu, the resources that may be destroyed include Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, IAM, and Amazon CloudWatch, depending on the actual Terraform configuration.

### Where to run it

Navigate to the directory containing the CloudMenu Terraform configuration, which should be the same directory used when running terraform apply.

First, check the current Terraform state:
```bash
terraform state list
```
This command displays the AWS resources currently managed by Terraform.

Then run:
```bash
terraform destroy
```
Terraform will generate a destruction plan and display the resources that are going to be deleted. Carefully review the list before confirming.

When Terraform prompts for confirmation:
```bash
Do you really want to destroy all resources?
Enter a value: yes
```
Enter:
```bash
yes
```
to start destroying the resources.

### Before you destroy

Before running terraform destroy, check the following:

- Ensure the AWS Account and AWS Region are correct for the CloudMenu environment being destroyed.
- Ensure Terraform is using the correct state for the environment that should be removed.
- Check the managed resources using terraform state list.
- If the CloudMenuOrders table contains important data, back up the data before running destroy.
- Make sure there are no users currently using CloudMenu during the cleanup process.

The current AWS account can be verified with:
```bash
aws sts get-caller-identity
```
The Terraform destruction plan can also be reviewed before actually deleting any resources:
```bash
terraform plan -destroy
```
This command shows the resources Terraform plans to remove without performing the destruction.

### After destroy

After the destruction process is completed, the Terraform state can be checked again:
```bash
terraform state list
```
If the entire stack has been successfully destroyed, the Terraform state should no longer contain the corresponding CloudMenu resources.

The AWS Management Console can also be used to verify that resources such as the following have been removed:

- Amazon S3 frontend bucket.
- Amazon CloudFront distribution.
- Amazon API Gateway.
- AWS Lambda functions.
- Amazon DynamoDB table CloudMenuOrders.
- CloudWatch Log Groups.
- IAM resources created by Terraform.

If terraform destroy fails during the process, review the error message, resolve the underlying issue, and run:
```bash
terraform destroy
```
again until the required resources have been successfully removed.