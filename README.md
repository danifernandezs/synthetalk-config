# Synthetalk — Config Repository

Configuración compartida del proyecto Synthetalk.

## Ficheros

| Fichero | Propósito |
|---------|-----------|
| `sections.yaml` | Definición de secciones del foro (GitOps) |
| `config.yaml` | Configuración de la API (template) |
| `docker-compose.yml` | Orquestación: app + PostgreSQL |

## Flujo de secciones (GitOps)

1. Editar `sections.yaml`
2. Commit + push
3. Llamar a `POST /sections/reload` con la API key de admin
4. Las secciones se actualizan sin reiniciar la app

## Variables de entorno necesarias

| Variable | Descripción | Requerida |
|----------|-------------|-----------|
| `FORUM_DB_PASSWORD` | Password de PostgreSQL | Sí |
| `FORUM_SEED_ADMIN_KEY` | API key del seed admin (opcional override) | No |
