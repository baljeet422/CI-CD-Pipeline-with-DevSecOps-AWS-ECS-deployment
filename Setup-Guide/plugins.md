# Jenkins Plugins — Installation & Use Case Documentation

## Overview

Jenkins plugins extend the pipeline's capabilities to integrate with external tools like SonarQube, Nexus, AWS ECR, Docker, and Slack. All plugins below were installed via **Manage Jenkins → Plugins → Available Plugins** and downloaded successfully.

---

## Installed Plugins & Use Cases

---

### 1. Nexus Artifact Uploader

**What it does:**
Allows Jenkins to upload build artifacts (`.war`, `.jar`, `.zip`) directly to a **Sonatype Nexus Repository** from within the pipeline using the `nexusArtifactUploader` step.

**Why this project uses it:**
After Maven builds the `vprofile-v2.war` file, this plugin uploads it to `jenkinnexus-repo` on the Nexus server under a timestamped version path (`QA/vproapp/<build-timestamp>/`). This ensures every build artifact is versioned, stored, and retrievable.


---

### 2. SonarQube Scanner

**What it does:**
Integrates Jenkins with SonarQube to trigger **static code analysis** during the pipeline. Injects SonarQube server URL and authentication token automatically via `withSonarQubeEnv()`.

**Why this project uses it:**
After the Maven build, SonarQube scans the code for bugs, vulnerabilities, code smells, and coverage. The pipeline uses `waitForQualityGate` to pause and abort if the Quality Gate fails — preventing bad code from reaching Docker and ECS.

---

### 3. Pipeline Maven Integration

**What it does:**
Provides the `withMaven()` pipeline step, allowing Jenkins to run Maven goals (`clean`, `install`, `test`, `sonar:sonar`) directly inside the pipeline with automatic settings injection.

**Why this project uses it:**
The application is a Java/Maven project. This plugin enables Jenkins to build `vprofile-v2.war` from source code using Maven, run unit tests, and trigger SonarQube analysis — all within the pipeline stages.

---

### 4. Timestamp / Build Timestamp Plugin

**What it does:**
Provides `BUILD_TIMESTAMP` as an environment variable in the pipeline, formatted as a human-readable or custom date-time string.

**Why this project uses it:**
Used to create **unique versioned folder names** for Nexus artifact uploads (e.g., `vproapp-3-12-08-22:26:53.war`). Without this, every build would overwrite the same file — making artifact history impossible to track.

---

### 5. Slack Notification

**What it does:**
Sends build result notifications (SUCCESS / FAILURE / UNSTABLE / ABORTED) to a specified **Slack channel** using the `slackSend` step.

**Why this project uses it:**
The team gets real-time alerts on the `#devopscicd` channel in the `devlearn-corp` Slack workspace after every pipeline run — without having to check Jenkins manually. Color-coded messages (green/red/yellow) make it instantly clear if a deployment succeeded or failed.

---

### 6. AWS SDK :: All (aws-java-sdk)

**What it does:**
Bundles the complete **AWS Java SDK** inside Jenkins, giving pipeline steps the ability to interact with any AWS service (ECR, ECS, S3, IAM, etc.) programmatically.

**Why this project uses it:**
Required as a dependency for the AWS ECR plugin and Pipeline AWS Steps plugin. Without it, Jenkins cannot authenticate with AWS or make API calls to ECR and ECS during the pipeline.

---

### 7. Amazon ECR Plugin

**What it does:**
Handles **ECR authentication** automatically — generates a temporary Docker login token using AWS credentials (`aws ecr get-login-password`) and authenticates Docker before pushing images.

**Why this project uses it:**
Instead of manually running `aws ecr get-login-password` in a shell step, this plugin handles the ECR login seamlessly using the stored `awscreds` credential. It enables `docker push` to the private `dockerimagerepo` ECR registry.

---

### 8. CloudBees Docker Build and Publish

**What it does:**
Provides a simple UI-based and pipeline step (`dockerBuildAndPublish`) to **build a Docker image from a Dockerfile** and **publish it to a Docker registry** (ECR, Docker Hub, etc.) in one step.

**Why this project uses it:**
Simplifies the Docker build + tag + push workflow into a single declarative step in the Jenkinsfile. Handles tagging with build number and pushing to `dockerimagerepo` in ECR without writing multiple shell commands.

---

### 9. Docker Pipeline

**What it does:**
Provides the `docker` global variable and methods (`docker.build()`, `docker.withRegistry()`, `docker.image()`) for use directly inside **Declarative and Scripted Jenkinsfiles**.

**Why this project uses it:**
Enables full Docker lifecycle management within the Jenkinsfile — building images from the application Dockerfile, tagging with build numbers, authenticating with ECR, and pushing — all using clean, readable pipeline syntax rather than raw shell commands.

```


