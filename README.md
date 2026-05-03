
![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Containerization-2496ED?logo=docker&logoColor=white)
![SonarQube](https://img.shields.io/badge/SonarQube-Code%20Quality-4E9BCD?logo=sonarqube&logoColor=white)
![Amazon ECS](https://img.shields.io/badge/Amazon%20ECS-Container%20Orchestration-FF9900?logo=amazonaws&logoColor=white)
![Shell](https://img.shields.io/badge/Shell-Script-4EAA25?logo=gnubash&logoColor=white)
![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![License](https://img.shields.io/badge/License-MIT-yellow)

# CI-CD-Pipeline-with-DevSecOps-AWS-ECS-deployment

# Project Overview
This project implements a **fully automated CI/CD pipeline** integrated with **DevSecOps practices** to build, test, analyze, containerize, and deploy a Java web application to **AWS ECS** (Elastic Container Service).
 
The pipeline is triggered on every code push to GitHub and moves the application through multiple stages — from code quality checks and security analysis to Docker image creation and ECS deployment — with zero manual intervention.

# AWS Services Used
| Service     | Purpose                                 |
|-------------|-----------------------------------------|
| **AWS ECS** | Runs and orchestrates Docker containers |
| **AWS ECR** | Private Docker image registry           |
| **AWS EC2** | Hosts Jenkins and SonarQube servers     |
| **AWS IAM** | Manages permissions and service roles   |
| **AWS VPC** | Network isolation for all services      |
 
# Architecture-diagram

```mermaid
graph LR
    subgraph Development["Development"]
        Dev[Developer <br/> git] --> GitHub((GitHub))
    end

    subgraph CI_Server["Jenkins"]
        GitHub --> Fetch[Jenkins - <br/> Fetch Code]
        Fetch --> Maven[MAVEN <br/> Unit & Checkstyle]
    end

    subgraph Code_Quality["SonarQube"]
        Maven --> SQ[SonarQube <br/> Analysis]
        SQ --> QG[Quality Gates <br/> Check]
    end

    subgraph Artifact_Creation["Artifact Creation"]
        QG --> Docker[Docker Build <br/> Containerize]
        Docker --> ECR[AWS ECR <br/> Publish]
    end

    subgraph Deployment["AWS Deployment"]
        ECR --> ECS[AWS ECS <br/> Run]
    end

    %% Styling
    style Dev fill:#1f2937,stroke:#3b82f6,color:#fff
    style GitHub fill:#1f2937,stroke:#3b82f6,color:#fff
    style CI_Server fill:#0f172a,stroke:#3b82f6,color:#fff
    style Code_Quality fill:#0f172a,stroke:#3b82f6,color:#fff
    style Artifact_Creation fill:#0f172a,stroke:#3b82f6,color:#fff
    style Deployment fill:#0f172a,stroke:#3b82f6,color:#fff