# AWS ECS (Elastic Container Service) Setup & Configuration

## Overview

**Amazon Elastic Container Service (ECS)** with **AWS Fargate** is used in this project to run the containerized application without managing any servers. Jenkins pushes the Docker image to ECR, and ECS automatically pulls and runs it. The application is exposed to the internet via an **Application Load Balancer (ALB)**.

---

## Final Result — Application Live on Browser

The deployed application is accessible via the Load Balancer DNS endpoint:

```
http://jenkins-cicd-lb-497733968.us-east-1.elb.amazonaws.com
```

> The app login page is live and accessible from any browser through the ALB endpoint. ✅

---

## Architecture Overview

```
Jenkins Pipeline
        ↓
Docker Image pushed to ECR (dockerimagerepo)
        ↓
ECS Task Definition (jenkins-cicd-task:1)
pulls image from ECR URI
        ↓
ECS Service (jenkins-cicd-service)
runs Task on Fargate cluster (jenkins-ECScluster)
        ↓
Container: jenkins-container → Port 8080
        ↓
Target Group (jenkins-cicd-TG) → Port 8080
        ↓
Application Load Balancer (jenkins-cicd-LB) → Port 80
        ↓
User accesses app via ALB DNS in browser
```

---

## Step 1 — Create ECS Cluster

An ECS cluster named `jenkins-ECScluster` was created as the environment where all containers run.

**Steps followed:**
1. AWS Console → `ECS` → `Clusters` → `Create Cluster`
2. Configured:

| Field                   | Value                                  |
|-------------------------|----------------------------------------|
| **Cluster Name**        | `jenkins-ECScluster`                   |
| **Infrastructure Type** | AWS Fargate (Serverless)               |
| **Container Insights**  |  Enabled (sends metrics to CloudWatch) |
| **Region**              | us-east-1                              |

3. Clicked **Create**

> **Why Fargate?** No EC2 instances to manage — AWS handles the underlying infrastructure. You only define CPU/memory for the container.

> **Why Container Insights?** Enables CPU utilization, memory utilization, Network RX/TX metrics visible directly in CloudWatch and ECS Health dashboard.

---

## Step 2 — Create Task Definition

A **Task Definition** acts as a blueprint for the container — it defines what image to run, how much CPU/memory to give it, and what role it uses.

**Steps followed:**
1. ECS → `Task Definitions` → `Create new task definition`
2. Configured:

### Task Definition Overview

| Field | Value |
|-------|-------|
| **Task Definition Name**| `jenkins-cicd-task`                                                      |
| **ARN**                 | `arn:aws:ecs:us-east-1:829734436409:task-definition/jenkins-cicd-task:1` |
| **Status**              |  ACTIVE                                                                  |
| **App Environment**     | Fargate                                                                  |
| **OS / Architecture**   | Linux / X86_64                                                           |
| **Network Mode**        | awsvpc                                                                   |
| **Task CPU**            | 1,024 units (1 vCPU)                                                     |
| **Task Memory**         | 2,048 MiB (2 GiB)                                                        |
| **Task Execution Role** | `ecsTaskExecutionRole`                                                   |

### Container Configuration

| Field              | Value                                                                     |
|--------------------|---------------------------------------------------------------------------|
| **Container Name** | `jenkins-container`                                                       |
| **Image URI**      | `<aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/dockerimagerepo:latest` |
| **Port Mapping**   | 8080 (TCP)                                                                |

### Task Execution Role — `ecsTaskExecutionRole`

The `ecsTaskExecutionRole` was configured with the following permissions so ECS can pull the image from ECR and send logs to CloudWatch:

| Permission                        | Policy                                                       |
|-----------------------------------|--------------------------------------------------------------|
| Pull image from ECR               | `AmazonECRReadOnly` / `AmazonEC2ContainerRegistryFullAccess` |
| Send container logs to CloudWatch | `CloudWatchLogsFullAccess`                                   |
| Basic ECS task execution          | `AmazonECSTaskExecutionRolePolicy`                           |

## Step 3 — Create Security Group for ECS & Load Balancer

A security group `ecselb-sg` was created to control traffic to the ECS service and Load Balancer.

| Rule      | Type        | Protocol | Port | Source               | Purpose                    |
|-----------|-------------|----------|------|----------------------|----------------------------|
| Inbound 1 | Custom TCP  | TCP      | 8080 | 0.0.0.0/0 (Anywhere) | Direct container access    |
| Inbound 2 | HTTP        | TCP      | 80   | 0.0.0.0/0 (Anywhere) | Load Balancer HTTP traffic |
| Outbound  | All traffic | All      | All  | 0.0.0.0/0            | Allow all outbound         |

---

## Step 4 — Create Load Balancer & Target Group

An **Application Load Balancer (ALB)** was set up to distribute traffic to the ECS container and provide a stable public endpoint.

### Target Group — `jenkins-cicd-TG`

| Field                 | Value                             |
|-----------------------|-----------------------------------|
| **Target Group Name** | `jenkins-cicd-TG`                 |
| **Target Type**       | IP (Fargate tasks use IP targets) |
| **Protocol**          | HTTP                              |
| **Port**              | 8080                              |
| **Health Check Path** | `/`                               |

### Load Balancer — `jenkins-cicd-LB`

| Field                  | Value                                                   |
|------------------------|---------------------------------------------------------|
| **Load Balancer Name** | `jenkins-cicd-LB`                                       |
| **Type**               | Application Load Balancer (ALB)                         |
| **Scheme**             | Internet-facing                                         |
| **Listener**           | HTTP : 80                                               |
| **Forward To**         | `jenkins-cicd-TG` (port 8080)                           |
| **Security Group**     | `ecselb-sg`                                             |
| **DNS Endpoint**       | `jenkins-cicd-lb-497733968.us-east-1.elb.amazonaws.com` |

---

## Step 5 — Create ECS Service

An ECS **Service** ensures the task always runs and handles new deployments via rolling update.

**Steps followed:**
1. ECS → `jenkins-ECScluster` → `Services` → `Create`
2. Configured:

| Field                         | Value |
|-------------------------------|--------------------------------------------------------------------------------------|
| **Service Name**              | `jenkins-cicd-service`                                                               |
| **ARN**                       | `arn:aws:ecs:us-east-1:829734436409:service/jenkins-ECScluster/jenkins-cicd-service` |
| **Task Definition**           | `jenkins-cicd-task:1`                                                                |
| **Desired Tasks**             | 1                                                                                    |
| **Deployment Strategy**       | Rolling Update                                                                       |
| **Status**                    |  Active                                                                              |
| **Deployment Status**         |  Success                                                                             |
| **Tasks Running**             | 0 Pending \| 1 Running                                                               |
| **Load Balancer**             | `jenkins-cicd-LB` (Application Load Balancer)                                        |
| **Container : Port**          | `jenkins-container:8080`                                                             |
| **Target Group**              | `jenkins-cicd-TG`                                                                    |
| **Target Health**             |  1 Healthy \| 0 Unhealthy                                                            |
| **Health Check Grace Period** | 0 seconds                                                                            |

> **Rolling Update** means when Jenkins pushes a new image, ECS replaces the old container with the new one gradually — zero downtime deployment.

---

## Step 6 — Container Insights Metrics (CloudWatch)

With Container Insights enabled, the ECS service health is monitored in real time.

| Metric                       | Value (Observed)         |
|------------------------------|--------------------------|
| **CPU Utilization (Max)**    | 42.1%                    |
| **CPU Utilization (Avg)**    | ~0% (idle after startup) |
| **Memory Utilization (Max)** | 15.97%                   |
| **Memory Utilization (Avg)** | ~15.97% (stable)         |
| **Network RX (Max)**         | 1.211k Bytes/Second      |
| **Network TX (Max)**         | 25.4k Bytes/Second       |

> Two deployment events are visible in the charts at `2026-02-14 12:48:30` and `2026-02-14 14:16:10` — these correspond to new container starts triggered by Jenkins pipeline runs.

---

## Step 7 — Container Logs (CloudWatch)

ECS streams container logs to **CloudWatch Logs** automatically via the `ecsTaskExecutionRole`.

| Detail               | Value                                  |
|----------------------|----------------------------------------|
| **Task ID**          | `4f8643f12797467e88479f6baf737375`     |
| **Container**        | `jenkins-container`                    |
| **Total Log Events** | 627                                    |
| **Log Viewer**       | CloudWatch Logs / CloudWatch Live Tail |
