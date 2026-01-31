# itw-infra

Shared local infrastructure for ITWizardry services.

## Includes
- PostgreSQL (for `user-service`)
- Kafka + Zookeeper (shared broker for services)
- Mailhog (SMTP + Web UI)

## Requirements
- Docker
- Docker Compose v2

## Quick start

```bash
cp .env.example .env
docker compose up -d

Endpoints

PostgreSQL: localhost:${POSTGRES_PORT:-5432}

Kafka: localhost:${KAFKA_PORT:-9092}

Mailhog UI: http://localhost:${MAILHOG_UI_PORT:-8025}

Mailhog SMTP: localhost:${MAILHOG_SMTP_PORT:-1025}

Shared Docker network

All containers run in the network: itw-dev-net (configurable via DOCKER_NETWORK).

Used by

user-service

notification-service
```