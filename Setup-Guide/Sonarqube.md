# SonarQube Setup & Configuration Documentation

## Overview

SonarQube is used in this project as the **code quality and security analysis tool**, integrated directly into the Jenkins CI/CD pipeline. Every code push triggers an automatic scan, and the pipeline only proceeds to build & deploy if the Quality Gate **passes**.

---

## Step 1 — Generate SonarQube Token

A **SonarQube authentication token** was generated so Jenkins can communicate with SonarQube securely without using a username/password.

**Steps followed:**
1. Logged into SonarQube → `My Account` → `Security`
2. Generated a new token (`sonar-token`)
3. Copied the token

---

## Step 2 — Add Token to Jenkins

The SonarQube token was added to Jenkins so the pipeline can authenticate with SonarQube during scans.

**Steps followed:**
1. Opened Jenkins → `Manage Jenkins` → `Credentials`
2. Added a new credential:
   - **Kind:** Secret Text
   - **Secret:** `<sonar-token-value>`
   - **ID:** `sonar-token` *(referenced in Jenkinsfile)*
3. Then went to `Manage Jenkins` → `Configure System` → `SonarQube Servers`
4. Added SonarQube server:
   - **Name:** `sonar-server`
   - **Server URL:** `http://<sonar-server-ip>:80`
   - **Token:** selected `sonar-token` credential

---

## Step 3 — Project Analysis

**Project analysed:** `jenkins-QG-pro`

| Metric                | Value  | Rating |
|-----------------------|--------|--------|
| **Quality Gate**      | Passed | —      |
| **Bugs**              | 30     | 🔴 E   |
| **Vulnerabilities**   | 0      | 🟢 A   |
| **Hotspots Reviewed** | 0.0%   | 🔴 E   |
| **Code Smells**       | 742    | 🟢 A   |
| **Coverage**          | 0.0%   | ⭕ —   |
| **Duplications**      | 2.2%   | 🟢 —   |
| **Lines of Code**     | 18k    | —       |

---

## Step 4 — Quality Gate Configuration

A **custom Quality Gate** was created to define pass/fail thresholds for the pipeline.

**Steps followed:**
1. SonarQube → `Quality Gates` → `Create`
2. Named the Quality Gate (e.g., `jenkins-QG-pro`)
3. Added conditions with threshold values:

| Condition         | Metric                     | Operator        | Threshold      |
|-------------------|----------------------------|-----------------|----------------|
| Security Issues   | Vulnerabilities            | is greater than | 0              |
| Reliability       | Bugs                       | is greater than | *(value)*  |
| Security Hotspots | Security Hotspots Reviewed | is less than    | 100%           |
| Maintainability   | Code Smells                | is greater than | *(value)*  |
| Coverage          | Coverage                   | is less than    | *(value)*% |

4. Set this Quality Gate as **default** for the project `jenkins-QG-pro`

---

## Step 5 — Webhook Configuration

A **webhook** named `jenkins-ci-webhook` was configured in SonarQube so that after every analysis, SonarQube **automatically sends the Quality Gate result back to Jenkins**.

**Steps followed:**
1. SonarQube → `Administration` → `Configuration` → `Webhooks`
2. Clicked `Create` and filled in:

| Field      | Value                                                |
|------------|------------------------------------------------------|
| **Name**   | `jenkins-ci-webhook`                                 |
| **URL**    | `http://<jenkins-server-ip>:8080/sonarqube-webhook/` |
| **Secret** | --                                                   |

3. Saved the webhook


