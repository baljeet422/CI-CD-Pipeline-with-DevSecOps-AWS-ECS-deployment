# Nexus Repository Setup & Configuration Documentation

## Overview

**Sonatype Nexus Repository** is used in this project as the **centralized artifact management system**. After Jenkins builds the application, the generated `.war` artifact is automatically uploaded to Nexus. This ensures every build is versioned, stored, and traceable — and can be pulled for deployment at any time.

---

## Step 1 — Nexus Installation on EC2

Nexus was installed on the dedicated EC2 instance (`Nexus-server`, t2.medium).

**Steps followed:**
1. SSH into the Nexus EC2 instance
2. Installed Java (Nexus requires Java 17)
3. Downloaded and extracted Nexus:
   ```bash
   wget https://download.sonatype.com/nexus/3/nexus-3.x.x-unix.tar.gz
   tar -xvf nexus-3.x.x-unix.tar.gz
   ```
4. Created a `nexus` user and set ownership
5. Configured Nexus to run as a service
6. Started Nexus:
   ```bash
   sudo systemctl start nexus
   sudo systemctl enable nexus
   ```
7. Accessed UI at `http://<nexus-ip>:8081`

---

## Step 2 — Repository Creation

A **hosted raw/Maven repository** named `jenkinnexus-repo` was created to store build artifacts uploaded by Jenkins.

**Steps followed:**
1. Logged into Nexus UI → `Administration` → `Repositories` → `Create repository`
2. Selected repository type: **maven2 (hosted)**
3. Configured:

| Field                 | Value              |
|-----------------------|--------------------|
| **Repository Name**   | `jenkinnexus-repo` |
| **Type**              | Hosted             |
| **Format**            | Maven 2 / Raw      |
| **Version Policy**    | Mixed (or Release) |
| **Deployment Policy** | Allow Redeploy     |
| **Storage**           | default            |

4. Saved the repository

---

## Step 3 — Nexus Credentials Added to Jenkins

So Jenkins can upload artifacts to Nexus, the Nexus credentials were added to Jenkins.

**Steps followed:**
1. Jenkins → `Manage Jenkins` → `Credentials`
2. Added new credential:
   - **Kind:** Username with password
   - **Username:** `admin`
   - **Password:** `<nexus-password>`
   - **ID:** `nexus-credentials` *(referenced in Jenkinsfile)*
3. Nexus server URL also configured in `pom.xml` or Jenkinsfile pipeline stage

---

## Step 4 — Artifact Upload from Jenkins

After a successful build and Quality Gate pass in SonarQube, Jenkins automatically uploads the `.war` artifact to Nexus.









