# Service Domain/URL Configuration

## Overview

When exposing a service in Coolify that uses Docker Compose, the subdomain/domain must be updated via API using the `urls` field. This is critical for making services accessible via HTTPS.

## Endpoint

```http
PATCH /api/v1/services/{uuid}
```

## Request Body

```json
{
  "urls": [
    {
      "name": "service-name",
      "url": "https://subdomain.domain.tld"
    }
  ]
}
```

## Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `urls` | array | Array of objects with `name` and `url` properties |
| `name` | string | Must match the service name defined in docker-compose.yml |
| `url` | string | Full URL including protocol (https:// or http://) |

## Important Warnings

| Do NOT Use | Reason |
|------------|--------|
| `fqdn` | Returns "This field is not allowed" error |
| `domains` | Not available for services endpoint |

## Example Request

```bash
curl -X PATCH "https://your-coolify-instance.com/api/v1/services/cbmphrjpmyt5d5cp2euyu836" \
  -H "Authorization: Bearer 1|your-api-token" \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      {
        "name": "ipfinder",
        "url": "https://ipfinder.yourdomain.com"
      }
    ]
  }'
```

## Response

```json
{
  "uuid": "cbmphrjpmyt5d5cp2euyu836",
  "domains": [
    "https://ipfinder.yourdomain.com"
  ]
}
```

## When to Use This Method

- Services deployed with `build_pack: dockercompose`
- When you need to assign or update a subdomain for an existing service
- When the Coolify UI shows an empty "Domains" field
- After verifying the service is running but not accessible via HTTPS

## Workflow for New Services

1. **Create the service** via API or UI
2. **Verify service is running** with `GET /v1/services/{uuid}`
3. **Configure domain** using `PATCH /v1/services/{uuid}` with `urls` field
4. **Verify HTTPS access** at the configured URL

## Troubleshooting

| Error | Cause | Solution |
|-------|-------|----------|
| "Application not found" | Wrong endpoint | Use `/services/{uuid}` NOT `/applications/{uuid}` |
| "Validation failed" | Invalid field format | Ensure `urls` is an array with `name` and `url` |
| "This field is not allowed" | Using `fqdn` field | Use `urls` array instead |
| HTTPS 503 error | Domain not configured | Update via API as shown above |

## Related Endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/services/{uuid}` | Get service details (check `fqdn` field) |
| PATCH | `/services/{uuid}` | Update service configuration including domains |

## Requirements

- **Coolify version**: 4.0.0-beta.468+
- **Service type**: Docker Compose (`build_pack: dockercompose`)
- **API token scope**: Full access (`*`)
- **Protocol**: Must include `https://` or `http://` in URL

## Date

2026-04-05

## Tested By

Blockito
