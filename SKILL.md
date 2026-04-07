---
name: coolify-api
description: >
  Interact with Coolify's REST API to manage servers, projects, applications, and deployments.
  Use when the user asks to "check coolify status", "list my projects", "deploy an app",
  "manage coolify", "use coolify api", or mentions Coolify-related operations.
metadata:
  version: 1.3.1
---

# Coolify API Skill

This skill enables interaction with Coolify's REST API for infrastructure management.

## Quick Start

Before making any API calls:

1. **Enable API Access**: Go to **Settings** > **Advanced** and enable API Access. (If enabled, authenticated requests to Coolify's REST API will be allowed. Configure API tokens in Security > API Tokens. `/security/api-tokens`)
2. **Gather Requirements**: You will need your Coolify **Server URL** and an **API Token**.
3. **Read the authorization guide**: See `references/authorization.md` for authentication requirements
4. **Check available endpoints**: See `references/openapi.json` for supported operations and parameters

## Base Configuration

| Setting | Value |
|---------|-------|
| Base URL | `http://<ip>:8000/api` |
| API Prefix | `/v1` (except `/health` and `/feedback`) |
| Auth Header | `Authorization: Bearer <token>` |

## Permission Levels

Understanding token permissions is critical for proper API responses:

| Permission | Description |
|------------|-------------|
| `read-only` | Default. Read data only, no sensitive info |
| `read:sensitive` | Read data including sensitive information |
| `view:sensitive` | View passwords, API keys, and secrets in responses |
| `*` | Full access to all resources and sensitive data |

## Deployments and Resource Creation

Whenever the user asks to create, install, or deploy a new application, service, or server, you **MUST determine the project context first**:

1. Use `GET /v1/projects` to fetch the list of existing projects.
2. Inform the user of the available projects.
3. Ask the user if they want to deploy the resource into an existing project or create a new one.
   *Tip: Suggest an appropriate project name based on the user's request if you think a new one is best.*
4. *Do not proceed with the creation until the user confirms the target project.*

## Common Workflows

### Health Check
```
GET /health
```
No authentication required. Returns server status.

### Authenticated Requests
All other endpoints require the Bearer token:
```
GET /v1/projects
Authorization: Bearer <token>
```

## Error Handling

| Scenario | Cause | Solution |
|----------|-------|----------|
| Redacted data in response | Token lacks `view:sensitive` | Generate token with `view:sensitive` permission |
| 401 Unauthorized | Invalid or missing token | Verify token in `Authorization` header |
| 403 Forbidden | Token lacks required scope | Check token permissions match the operation |
| Resource not found | Token scoped to different team | Ensure token belongs to correct team |

## Reference Files

- **Authentication details**: `references/authorization.md`
- **API endpoints**: `references/openapi.json`
- **Service domain configuration**: `references/service-domains.md`

## Workflows

- **Deploying Filebrowser**: `workflows/deploy-filebrowser.md` (Use this whenever the user asks to deploy Filebrowser)
