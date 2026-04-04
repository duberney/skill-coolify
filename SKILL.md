---
name: coolify-api
description: >
  Interact with Coolify's REST API to manage servers, projects, applications, and deployments.
  Use when the user asks to "check coolify status", "list my projects", "deploy an app",
  "manage coolify", "use coolify api", or mentions Coolify-related operations.
metadata:
  version: 1.0.0
---

# Coolify API Skill

This skill enables interaction with Coolify's REST API for infrastructure management.

## Quick Start

Before making any API calls:

1. **Read the authorization guide**: See `references/authorization.md` for authentication requirements
2. **Check available endpoints**: See `references/openapi.json` for supported operations and parameters

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
