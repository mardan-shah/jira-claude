# Orcastrator: Jira to Claude Automation

This project provides an automated workflow to process Jira tickets using Claude Code (or a mock implementation) and sync results back to Jira and Slack, all orchestrated via **Kestra**.

## Project Structure

- `docker-compose.yml`: Defines the Kestra and Postgres services.
- `Jira-claude.yml`: The main Kestra flow for full Jira-to-Claude automation.
- `claude-only.yml`: A simplified Kestra flow for running Claude commands.
- `claude-mock`: A shell script to simulate Claude Code behavior without API calls.
- `.env.example`: Template for environment variables.

## Getting Started

### 1. Prerequisites
- Docker and Docker Compose installed.
- Access to Jira, Slack (Webhooks), and GitHub (Personal Access Token).

### 2. Environment Setup
Copy the `.env.example` to `.env` and fill in your actual values:
```bash
cp .env.example .env
```

**Required Variables:**
- `POSTGRES_PASSWORD`: Password for the internal Kestra database.
- `KESTRA_ADMIN_USER`: Email for Kestra login.
- `KESTRA_ADMIN_PASSWORD`: Password for Kestra login.
- `JIRA_BASE_URL`: Your Atlassian domain (e.g., `https://your-org.atlassian.net`).
- `JIRA_AUTH_BASIC`: Base64 encoded `email:api_token`.
- `SLACK_WEBHOOK_URL`: Incoming webhook URL for notifications.
- `GITHUB_TOKEN`: PAT with repository access.
- `USE_MOCK`: Set to `true` to use the mock script, `false` for real Claude CLI.

### 3. Launching the Orchestrator
Run the following command to start Kestra and Postgres:
```bash
docker-compose up -d
```

Once started, access the Kestra UI at:
- **URL**: `http://localhost:8080`
- **User**: (As defined in your `.env`)
- **Password**: (As defined in your `.env`)

### 4. Importing the Flow
1. Open Kestra UI.
2. Go to **Flows** -> **Create**.
3. Copy the content of `Jira-claude.yml` into the editor.
4. Save the flow.

## Workflow Execution

1. **Trigger**: The flow starts via a Jira Webhook or manual execution.
2. **Analysis**: Extract issue data (Key, Summary, Description) from Jira.
3. **Setup**: Clone the relevant repository and create a feature branch based on the ticket.
4. **Implementation**:
   - If `USE_MOCK` is `true`, `claude-mock` simulates implementation.
   - If `USE_MOCK` is `false`, the real Claude CLI processes the ticket.
5. **Sync**:
   - Results are posted as a comment on the Jira ticket.
   - A notification is sent to Slack.
   - Changes are committed and pushed to GitHub (if in Implement mode).

## Security Note
All sensitive keys and passwords have been removed from the source files. Always use the `.env` file to manage credentials and never commit your `.env` to version control.
