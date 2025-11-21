# linkedin-post-automation
# Serverless LinkedIn Automation on GCP 🚀

A robust, self-hosted automation pipeline that schedules and posts content to LinkedIn using **n8n**, **Google Cloud Platform**, and **Google Sheets**.

![n8n](https://img.shields.io/badge/n8n-Workflow-orange?logo=n8n) ![GCP](https://img.shields.io/badge/GCP-Compute%20Engine-blue?logo=google-cloud) ![Docker](https://img.shields.io/badge/Docker-Container-blue?logo=docker)

## 📋 Project Overview

This tool allows for "Set and Forget" social media management. It reads content from a managed Google Sheet queue, processes the data, posts to a personal LinkedIn profile via API, and updates the status to prevent duplicates.

**Key Features:**
* **Infrastructure:** Self-hosted n8n instance on GCP Compute Engine (e2-medium).
* **Security:** Reverse Proxy via Caddy with auto-renewing SSL (Let's Encrypt).
* **Logic:** Custom OAuth2 authentication for LinkedIn API (OpenID + UGC Posts).
* **Data Persistence:** Two-way sync with Google Sheets (Read pending -> Post -> Update status).

## 🏗️ Architecture

**Data Flow:**
1.  **Trigger:** Scheduled Cron job (Every day at 09:00 UTC).
2.  **Source:** Fetch row from Google Sheets where `Status == "Pending"`.
3.  **Transform:** Extract `Content` and `ImageURL`.
4.  **Action:** Authenticate via OAuth2 and `POST` to LinkedIn `ugcPosts` endpoint.
5.  **Update:** Write back to Google Sheets setting `Status = "Posted"`.

## 🛠️ Tech Stack

* **Cloud:** Google Cloud Platform (Compute Engine)
* **OS:** Debian 12 (Bookworm)
* **Containerization:** Docker & Docker Compose
* **Automation:** n8n (Workflow Automation Tool)
* **APIs:** LinkedIn API v2, Google Sheets API v4

## ⚙️ Setup & Deployment

### 1. Infrastructure Setup
The instance is deployed on an `e2-medium` VM in the `asia-south1` region.
```bash
# Example deployment command
curl -fsSL [https://raw.githubusercontent.com/llmx-de/n8n-cloud-deploy/main/install.sh](https://raw.githubusercontent.com/llmx-de/n8n-cloud-deploy/main/install.sh) | bash

2. Configuration
Domain: Configured via A-Record pointing to GCP Static IP.

SSL: Handled automatically by Caddy sidecar container.

3. API Permissions
LinkedIn App Scopes:

openid: For user identification.

w_member_social: To create posts on behalf of the user.

📂 Repository Structure      
/workflows: Contains the JSON export of the n8n logic. Import this directly into your n8n instance.

/infra: Reference Docker Compose configuration for the server setup.

🚀 Usage
Add rows to the Google Sheet with columns: Content, Status, ImageURL.

Set Status to Pending.

The bot wakes up at the scheduled time and processes the queue.