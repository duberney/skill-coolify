# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2026-04-06

### Added

- **references/service-domains.md** - Service domain/URL configuration guide
  - Documentation for exposing Docker Compose services as subdomains
  - Usage of `urls` field (NOT `fqdn` or `domains`)
  - Troubleshooting common domain configuration issues
  - Workflow for configuring new service domains

### Changed

- Updated `SKILL.md` to reference the new service-domains.md
- Updated `README.md` with service domain configuration section
- Updated file structure documentation in README.md

## [1.1.0] - 2026-03-24

### Changed

- Updated `SKILL.md` to reference `openapi.json` instead of `endpoints.md` for proper API reference implementation following Claude best practices.
- Replaced the static `references/endpoints.md` guide with the complete `references/openapi.json` OpenAPI specification.

## [1.0.0] - 2026-03-18

### Added

- **SKILL.md** - Coolify API skill definition with metadata and usage documentation
  - Base configuration for API interaction
  - Permission levels explanation (`read-only`, `read:sensitive`, `view:sensitive`, `*`)
  - Common workflows and error handling guide
- **references/authorization.md** - Comprehensive authorization guide
  - API access and route prefixing details
  - Bearer token authentication format
  - Token generation instructions
  - Permission types and their use cases
  - Troubleshooting for common authentication issues
- **references/endpoints.md** - Complete API endpoints reference
  - Public endpoints (health check, feedback)
  - Server management endpoints
  - Project management endpoints (CRUD operations)
  - Application management (start, stop, restart, deploy)
  - Service and database management
  - Deployment tracking and logs
  - Environment variables management
  - Webhooks configuration
  - Error responses and rate limiting
- **LICENSE.md** - MIT License
