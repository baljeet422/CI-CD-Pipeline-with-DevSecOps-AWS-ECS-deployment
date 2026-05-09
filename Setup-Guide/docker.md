# Docker Setup & Configuration Documentation

## Overview

Docker is installed on the **Jenkins server** to enable containerization as part of the CI/CD pipeline. After a successful build, SonarQube scan, and Nexus artifact upload, Jenkins uses Docker to **build a container image** of the application and **push it to AWS ECR** for deployment on AWS ECS.

---

## Step 1 — Install Docker Engine & Docker CLI

Docker Engine and Docker CLI were installed on the Jenkins EC2 instance so Jenkins can build and push Docker images as part of the pipeline.

**Steps followed:**

```bash
# 1. Update system packages
sudo apt-get update

# 2. Install required dependencies
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# 3. Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
    sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 4. Set up the Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. Install Docker Engine and Docker CLI
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# 6. Enable & start Docker service
sudo systemctl enable docker
sudo systemctl start docker
```

**Verify installation:**

```bash
systemctl status docker
```

## Step 2 — Add Jenkins User to Docker Group

By default, Docker commands require `sudo`. Since Jenkins runs pipeline commands as the `jenkins` user, it needs permission to run Docker **without sudo**. This is done by adding `jenkins` to the `docker` group.

**Steps followed:**

```bash
# Add jenkins user to the docker group
sudo usermod -aG docker jenkins

# Verify the group was added
groups jenkins
# Expected output includes: jenkins : jenkins docker
```

**Then restart Jenkins to apply the group change:**

```bash
sudo systemctl restart jenkins
```

## Docker Role in the CI/CD Pipeline

```
Maven Build → .war artifact
        ↓
Artifact uploaded to Nexus
        ↓
Docker picks up .war → Dockerfile builds image
        ↓
docker build -t vproapp:latest .
        ↓
docker tag vproapp:latest <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/vproapp:latest
        ↓
docker push → AWS ECR
        ↓
AWS ECS pulls image → Deploys container
```
