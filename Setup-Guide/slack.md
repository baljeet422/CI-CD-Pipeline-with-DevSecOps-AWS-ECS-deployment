# Slack Integration with Jenkins — Setup Documentation

## Overview

Slack is integrated into the Jenkins CI/CD pipeline to send **real-time build notifications** to the team. Every pipeline run — whether it succeeds, fails, or is aborted — automatically posts a message to the designated Slack channel, keeping the team instantly informed without checking Jenkins manually.

---

## Access

| Detail                      | Value              |
|-----------------------------|--------------------|
| **Platform**                | Slack              |
| **Workspace**               | `devlearn-corp`    |
| **Channel**                 | `#devopscicd`      |
| **App Added**               | Jenkins CI         |
| **Added By**                | Baljeet Kaur       |
| **Date Added**              | December 9th, 2025 |
| **Credential ID (Jenkins)** | `slacktoken`       |
| **Connection Test**         |  Success           |

---

## Step 1 — Sign Up & Create Slack Workspace

A Slack account and workspace were created to serve as the notification hub for the team.

**Steps followed:**
1. Went to [https://slack.com](https://slack.com) → clicked **Sign Up**
2. Signed up with email and verified the account
3. Created a new workspace:
   - **Workspace Name:** `devlearn-corp`
4. Slack generated the workspace URL: `devlearn-corp.slack.com`

---

## Step 2 — Create a Slack Channel

A dedicated channel was created specifically for CI/CD pipeline notifications.

**Steps followed:**
1. Inside the `devlearn-corp` workspace → clicked **Add channels** → **Create a channel**
2. Configured:

| Field            | Value                                     |
|------------------|-------------------------------------------|
| **Channel Name** | `devopscicd`                              |
| **Visibility**   | Public (or Private)                       |
| **Purpose**      | Receive Jenkins CI/CD build notifications |

3. Channel created: `#devopscicd`

---

## Step 3 — Add Jenkins CI App to Slack

The **Jenkins CI** app was added to the Slack workspace to enable integration.

**Steps followed:**
1. In Slack → went to `Apps` → searched for **Jenkins CI**
2. Clicked **Add to Slack**
3. On the configuration page:
   - Selected the channel to post notifications to: **`#devopscicd`**
   - Clicked **Add Jenkins CI Integration**
---

## Step 4 — Generate Slack Token

After adding the Jenkins CI app, Slack generated an **integration token** for authenticating Jenkins.

**Steps followed:**
1. On the Jenkins CI app configuration page in Slack → scrolled to **Token** section
2. Copied the generated token
3. The token format looks like: `xoxb-XXXXXXXXXXXX-XXXXXXXXXXXX-XXXXXXXXXXXXXXXXXXXXXXXX`
---

## Step 5 — Add Slack Token to Jenkins

The Slack token was added to Jenkins as a credential so the pipeline can authenticate with Slack.

**Steps followed:**
1. Jenkins → `Manage Jenkins` → `Credentials` → `Global` → `Add Credentials`
2. Configured:

| Field           | Value                       |
|-----------------|-----------------------------|
| **Kind**        | Secret Text                 |
| **Secret**      | `<slack-token-from-step-4>` |
| **ID**          | `slacktoken`                |
| **Description** | Slack Jenkins CI Token      |

3. Saved the credential

---

## Step 6 — Configure Slack in Jenkins System Settings

The Slack plugin was configured in Jenkins global system settings to link Jenkins to the `devlearn-corp` workspace.

**Steps followed:**
1. Jenkins → `Manage Jenkins` → `Configure System` → scrolled to **Slack** section
2. Filled in:

| Field                           | Value           |
|---------------------------------|-----------------|
| **Workspace**                   | `devlearn-corp` |
| **Credential**                  | `slacktoken`    |
| **Default Channel / Member ID** | `#all-devlearn` |
| **Custom Slack App Bot User**   | ☐ Unchecked    |

3. Clicked **Test Connection** → Result: **Success**
4. Saved the configuration

---