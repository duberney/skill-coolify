# API Endpoints Reference

## Overview

All endpoints (except where noted) require:
- Base URL: `http://<ip>:8000/api`
- Prefix: `/v1` for most endpoints
- Authentication: `Authorization: Bearer <token>`

---

## Public Endpoints

### Health Check

Check the server status without authentication.

```http
GET /health
```

**Authentication**: Not required

**Response**:
```json
{
  "status": "ok"
}
```

### Feedback

Submit feedback without authentication.

```http
POST /feedback
```

**Authentication**: Not required

**Body**:
```json
{
  "message": "Your feedback message",
  "email": "optional@email.com"
}
```

---

## Server Management

### List Servers

Retrieve all servers accessible by the authenticated token.

```http
GET /v1/servers
```

**Required Permission**: `read-only`

**Response**:
```json
[
  {
    "id": 1,
    "name": "Production Server",
    "description": "Main production server",
    "ip": "192.168.1.100",
    "status": "running"
  }
]
```

### Get Server Details

Retrieve details for a specific server.

```http
GET /v1/servers/{server_id}
```

**Parameters**:
| Name | Type | Location | Required |
|------|------|----------|----------|
| `server_id` | integer | path | Yes |

**Required Permission**: `read-only`

### Server Resources

Get resource usage for a server.

```http
GET /v1/servers/{server_id}/resources
```

**Required Permission**: `read-only`

**Response**:
```json
{
  "cpu": 45.2,
  "memory": {
    "used": 4096,
    "total": 8192,
    "percentage": 50
  },
  "disk": {
    "used": 50,
    "total": 100,
    "percentage": 50
  }
}
```

### Validate Server

Validate server configuration and connectivity.

```http
GET /v1/servers/{server_id}/validate
```

**Required Permission**: `read-only`

---

## Project Management

### List Projects

Retrieve all projects accessible by the authenticated token.

```http
GET /v1/projects
```

**Required Permission**: `read-only`

**Response**:
```json
[
  {
    "id": 1,
    "name": "My Project",
    "description": "Project description",
    "environments": [
      {
        "name": "production",
        "uuid": "uuid-here"
      }
    ]
  }
]
```

### Create Project

Create a new project.

```http
POST /v1/projects
```

**Required Permission**: `*`

**Body**:
```json
{
  "name": "New Project",
  "description": "Project description"
}
```

### Get Project Details

Retrieve details for a specific project.

```http
GET /v1/projects/{project_id}
```

**Required Permission**: `read-only`

### Update Project

Update project settings.

```http
PATCH /v1/projects/{project_id}
```

**Required Permission**: `*`

**Body**:
```json
{
  "name": "Updated Name",
  "description": "Updated description"
}
```

### Delete Project

Delete a project and its resources.

```http
DELETE /v1/projects/{project_id}
```

**Required Permission**: `*`

---

## Application Management

### List Applications

Retrieve all applications in a project.

```http
GET /v1/applications
```

**Query Parameters**:
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `project_id` | integer | No | Filter by project |

**Required Permission**: `read-only`

### Get Application

Retrieve details for a specific application.

```http
GET /v1/applications/{application_uuid}
```

**Required Permission**: `read-only`

### Start Application

Start a stopped application.

```http
GET /v1/applications/{application_uuid}/start
```

**Required Permission**: `*`

### Stop Application

Stop a running application.

```http
GET /v1/applications/{application_uuid}/stop
```

**Required Permission**: `*`

### Restart Application

Restart an application.

```http
GET /v1/applications/{application_uuid}/restart
```

**Required Permission**: `*`

### Deploy Application

Trigger a new deployment.

```http
GET /v1/applications/{application_uuid}/deploy
```

**Required Permission**: `*`

**Query Parameters**:
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `force` | boolean | No | Force rebuild |

---

## Service Management

### List Services

Retrieve all services.

```http
GET /v1/services
```

**Required Permission**: `read-only`

### Get Service

Retrieve details for a specific service.

```http
GET /v1/services/{service_uuid}
```

**Required Permission**: `read-only`

### Start Service

Start a stopped service.

```http
GET /v1/services/{service_uuid}/start
```

**Required Permission**: `*`

### Stop Service

Stop a running service.

```http
GET /v1/services/{service_uuid}/stop
```

**Required Permission**: `*`

### Restart Service

Restart a service.

```http
GET /v1/services/{service_uuid}/restart
```

**Required Permission**: `*`

---

## Database Management

### List Databases

Retrieve all databases.

```http
GET /v1/databases
```

**Required Permission**: `read-only`

### Get Database

Retrieve details for a specific database.

```http
GET /v1/databases/{database_uuid}
```

**Required Permission**: `read-only`

**Note**: Database credentials require `view:sensitive` permission.

### Start Database

Start a stopped database.

```http
GET /v1/databases/{database_uuid}/start
```

**Required Permission**: `*`

### Stop Database

Stop a running database.

```http
GET /v1/databases/{database_uuid}/stop
```

**Required Permission**: `*`

### Restart Database

Restart a database.

```http
GET /v1/databases/{database_uuid}/restart
```

**Required Permission**: `*`

---

## Deployments

### List Deployments

Retrieve deployment history for an application.

```http
GET /v1/applications/{application_uuid}/deployments
```

**Required Permission**: `read-only`

### Get Deployment Details

Retrieve details for a specific deployment.

```http
GET /v1/deployments/{deployment_uuid}
```

**Required Permission**: `read-only`

### Deployment Logs

Retrieve logs for a specific deployment.

```http
GET /v1/deployments/{deployment_uuid}/logs
```

**Required Permission**: `read-only`

---

## Environment Variables

### List Environment Variables

Retrieve environment variables for an application.

```http
GET /v1/applications/{application_uuid}/envs
```

**Required Permission**: `read-only`

**Note**: Sensitive values require `view:sensitive` permission.

### Create Environment Variable

Add a new environment variable.

```http
POST /v1/applications/{application_uuid}/envs
```

**Required Permission**: `*`

**Body**:
```json
{
  "key": "VARIABLE_NAME",
  "value": "variable_value",
  "is_preview": false
}
```

### Update Environment Variable

Update an existing environment variable.

```http
PATCH /v1/applications/{application_uuid}/envs/{env_uuid}
```

**Required Permission**: `*`

### Delete Environment Variable

Delete an environment variable.

```http
DELETE /v1/applications/{application_uuid}/envs/{env_uuid}
```

**Required Permission**: `*`

---

## Webhooks

### List Webhooks

Retrieve all configured webhooks.

```http
GET /v1/webhooks
```

**Required Permission**: `read-only`

### Create Webhook

Create a new webhook.

```http
POST /v1/webhooks
```

**Required Permission**: `*`

**Body**:
```json
{
  "url": "https://your-webhook-endpoint.com",
  "events": ["deployment:success", "deployment:failed"]
}
```

### Delete Webhook

Delete a webhook.

```http
DELETE /v1/webhooks/{webhook_id}
```

**Required Permission**: `*`

---

## Error Responses

All endpoints follow standard HTTP status codes:

| Code | Description |
|------|-------------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request - Invalid parameters |
| 401 | Unauthorized - Invalid or missing token |
| 403 | Forbidden - Insufficient permissions |
| 404 | Not Found - Resource doesn't exist |
| 422 | Unprocessable Entity - Validation error |
| 500 | Internal Server Error |

### Error Response Format

```json
{
  "message": "Error description",
  "errors": {
    "field": ["Validation error message"]
  }
}
```

---

## Rate Limiting

API requests are subject to rate limiting. Check response headers for limits:

```http
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 59
```

When rate limited, the API returns `429 Too Many Requests`.
