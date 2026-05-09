# AWS ECR (Elastic Container Registry) Setup & Configuration

## Overview

**Amazon Elastic Container Registry (ECR)** is used in this project as the **private Docker image registry**. After Jenkins builds the Docker image of the application, it is tagged and pushed to ECR. AWS ECS then pulls the image directly from ECR to run the containerized application.

---

## ECR Repository Details

| Detail              | Value                                               |
|---------------------|-----------------------------------------------------|
| **Registry Type**   | Private                                             |
| **Repository Name** | `dockerimagerepo`                                   |
| **Region**          | us-east-1 (N. Virginia)                             |
| **Total Images**    | 3                                                   |
| **Registry**        | Amazon Elastic Container Service → Private Registry |


## Step 1 — Install AWS CLI on Jenkins Server

AWS CLI was installed on the Jenkins EC2 instance so the Jenkins pipeline can authenticate with AWS and push Docker images to ECR.

**Steps followed:**

```bash

# Run the cnd
sudo snap install aws-cli --classic


## Step 2 — Create IAM User for Jenkins

A dedicated **IAM user named `Jenkins`** was created in AWS with the permissions needed to interact with ECR and ECS.

**Steps followed:**
1. AWS Console → `IAM` → `Users` → `Create User`
2. Username: **`Jenkins`**
3. Access type: **Programmatic access** (generates Access Key ID + Secret Access Key)
4. Attached the following managed policies directly:

| Policy Name                            | Purpose                                  |
|----------------------------------------|------------------------------------------|
| `AmazonEC2ContainerRegistryFullAccess` | Push/pull Docker images to/from ECR      |
| `AmazonECS_FullAccess`                 | Deploy and manage ECS tasks and services |

5. Clicked **Create User** → downloaded the credentials CSV

## Step 3 — Add AWS Credentials to Jenkins

The IAM user's access keys were added to Jenkins so the pipeline can authenticate with AWS during `docker push` to ECR.

**Steps followed:**
1. Jenkins → `Manage Jenkins` → `Credentials` → `Global` → `Add Credentials`
2. Configured:

| Field                 | Value                             |
|-----------------------|-----------------------------------|
| **Kind**              | AWS Credentials                   |
| **ID**                | `awscreds`                        |
| **Description**       | AWS IAM Jenkins user credentials  |
| **Access Key ID**     | `<IAM-Jenkins-access-key-id>`     |
| **Secret Access Key** | `<IAM-Jenkins-secret-access-key>` |

3. Saved the credential


## Step 4 — Create ECR Private Repository

A private ECR repository named `dockerimagerepo` was created to store Docker images built by Jenkins.

**Steps followed:**
1. AWS Console → `Elastic Container Registry` → `Private Registry` → `Repositories` → `Create repository`
2. Configured:

| Field                | Value                                                |
|----------------------|------------------------------------------------------|
| **Visibility**       | Private                                              |
| **Repository Name**  | `dockerimagerepo`                                    |
| **Tag Immutability** | Disabled (allows `latest` tag to be overwritten)     |
| **Scan on Push**     | Optional (can be enabled for vulnerability scanning) |
| **Encryption**       | AES-256 (default)                                    |

3. Clicked **Create repository**
4. Repository URI format:
```
<aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/dockerimagerepo
```