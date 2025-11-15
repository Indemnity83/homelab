# app-mealie

![Exposure-Public](https://img.shields.io/badge/Exposure-Public-orange)

Self‑hosted recipe manager and meal‑planning application.

Provides a clean interface for storing recipes, organizing weekly meal plans, and sharing with the household. This stack is publicly exposed through Traefik and authenticated via Authentik (OIDC); public signups and local password logins are disabled.

---

## What This Stack Does

- Runs Mealie using the official container image.
- Stores all application data in the local `appdata/mealie` directory.
- Routes traffic through Traefik for HTTPS access.
- Uses global defaults for `PUID`, `PGID`, `UMASK`, and `TZ`.

---

## Required Environment Variables (stack‑specific)

| Variable                  | Purpose                                                |
|---------------------------|--------------------------------------------------------|
| `OPENAI_API_KEY`          | Enables optional OpenAI features (if used)             |
| `OIDC_CLIENT_ID`          | Client ID issued by Authentik for Mealie               |
| `OIDC_CLIENT_SECRET`      | Client secret issued by Authentik for Mealie           |
| `OIDC_APP_SLUG`           | Application slug in Authentic for Mealie               |

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

- `ALLOW_SIGNUP=false` prevents public registration; all users are provisioned via Authentik.
- `ALLOW_PASSWORD_LOGIN=false` enforces SSO-only access; users must log in through Authentik.
- Authentik groups `mealie-users` and `mealie-admins` control basic user/admin access via OIDC.
- SMTP settings (`SMTP_HOST`, `SMTP_PORT`, etc.) are used for email features like notifications.
- `OPENAI_API_KEY` enables optional OpenAI-backed functionality in Mealie.
- Mealie supports importing recipes from URLs, copying from other services, or pasting raw text.