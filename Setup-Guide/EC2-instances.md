# AWS EC2 Instances Documentation

## Instance Overview

| # | Name           | Instance ID   | State   | Instance Type | Status Check      | Availability Zone |
|---|----------------|---------------|---------|---------------|-------------------|-------------------|
| 1 | jenkins-server | i-0f1ee277... | Running | t2.small      | 2/2 checks passed | us-east-1c        |
| 2 | Nexus-server   | i-0c9f3fd9... | Running | t2.medium     | 2/2 checks passed | us-east-1c        |
| 3 | sonar-server   | i-0e4e3985... | Running | t2.medium     | 2/2 checks passed | us-east-1c        |

---

## Detailed Instance Specifications

### 1. Jenkins Server

| Field                 | Value                                     |
|-----------------------|-------------------------------------------|
| **Name**              | jenkins-server                            |
| **Instance ID**       | i-0f1ee277...                             |
| **Instance State**    |  Running                                  |
| **Instance Type**     | t2.small                                  |
| **vCPUs**             | 1                                         |
| **RAM**               | 2 GB                                      |
| **Availability Zone** | us-east-1c                                |
| **Public IPv4 DNS**   | ec2-54-90-166-xxx.compute-1.amazonaws.com |
| **Security Group**    | jenkins-sg                                |
| **Open Ports**        | 22 (SSH), 8080 (Jenkins UI)               |
| **AMI / OS**          | `Ubuntu 22.04 LTS / Amazon Linux 2 `      |
| **Key Pair**          | ` jenkins-key.pem `                       |
| **EBS Storage**       | ` 20 GB gp2 `                             |


---

### 2. Nexus Server

| Field                 | Value                                      |
|-----------------------|--------------------------------------------|
| **Name**              | Nexus-server                               |
| **Instance ID**       | i-0c9f3fd9...                              |
| **Instance State**    |  Running                                   |
| **Instance Type**     | t2.medium                                  |
| **vCPUs**             | 2                                          |
| **RAM**               | 4 GB                                       |
| **Availability Zone** | us-east-1c                                 |
| **Public IPv4 DNS**   | ec2-54-204-129-xxx.compute-1.amazonaws.com |
| **Security Group**    | nexus-sg                                   |
| **Open Ports**        | 22 (SSH), 8081 (Nexus UI)                  |
| **AMI / OS**          | `Ubuntu 22.04 LTS / Amazon Linux 2 `       |
| **Key Pair**          | `nexus-key.pem `                           | 
| **EBS Storage**       | ` 30 GB gp2 `                              |


---

### 3. SonarQube Server

| Field                 | Value                                    |
|-----------------------|------------------------------------------|
| **Name**              | sonar-server                             |
| **Instance ID**       | i-0e4e3985...                            |
| **Instance State**    |  Running                                 |
| **Instance Type**     | t2.medium                                |
| **vCPUs**             | 2                                        |
| **RAM**               | 4 GB                                     |
| **Availability Zone** | us-east-1c                               |
| **Public IPv4 DNS**   | ec2-98-88-83-205.compute-1.amazonaws.com |
| **Security Group**    | sonar-sg (sg-080dd6e16f0d7b39a)          |
| **Open Ports**        | 22 (SSH), 80 (SonarQube UI)              |
| **AMI / OS**          | ` Ubuntu 22.04 LTS / Amazon Linux 2 `    |
| **Key Pair**          | `sonar-key.pem `                         |
| **EBS Storage**       | `20 GB gp2 `                             |


---



> **Why t2.medium for Nexus & SonarQube?**
> SonarQube requires more memory for code analysis, and Nexus needs storage bandwidth for managing Docker images and build artifacts — hence the larger instance type compared to Jenkins.

---

## Network Summary

| Server    | Public DNS                                 | Key Port |
|-----------|--------------------------------------------|----------|
| Jenkins   | ec2-54-90-166-xxx.compute-1.amazonaws.com  | :8080    |
| Nexus     | ec2-54-204-129-xxx.compute-1.amazonaws.com | :8081    |
| SonarQube | ec2-98-88-83-205.compute-1.amazonaws.com   | :80      |

---
