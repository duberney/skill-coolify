# Coolify API Skill

A Claude Code skill for interacting with Coolify's REST API to manage servers, projects, applications, databases, and deployments.

## Features

- **Server Management**: Monitor and manage Coolify servers
- **Project Operations**: Create, update, and delete projects
- **Application Lifecycle**: Deploy, start, stop, and restart applications
- **Database Management**: Control database instances
- **Service Control**: Manage containerized services
- **Deployment Tracking**: View deployment history and logs
- **Environment Variables**: Manage application configuration
- **Webhook Integration**: Set up event notifications

## Installation

### Option 1: Copy to Skills Directory

```bash
# Copy the skill to your Claude Code skills directory
cp -r skill-coolify ~/.claude/skills/coolify-api
```

### Option 2: Create a Symlink (Development)

```bash
# Linux/macOS
ln -s $(pwd)/skill-coolify ~/.claude/skills/coolify-api

# Windows (PowerShell as Administrator)
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\coolify-api" -Target "$(Get-Location)\skill-coolify"
```

## Usage

### Trigger Phrases

The skill automatically loads when you use phrases like:

- "Check Coolify status"
- "List my Coolify projects"
- "Deploy to Coolify"
- "Use the Coolify API"
- "Manage my Coolify server"

### Quick Start

Once loaded, Claude will have access to the Coolify API documentation and can help you:

```
# Check server health (no authentication required)
"Check the health of my Coolify instance at http://192.168.1.100:8000"

# List projects (authentication required)
"Using my API token, list all Coolify projects"

# Deploy an application
"Deploy the application with UUID abc-123"
```

## Configuration

### Enabling API Access

Before using the Coolify API, you must enable it in your instance:

1. Go to **Settings** > **Advanced** in your Coolify dashboard.
2. Enable the **API Access** setting.
   > If enabled, authenticated requests to Coolify's REST API will be allowed. Configure API tokens in Security > API Tokens. (`/security/api-tokens`)

### Required Information

To use this skill, you need:
1. **Server URL**: Your Coolify instance address (e.g., `http://192.168.1.100:8000`)
2. **API Token**: Your generated Bearer token

### API Access Details

| Setting | Value |
|---------|-------|
| Base URL | `http://<ip>:8000/api` |
| API Prefix | `/v1` (except `/health` and `/feedback`) |
| Auth Header | `Authorization: Bearer <token>` |

### Generating an API Token

1. Navigate to your Coolify dashboard
2. Go to **Security** > **API tokens** (or **Keys & Tokens** > **API tokens** depending on your version)
3. Enter a name and click **Create New Token**
4. Copy the token immediately (shown only once)

### Permission Levels

| Permission | Read | Write | View Sensitive |
|------------|------|-------|----------------|
| `read-only` | Yes | No | No |
| `read:sensitive` | Yes | No | Yes |
| `view:sensitive` | Yes | No | Yes |
| `*` (full access) | Yes | Yes | Yes |

## Examples

### Health Check

```
GET /health
```

No authentication required. Returns server status.

### List Projects

```http
GET /v1/projects
Authorization: Bearer 1|your-api-token-here
```

### Deploy an Application

```http
GET /v1/applications/{uuid}/deploy
Authorization: Bearer 1|your-api-token-here
```

### Manage Environment Variables

```http
POST /v1/applications/{uuid}/envs
Authorization: Bearer 1|your-api-token-here
Content-Type: application/json

{
  "key": "DATABASE_URL",
  "value": "postgres://localhost:5432/mydb"
}
```

## Service Domain Configuration

When exposing a service (Docker Compose) as a subdomain, you must use the `urls` field:

```http
PATCH /v1/services/{uuid}
Content-Type: application/json

{
  "urls": [
    {
      "name": "service-name",
      "url": "https://subdomain.domain.tld"
    }
  ]
}
```

**Important**:
- Use `urls` field (NOT `fqdn` or `domains`)
- `name` must match the service name in docker-compose.yml
- URL must include protocol (`https://` or `http://`)

See `references/service-domains.md` for full documentation.

## File Structure

```
skill-coolify/
├── SKILL.md                    # Main skill definition (YAML + instructions)
├── README.md                   # This documentation file
├── LICENSE.md                  # MIT License
├── CHANGELOG.md                # Version history
├── references/
│   ├── authorization.md        # Authentication & permissions guide
│   ├── openapi.json            # OpenAPI 3.1.0 specification
│   └── service-domains.md      # Service domain/URL configuration
└── workflows/
    └── deploy-filebrowser.md   # Workflow for deploying Filebrowser
```

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Sensitive data redacted | Token lacks `view:sensitive` | Regenerate token with proper permission |
| 401 Unauthorized | Invalid/missing token | Verify Authorization header format |
| 403 Forbidden | Insufficient permissions | Use token with `*` permission for write ops |
| Resources not found | Wrong team scope | Ensure token belongs to correct team |

### Rate Limiting

The API returns rate limit information in response headers:

```http
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 59
```

## Resources

- [Coolify Documentation](https://coolify.io/docs)
- [Coolify GitHub](https://github.com/coollabsio/coolify)
- [Coolify Discord](https://coollabs.io/discord)

## License

This skill is provided as-is for use with Claude Code and the Coolify platform.

## Version

- **Skill Version**: 1.3.2
- **Compatible with**: Coolify v4.x API
