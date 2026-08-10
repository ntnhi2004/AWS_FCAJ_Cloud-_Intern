---
title: "Frontend hosting"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 4.3.2 </b> "
aliases:
  - /4-workshop/4.3-deployment-operations-monitoring/4.3.2-frontend-hosting-auth/
---

## 4.3.2 Frontend hosting

### AWS Amplify Console after terraform apply

CloudMenu uses Amazon S3 to store frontend files and Amazon CloudFront to distribute the content to users over the Internet.

The CloudMenu frontend currently consists of three main interfaces:

Customer: An interface for customers to scan the QR code at their table and place food orders.
Kitchen: An interface for kitchen staff to view incoming orders and update their order status.
Manager: A dashboard interface for managers to monitor data and view statistics.

The frontend architecture is deployed using the following model:
![The frontend architecture](/images/4-Workshop/AWS_CloudMenu_Frontend.png)

When a user accesses the CloudFront domain, CloudFront retrieves the frontend files from S3 and distributes the content to the user's browser. Therefore, the frontend does not need to run directly from a development machine or an IDE such as VS Code.

### Frontend Deployment Flowl

CloudMenu currently does not use an automated CI/CD pipeline for frontend deployment. The deployment process is performed manually after the developer completes and builds the frontend source code.

The current deployment flow is:
![The current deployment flow](/images/4-Workshop/AWS_CloudMenu_Frontend2.png)

This approach is suitable for the current development and workshop stage because it is simple, easy to verify, and does not require setting up an additional CI/CD pipeline.

However, manual deployment has several limitations:

- The frontend must be built and uploaded manually after each update.
- It is easier to make mistakes, such as missing files or deploying an incorrect version.
- There is no automated source code validation process before deployment.
- Rollback to a previous frontend version is not automated.

### Verifying the Frontend After Deployment

After the frontend files have been uploaded to S3 and CloudFront has distributed the new version, the deployment can be verified using the CloudFront domain provided by AWS.

For example:

https://d3be9t7i3323e7.cloudfront.net/index.html?table=01

After successfully accessing the CloudFront URL, the CloudMenu interfaces can be tested individually:

- Customer: Access the menu by scanning the QR code associated with a table and create a new order.
![Customer](/images/4-Workshop/AWS_CloudMenu_Customer.png)

- Kitchen: Verify that newly created orders are displayed and update their status.
![Kitchen](/images/4-Workshop/AWS_CloudMenu_Kitchen.png)

- Manager: Access the dashboard and verify that order data and statistics are displayed correctly.
![Manager](/images/4-Workshop/AWS_CloudMenu_Manager.png)


If all three interfaces work correctly and the frontend can successfully communicate with the Amazon API Gateway endpoint, the frontend hosting deployment can be considered successful.