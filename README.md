# Automated-Incident-Reporting-System
This repository contains an n8n workflow for an end-to-end automated incident detection, logging, and alerting system. It enables real-time monitoring and rapid response to system failures or security issues, reducing manual intervention and improving response times.
.

🚀 Key Features
Event-Driven Workflows: Triggered by real-time webhooks or scheduled cron jobs to monitor system logs, errors, and other indicators.

Multi-Channel Real-Time Alerts: Instantly notifies team members and stakeholders via Slack and Email for high-priority incidents.

Automated Incident Logging: Creates structured incident reports (timestamp, error type, severity, etc.) and automatically stores them in a Google Sheet for auditing and analysis.

Intelligent Escalation Logic: Includes conditional logic and wait times to automatically escalate unresolved high-priority incidents to senior team members.

Python Integration: Uses an embedded Python script to parse, validate, and standardize incoming data from various sources.

Custom Webhook Integration: Provides a webhook endpoint to easily integrate with third-party monitoring tools, dashboards, or CI/CD pipelines.

⚙️ How It Works
The workflow follows a logical path to process incidents:

Trigger: The workflow is initiated by either a Webhook receiving a POST request or a Cron job running on a schedule.

Parse & Standardize: A Python Code node processes the incoming data, creating a standardized incident object.

Log Incident: The details are immediately logged as a new row in a Google Sheet.

Check Priority: An IF node checks if the incident priority is "high". If not, the workflow stops.

Initial Alert: For high-priority incidents, alerts are sent simultaneously to a Slack channel and via Email to the on-call team.

Wait & Escalate: The workflow waits for 15 minutes. If the incident is not acknowledged (i.e., the workflow is not manually stopped), it sends a second round of alerts to an escalation channel/email address.

Respond: If triggered by a webhook, a success message is sent back to the source system.

##📋 Prerequisites

Before you can use this workflow, you will need:

An active n8n instance.

Credentials for the following services configured in n8n:

Google API (with access to Google Sheets)

Slack (with permissions to post in the desired channels)

SMTP or another email provider for sending email alerts.

🛠️ Setup Instructions
Import Workflow: Copy the JSON from the incident-reporting-workflow.json file in this repository and paste it onto your n8n canvas.

Configure Credentials:

Select your pre-configured credentials in the following nodes:

Google Sheets (Log Incident)

Send Slack Alert (Team)

Send Email Alert (Team)

Send Slack Alert (Escalation)

Send Email Alert (Escalation)

Update Placeholders:

In the Google Sheets (Log Incident) node, replace YOUR_GOOGLE_SHEET_ID with the actual ID of your Google Sheet.

Update the email addresses, Slack channels (#incidents, #sre-leads), and escalation timings (Wait 15 mins for ack) to match your team's structure.

Activate Workflow: Save and activate the workflow in the n8n UI.

🚀 How to Use
Triggering via Webhook
Send a POST request to the webhook URL provided by the Webhook Trigger node.

Example using curl:

curl -X POST \
-H "Content-Type: application/json" \
-d '{
  "title": "API Gateway Latency Spike",
  "priority": "high",
  "details": "The p99 latency for the user-auth service is over 2000ms."
}' \
[YOUR_N8N_WEBHOOK_URL]

Triggering via Cron Job
The Cron Trigger is configured to run automatically every 15 minutes. It will generate a default incident that you can customize in the Code (Parse & Standardize) node. This is useful for periodic health checks.
