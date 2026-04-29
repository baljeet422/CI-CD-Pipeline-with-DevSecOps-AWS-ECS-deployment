 CI-CD-Pipeline-with-DevSecOps-AWS-ECS-deployment

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
    ```