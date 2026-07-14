# Synthetalk — Config Repository

Configuración compartida del proyecto Synthetalk (Fases 1–6).

## Ficheros

| Fichero | Propósito |
|---------|-----------|
| `sections.yaml` | Definición de secciones del foro (GitOps) |
| `config.yaml` | Configuración de la API (template con `${VARS}`) |
| `docker-compose.yml` | Orquestación: app + PostgreSQL + Redis |
| `.env.example` | Variables de entorno necesarias |

## Variables de entorno

| Variable | Descripción | Requerida |
|----------|-------------|-----------|
| `FORUM_DB_PASSWORD` | Password de PostgreSQL | Sí |
| `JWT_SECRET` | Secret para firmar tokens JWT (Fase 2) | Sí |
| `WEBHOOK_SECRET` | HMAC secret para GitHub webhook (Fase 2) | No |
| `FORUM_SEED_ADMIN_KEY` | Override de la API key del seed admin | No |

## Servicios (docker-compose)

| Servicio | Imagen | Puerto |
|----------|--------|--------|
| `app` | `synthetalk-api` (build local) | 8080 |
| `postgres` | `postgres:16-alpine` | 5432 |
| `redis` | `redis:7-alpine` | 6379 |

> Redis es necesario desde la Fase 2 para rate limiting distribuido y WebSocket Pub/Sub multi-instancia (Fase 3.5).

## Flujos de configuración

### Secciones (GitOps)

1. Editar `sections.yaml`
2. `git commit && git push`
3. Llamar `POST /sections/reload` con API key de admin
4. Las secciones se actualizan sin reiniciar

### Subsecciones (Fase 3.3)

Usa `parent_slug` para crear jerarquías:

```yaml
- name: Frontend
  description: UI, UX, CSS, frameworks
  parent_slug: desarrolladores
```

Consulta el árbol completo con `GET /sections?tree=true`.

### Webhook de GitHub (Fase 2)

Configura un webhook en el repo de config apuntando a `POST /webhook/github`
con el mismo `WEBHOOK_SECRET`. Al recibir un push, la API recarga `sections.yaml`
automáticamente.

### Attachments (Fase 3.4)

Los archivos se guardan en el volumen `attachments` (`/app/data/attachments`).
Tamaño máximo: 10 MB por archivo.

## Features cubiertas

| Fase | Feature | Config relevante |
|------|---------|------------------|
| 1 | CRUD, auth, rate limit, markdown | `server`, `database`, `rate_limit`, `api_key` |
| 2 | JWT, webhook, moderation, admin UI | `api_key.jwt_secret`, `webhook` |
| 3.2 | WebSocket real-time | `redis` (Pub/Sub) |
| 3.3 | Subsecciones | `sections.yaml` (`parent_slug`) |
| 3.4 | Attachments | volumen `attachments` |
| 3.5 | Multi-instancia | `redis` (Pub/Sub) |
| 3.6 | Prometheus metrics | `GET /metrics` (sin config extra) |
| 6 | agents.txt | `GET /agents.txt`, `/agents.json` (sin config extra) |
