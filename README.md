# DevOps Deployment Portal

An event-driven, automated deployment platform built on AWS that enables developers to deploy web applications by uploading deployment packages to Amazon S3. The solution leverages AWS Lambda, AWS Systems Manager, Auto Scaling Groups, and Application Load Balancers to automate application deployment without requiring manual server access.

---

## Project Overview

This project demonstrates an event-driven automated deployment pipeline on Amazon Web Services (AWS). The solution automates application deployments by triggering an AWS Lambda function whenever a deployment package (.zip) is uploaded to an Amazon S3 bucket.

The Lambda function invokes AWS Systems Manager (SSM) Run Command to remotely deploy the application on Amazon EC2 instances running inside an Auto Scaling Group (ASG). The deployed application is served through an Application Load Balancer (ALB), providing high availability and scalability.

This project eliminates manual deployments and SSH-based server management by using native AWS services to build an automated deployment workflow.

---

# Project Objectives

- Automate application deployments without manual server access.
- Build an event-driven deployment pipeline using AWS services.
- Implement scalable infrastructure using Auto Scaling Groups.
- Improve application availability using an Application Load Balancer.
- Secure infrastructure using IAM roles and AWS Systems Manager.

---

# Features

- Automated deployment using Amazon S3 Event Notifications
- AWS Lambda based deployment automation
- Remote deployment using AWS Systems Manager Run Command
- Auto Scaling Group for scalable infrastructure
- Application Load Balancer for traffic distribution
- EC2 bootstrapping using Launch Templates and User Data
- Secure IAM Role-based permissions
- Fully automated deployment after uploading application artifacts
- No SSH required for application deployment

---

# Architecture

```
                        +----------------+
                        |   Developer    |
                        +-------+--------+
                                |
                                | Upload ZIP
                                |
                                ▼
                      +----------------------+
                      |      Amazon S3       |
                      |  (Artifacts Bucket)  |
                      +----------+-----------+
                                 |
                      Object Created Event
                                 |
                                 ▼
                      +----------------------+
                      |     AWS Lambda       |
                      +----------+-----------+
                                 |
                                 |
                  AWS Systems Manager Run Command
                                 |
                                 ▼
              +--------------------------------------+
              | Auto Scaling Group (EC2 Instances)   |
              |       Apache Web Server              |
              +----------------+---------------------+
                               |
                               |
                               ▼
                  +-----------------------------+
                  | Application Load Balancer   |
                  +--------------+--------------+
                                 |
                                 ▼
                            End Users
```

---

# AWS Services Used

| AWS Service | Purpose |
|-------------|---------|
| Amazon EC2 | Hosts the web application |
| Launch Template | Standardizes EC2 configuration |
| Auto Scaling Group | Automatically manages EC2 instances |
| Application Load Balancer | Distributes incoming traffic |
| Amazon S3 | Stores deployment artifacts |
| AWS Lambda | Automates deployment process |
| AWS Systems Manager | Executes deployment commands on EC2 |
| IAM | Manages secure permissions |
| CloudWatch | Lambda logging and monitoring |
| Security Groups | Controls inbound and outbound traffic |

---

# Deployment Workflow

### Step 1

Developer uploads a deployment package (.zip) into the **artifacts/** folder of the Amazon S3 bucket.

↓

### Step 2

Amazon S3 generates an **ObjectCreated** event.

↓

### Step 3

The event automatically triggers the AWS Lambda function.

↓

### Step 4

Lambda extracts the uploaded object's information and invokes AWS Systems Manager Run Command.

↓

### Step 5

Systems Manager remotely executes deployment commands on all EC2 instances inside the Auto Scaling Group.

↓

### Step 6

The deployment package is downloaded from S3.

↓

### Step 7

The application files replace the existing website inside:

```
/var/www/html
```

↓

### Step 8

Apache immediately starts serving the updated application.

↓

### Step 9

Users access the latest deployed application through the Application Load Balancer DNS.

---

# Project Structure

```
deployment-pipeline/

│
├── application/
│   ├── index.html
│   ├── css/
│   ├── js/
│   └── images/
│
├── lambda/
│   └── lambda_function.py
│
├── scripts/
│
├── README.md
│
└── architecture.png
```

---

# Prerequisites

- AWS Account
- Amazon EC2
- Amazon S3
- AWS Lambda
- AWS Systems Manager
- IAM
- Launch Template
- Auto Scaling Group
- Application Load Balancer
- Basic Linux knowledge
- Apache Web Server

---

# Deployment Steps

## 1. Create Amazon S3 Bucket

- Create an S3 bucket.
- Enable Event Notifications.
- Create the following folders:

```
artifacts/
scripts/
logs/
```

---

## 2. Create IAM Roles

Create:

- EC2 IAM Role
- Lambda Execution Role

Attach the required permissions for:

- Amazon S3
- Systems Manager
- CloudWatch
- EC2

---

## 3. Launch EC2

- Create Launch Template.
- Install Apache using User Data.
- Install:

```
apache2
awscli
unzip
```

---

## 4. Create Auto Scaling Group

- Use Launch Template.
- Configure Desired Capacity.
- Attach Target Group.

---

## 5. Create Application Load Balancer

- Internet Facing
- HTTP Listener
- Register Target Group
- Configure Health Checks

---

## 6. Configure Systems Manager

- Attach AmazonSSMManagedInstanceCore role.
- Verify Managed Instance status.

---

## 7. Create Lambda Function

Configure Lambda to:

- Receive S3 events
- Identify uploaded ZIP
- Invoke Systems Manager Run Command

---

## 8. Configure S3 Event Notification

Trigger:

```
ObjectCreated
```

Folder:

```
artifacts/
```

Suffix:

```
.zip
```

Destination:

```
AWS Lambda
```

---

## 9. Upload Application

Upload:

```
application.zip
```

or any deployment ZIP into:

```
artifacts/
```

The deployment starts automatically.

---

# Testing

The following tests were successfully completed.

✅ Upload Version 1

- Lambda triggered
- SSM executed
- Application deployed
- Website accessible through ALB

---

✅ Upload Version 2

- Automatic deployment triggered
- Existing application replaced
- Website updated successfully

---

✅ Upload multiple deployment packages

- Every uploaded ZIP triggered deployment successfully.

---

# Troubleshooting

## ALB Target Unhealthy

Issue:

Target group reported unhealthy instances.

Resolution:

Configured Apache installation using EC2 User Data and corrected Health Check configuration.

---

## AWS CLI Missing

Issue:

```
aws: command not found
```

Resolution:

Installed:

```
awscli
```

inside Launch Template User Data.

---

## unzip Missing

Issue:

```
unzip: command not found
```

Resolution:

Installed:

```
unzip
```

using User Data.

---

## Apache Service Missing

Issue:

```
apache2 service not found
```

Resolution:

Installed and enabled Apache during EC2 initialization.

---

## Lambda Permission Error

Issue:

Lambda could not invoke Systems Manager.

Resolution:

Updated IAM policy with:

- ssm:SendCommand
- ssm:GetCommandInvocation

---

## Auto Scaling Refresh

Issue:

Existing instances did not receive updated User Data.

Resolution:

Created a new Launch Template version and initiated an Instance Refresh.

---

# Future Enhancements

- HTTPS using AWS Certificate Manager
- Route 53 custom domain
- CloudFront CDN
- SNS deployment notifications
- CloudWatch Alarms
- Blue-Green deployment strategy
- Infrastructure provisioning using Terraform
- CI/CD integration with GitHub Actions

---

# Screenshots

Add screenshots of:

- Project Architecture
- Amazon S3 Bucket
- Lambda Function
- Systems Manager Run Command
- EC2 Instances
- Launch Template
- Auto Scaling Group
- Target Group
- Application Load Balancer
- Lambda CloudWatch Logs
- Running Application
- AWS Console Overview

---

# Skills Demonstrated

- Amazon EC2
- Auto Scaling Groups
- Launch Templates
- Application Load Balancer
- Amazon S3
- AWS Lambda
- AWS Systems Manager
- IAM
- CloudWatch
- Linux Administration
- Apache Web Server
- Event-Driven Architecture
- Infrastructure Automation
- Deployment Automation
- AWS Networking
- Troubleshooting

---

# Author

**Vishwanath Veerapur**

Cloud & DevOps Enthusiast

GitHub:
https://github.com/Vishwa0456

LinkedIn:
https://www.linkedin.com/in/vishwanath-veerapur-04b643371

---

# License

This project is created for learning and portfolio purposes.
