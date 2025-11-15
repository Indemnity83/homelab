# Authentik (Identity Provider)

![Public Stack](https://img.shields.io/badge/Exposure-Public-orange?style=flat-square)
![Private Stack](https://img.shields.io/badge/Exposure-Private-blue?style=flat-square)

Self‑hosted identity provider for single sign‑on (SSO) across the homelab. Provides user authentication, group‑based authorization, OIDC providers for apps like Mealie and Paperless, and forward‑auth for Traefik.

---

## What This Stack Does

- Provides OIDC (OpenID Connect) authentication for apps
- Manages users, groups, permissions, and app‑specific roles
- Acts as a forward‑auth provider for Traefik when protecting non‑OIDC apps

---

## Required Environment Variables

Ensure these exist in the global Portainer environment or in a stack-local `.env` file:

| Variable                  | Purpose                                        |
|---------------------------|------------------------------------------------|
| `AUTHENTIK_SECRET_KEY`    | Master secret used by Authentik for encryption |
| `AUTHENTIK_DB_USER`       | Postgres username for the Authentik DB         |
| `AUTHENTIK_DB_PASSWORD`   | Postgres password for the Authentik DB         |
| `AUTHENTIK_DB_NAME`       | Postgres database name (usually `authentik`)    |

---

## Routing

### Traefik

| Hostname               | Purpose                         | Middleware |
|------------------------|---------------------------------|------------|
| `auth.stephandkyle.us` | Authentik UI / OIDC endpoints   | none       |

### Internal

| URL                            | Purpose                     | Network         |
|--------------------------------|-----------------------------|-----------------|
| `http://authentik-server:9000` | Authentik server backend    | `authentik_net` |
| `http://authentik-worker:9000` | Background worker services  | `authentik_net` |
| `redis://authentik-redis:6379` | Redis cache backend         | `authentik_net` |
| `postgresql://authentik-postgres` | Primary database         | `authentik_net` |

---

## Notes for Future Reference

- The `authentik-server` and `authentik-worker` containers must use the same `AUTHENTIK_SECRET_KEY`.
- Email configuration is handled via environment variables in the stack, not the UI.
- Postgres and Redis healthchecks gate startup so the server does not initialize prematurely.
- Use pinned image tags (e.g. `:2025.10.1`) rather than `latest` to ensure safe upgrades.
- User logins, groups, and admin permissions are all managed through the Authentik UI.
- This stack is not suitable for first‑time initialization without existing Postgres data. The `user` override on the Postgres container prevents Postgres from creating its initial database directories, causing startup failures if the data directory is empty.