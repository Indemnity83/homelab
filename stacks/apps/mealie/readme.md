# app-mealie

![Exposure-Public](https://img.shields.io/badge/Exposure-Public-orange)

Self‑hosted recipe manager and meal‑planning application.

Provides a clean interface for storing recipes, organizing weekly meal plans, and sharing with the household. This stack is publicly exposed through Traefik.

---

## What This Stack Does

- Runs Mealie using the official container image.
- Stores all application data in the local `appdata/mealie` directory.
- Routes traffic through Traefik for HTTPS access.
- Uses global defaults for `PUID`, `PGID`, `UMASK`, and `TZ`.

---

## Required Environment Variables (stack‑specific)

| Variable        | Purpose                                  |
|------------------|-------------------------------------------|
| `BASE_URL`       | External hostname used by Mealie          |
| `ALLOW_SIGNUP`   | Controls whether public signups are allowed |
| `MAX_WORKERS`    | Worker count for Mealie backend            |
| `WEB_CONCURRENCY`| Gunicorn concurrency settings              |

---

## Routing

### Traefik

| Hostname                     | Purpose                     | Middleware |
|------------------------------|------------------------------|------------|
| `recipes.stephandkyle.us`    | Public Mealie interface      |     |

### Internal

| URL                       | Purpose                    | Network        |
|---------------------------|-----------------------------|----------------|
| `http://mealie:9000`      | Main Mealie backend         | `traefik_proxy` |

---

## Notes for Future Reference

- `ALLOW_SIGNUP=false` prevents public registration; all household users must be created manually.
- Mealie supports importing recipes from URLs, copying from other services, or pasting raw text.
- Use the nightly tag cautiously—breaking schema changes are more common.
- When switching back to stable releases, check migration notes for compatibility.