# Cost Analysis — CI/CD Pipeline with DevSecOps & AWS ECS

## Overview

This document outlines the estimated AWS and third-party service costs for running the complete CI/CD pipeline as shown in the architecture. All AWS costs are based on **us-east-1 (N. Virginia)** region pricing and assume the infrastructure runs **24×7 for a full month (730 hours)** unless stated otherwise.

## 1. AWS EC2 Instances (Jenkins, SonarQube, Nexus)

Three EC2 instances are used to host Jenkins, SonarQube, and Nexus.

| Instance       | Type      | vCPU | RAM  | On-Demand Price/hr | Monthly Cost (730 hrs) |
|----------------|-----------|------|------|--------------------|------------------------|
| jenkins-server | t2.small  | 1    | 2 GB | $0.023             | ~$16.79                |
| sonar-server   | t2.medium | 2    | 4 GB | $0.0464            | ~$33.87                |
| Nexus-server   | t2.medium | 2    | 4 GB | $0.0464            | ~$33.87                |
| **Total EC2**  |           |      |      |                    | **~$84.53 / month**    |



## 2. AWS ECS Fargate (Application Runtime)

ECS Fargate runs the containerized application (`jenkins-container`) with no EC2 instances to manage.

| Resource          | Allocation       | Price              | Monthly Cost        |
|-------------------|------------------|--------------------|---------------------|
| vCPU              | 1 vCPU × 730 hrs | $0.04048 / vCPU-hr | ~$29.55             |
| Memory            | 2 GB × 730 hrs   | $0.004445 / GB-hr  | ~$6.49              |
| **Total Fargate** |                  |                    | **~$36.04 / month** |


## 3. AWS ECR (Elastic Container Registry)

ECR stores the Docker images pushed by Jenkins.

| Resource                                  | Usage                           | Price            | Monthly Cost       |
|-------------------------------------------|---------------------------------|------------------|--------------------|
| Storage                                   | ~1 GB (3 images × ~317 MB each) | $0.10 / GB-month | ~$0.10             |
| Data Transfer (push from EC2 same region) | Free                            | $0.00            | $0.00              |
| Data Transfer (ECS pull, same region)     | Free                            | $0.00            | $0.00              |
| **Total ECR**                             |                                 |                  | **~$0.10 / month** |


## 4. AWS Application Load Balancer (jenkins-cicd-LB)

The ALB (`jenkins-cicd-LB`) exposes the ECS container to the internet.

| Resource                           | Usage                | Price                 | Monthly Cost        |
|------------------------------------|----------------------|-----------------------|---------------------|
| ALB Hours                          | 730 hrs              | $0.008 / LCU-hr (min) | ~$5.84              |
| Load Balancer Capacity Units (LCU) | ~1 LCU (low traffic) | $0.008 / LCU-hr       | ~$5.84              |
| **Total ALB**                      |                      |                       | **~$11.68 / month** |

---

## 5. Amazon CloudWatch (Container Insights + Logs)

Container Insights was enabled for the ECS cluster, and ECS streams container logs to CloudWatch.

| Resource                        | Usage         | Price                  | Monthly Cost       |
|---------------------------------|---------------|------------------------|--------------------|
| Container Insights metrics      | 1 ECS cluster | $0.35 / metric / month | ~$3.50             |
| Log ingestion (627+ log events) | ~1 GB/month   | $0.50 / GB             | ~$0.50             |
| Log storage                     | ~1 GB         | $0.03 / GB-month       | ~$0.03             |
| **Total CloudWatch**            |               |                        | **~$4.03 / month** |

---

## 6. EBS Storage (EC2 Root Volumes)

Each EC2 instance has an EBS volume attached for OS and application data.

| Instance       | Estimated Volume | Type | Price            | Monthly Cost       |
|----------------|------------------|------|------------------|--------------------|
| jenkins-server | 20 GB            | gp2  | $0.10 / GB-month | ~$2.00             |
| sonar-server   | 20 GB            | gp2  | $0.10 / GB-month | ~$2.00             |
| Nexus-server   | 30 GB            | gp2  | $0.10 / GB-month | ~$3.00             |
| **Total EBS**  |                  |      |                  | **~$7.00 / month** |
 
---

## 7. Data Transfer
 
| Transfer Type                   | Direction         | Cost               |
|---------------------------------|-------------------|--------------------|
| EC2 → ECR (same region)         | Push Docker image | Free               |
| ECR → ECS Fargate (same region) | Pull image        | Free               |
| ALB → Internet (outbound)       | First 1 GB/month  | Free               |
| ALB → Internet (outbound)       | Beyond 1 GB       | $0.09 / GB         |
| **Estimated Total**             |                   | **~$1.00 / month** |

---


## Monthly Cost Summary

| Service           | Component                         | Estimated Monthly Cost |
|-------------------|-----------------------------------|------------------------|
| AWS EC2           | jenkins-server (t2.small)         | ~$16.79                |
| AWS EC2           | sonar-server (t2.medium)          | ~$33.87                |
| AWS EC2           | Nexus-server (t2.medium)          | ~$33.87                |
| AWS ECS Fargate   | jenkins-container (1 vCPU, 2 GiB) | ~$36.04                |
| AWS ECR           | dockerimagerepo (3 images)        | ~$0.10                 |
| AWS ALB           | jenkins-cicd-LB                   | ~$11.68                |
| Amazon CloudWatch | Container Insights + Logs         | ~$4.03                 |
| AWS EBS           | 3 volumes (70 GB total)           | ~$7.00                 |
| Data Transfer     | Outbound internet                 | ~$1.00                 |
| GitHub            | Source code hosting               | $0.00                  |
| SonarQube         | Self-hosted Community             | $0.00                  |
| Nexus Repository  | Self-hosted Community             | $0.00                  |
| Slack             | Free workspace                    | $0.00                  |
| **TOTAL**         |                                   | **~$144.38 / month**   |

---
