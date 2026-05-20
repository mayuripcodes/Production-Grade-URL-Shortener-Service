# Production-Grade-URL-Shortener-Service

A production-oriented URL shortener built with Go, Echo, PostgreSQL, Redis, and a Svelte frontend.

It includes a REST API, a browser-based management UI, embedded OpenAPI docs, Redis-backed redirect lookups, health endpoints, Docker support, and a CLI for running migrations and operational tasks.

## Highlights

- Go service with Echo-based HTTP routing and middleware
- PostgreSQL persistence with embedded SQL migrations
- Redis caching for fast redirect resolution
- Svelte 5 + Vite frontend served by the Go binary
- OpenAPI 3.1 specification with Swagger UI and Redoc
- Configurable expiry, soft delete, click tracking, rate limiting, CORS, and trusted proxy handling
- Docker Compose profiles for development, integration testing, and TLS smoke testing

## Stack

- Go 1.26+
- Echo v5
- Cobra and Viper
- PostgreSQL with `pgx/v5`
- Redis with `go-redis/v9`
- Goose migrations
- Svelte 5, Vite, Tailwind CSS v4, TypeScript
- Just task runner

## Project Layout

```text
cmd/url-shortener/        application entrypoint
internal/                 config, CLI, handlers, server, cache, store, shortener
migrations/               embedded SQL migrations
api/                      OpenAPI source and embedded assets
web/                      frontend app and build pipeline
```

## Quick Start

This project is designed for a Unix-like environment such as Linux, macOS, or WSL2 on Windows.

### Requirements

- Go 1.26+
- Node.js 24+
- Docker with Compose v2
- [Just](https://github.com/casey/just)
- `git`

### Local Setup

```sh
just init
just web-install
just build
```

### Run Common Tasks

```sh
just web-dev
just web-check
just test
just test-integration
just lint
just govulncheck
docker compose --profile=dev up --wait -d
just compose-smoke
docker compose --profile=dev down -v
```

## Running the Service

The compiled binary exposes several operational commands:

```sh
./bin/url-shortener --help
./bin/url-shortener run
./bin/url-shortener version
./bin/url-shortener config
./bin/url-shortener migrate up
./bin/url-shortener migrate status
./bin/url-shortener healthcheck
```

## Configuration

All settings are driven by environment variables prefixed with `URL_SHORTENER_`.

Important variables:

| Variable | Default | Purpose |
| --- | --- | --- |
| `URL_SHORTENER_ENV` | `prod` | Runtime mode |
| `URL_SHORTENER_ADDR` | `:8080` | Listen address |
| `URL_SHORTENER_BASE_URL` | `http://localhost:8080` | Public base URL |
| `URL_SHORTENER_DATABASE_URL` | _(empty)_ | PostgreSQL connection string |
| `URL_SHORTENER_REDIS_URL` | _(empty)_ | Redis connection string |
| `URL_SHORTENER_AUTO_MIGRATE` | `false` | Apply migrations on startup |
| `URL_SHORTENER_CODE_LENGTH` | `7` | Generated short-code length |
| `URL_SHORTENER_RATE_LIMIT_RPS` | `0` | Per-IP create-link rate limit |
| `URL_SHORTENER_TRUSTED_PROXIES` | _(empty)_ | Trusted reverse proxy CIDRs |
| `URL_SHORTENER_TLS_CERT_FILE` | _(empty)_ | TLS certificate path |
| `URL_SHORTENER_TLS_KEY_FILE` | _(empty)_ | TLS private key path |

Run `url-shortener config` to inspect the resolved configuration with secrets redacted.

## API Overview

The OpenAPI contract lives in [`api/openapi.yaml`](api/openapi.yaml) and is also served at runtime.

Available endpoints include:

- `POST /api/v1/links` to create a short link
- `GET /api/v1/links/:code` to fetch link metadata
- `DELETE /api/v1/links/:code` to soft-delete a link
- `GET /r/:code` to redirect to the original URL
- `GET /api/v1/openapi.json` and `GET /api/v1/openapi.yaml` for the spec
- `GET /api/v1/docs` and `GET /api/v1/redoc` for interactive documentation

Example create request:

```http
POST /api/v1/links
Content-Type: application/json

{
  "target_url": "https://example.com",
  "code": "optional-code",
  "expires_at": "2026-05-01T00:00:00Z"
}
```

Example response:

```json
{
  "code": "a1B2c3D",
  "short_url": "https://your.host/r/a1B2c3D",
  "target_url": "https://example.com",
  "created_at": "2026-04-30T06:48:00Z",
  "click_count": 0,
  "expires_at": "2026-05-01T00:00:00Z"
}
```

## Web Interface

The root route serves a single-page app that lets you:

- create short links
- choose an optional custom code
- set an expiry window
- copy generated URLs
- review recent links
- monitor click counts
- delete links from the UI

The production frontend is built into `web/dist/` and embedded directly into the Go binary.

## Operational Endpoints

- `/healthz` for liveness
- `/readyz` for dependency readiness
- `/version` for build metadata

## Deployment

Two deployment patterns are supported:

1. Direct TLS with `URL_SHORTENER_TLS_CERT_FILE` and `URL_SHORTENER_TLS_KEY_FILE`
2. Plain HTTP behind a reverse proxy such as Caddy, Nginx, or Traefik

For local infrastructure, use the Compose profiles defined in [`compose.yaml`](compose.yaml):

- `dev` for the full application stack
- `test` for integration-test databases and cache
- `tls` for HTTPS development with local certificates

## Releases

The project is ready for CI/CD workflows that produce:

- multi-architecture container images
- release archives for Linux and macOS
- generated changelogs
- SBOM and provenance attestations

## Contributing

Contribution workflow and commit conventions are documented in [`CONTRIBUTING.md`](CONTRIBUTING.md).

## License

[MIT](LICENSE)
