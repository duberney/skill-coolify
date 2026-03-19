# Authorization Guide

## API Access

The Coolify API is accessible at:

```
http://<your-coolify-ip>:8000/api
```

### Route Prefixing

| Endpoint Type | Prefix | Example |
|---------------|--------|---------|
| Health Check | None | `GET /health` |
| Feedback | None | `GET /feedback` |
| All other endpoints | `/v1` | `GET /v1/projects` |

## Authentication

All API requests (except `/health` and `/feedback`) require Bearer token authentication.

### Header Format

```http
Authorization: Bearer <your-api-token>
```

### Example Request

```http
GET /v1/projects HTTP/1.1
Host: your-coolify-instance.com
Authorization: Bearer 3|WaobqX9tJQshKPuQFHsyApxuOOggg4wOfvGc9xa233c376d7
```

## Token Generation

Generate API tokens from the Coolify UI:

1. Navigate to **Keys & Tokens** > **API tokens**
2. Enter a descriptive name for your token
3. Click **Create New Token**
4. **Important**: Copy the token immediately - it will only be shown once

### Token Format

```
<id>|<random-string>
```

Example: `3|WaobqX9tJQshKPuQFHsyApxuOOggg4wOfvGc9xa233c376d7`

## Token Scope

API tokens are scoped to a specific team. The token can only access resources owned by that team.

**Important**: If you cannot see expected resources, verify the token is scoped to the correct team.

## Permission Types

### `read-only` (Default)

- Read access to non-sensitive data
- Cannot create, update, or delete resources
- Sensitive data is redacted in responses

**Use case**: Monitoring, status checks, listing resources

### `read:sensitive`

- Read access including sensitive information
- Cannot create, update, or delete resources
- View configuration that is normally redacted

**Use case**: Auditing, configuration review, debugging

### `view:sensitive`

- Allows viewing sensitive data in API responses
- Passwords, API keys, and secrets are visible
- Must be combined with other permissions

**Use case**: Credential retrieval, secret management

### `*` (Full Access)

- Complete read/write access to all resources
- View all sensitive data
- Create, update, and delete operations

**Use case**: Automation, deployment pipelines, full management

## Troubleshooting

### Sensitive Data Redacted

**Symptom**: API responses contain `********` or `[REDACTED]` for passwords, keys, etc.

**Cause**: Token lacks `view:sensitive` permission

**Solution**:
1. Generate a new token with `view:sensitive` permission
2. Or use a token with `*` (full access) permission

### 401 Unauthorized

**Symptom**: API returns 401 status code

**Cause**: Invalid, expired, or missing token

**Solution**:
1. Verify the `Authorization` header is present
2. Check the token format includes `Bearer ` prefix
3. Regenerate the token if it may have been revoked

### 403 Forbidden

**Symptom**: API returns 403 status code

**Cause**: Token lacks required permission for the operation

**Solution**:
1. Check if operation requires write access (token may be `read-only`)
2. Generate a new token with appropriate permissions
3. For write operations, use `*` permission

### Resource Not Found

**Symptom**: Expected resources return 404 or empty lists

**Cause**: Token scoped to wrong team

**Solution**:
1. Verify the token belongs to the correct team
2. Generate a new token from the correct team context
