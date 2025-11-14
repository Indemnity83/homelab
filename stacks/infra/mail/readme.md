# infra-mail

Lightweight internal SMTP relay for the homelab.  

All apps send email to `mail` on the Docker network; the relay forwards mail out via Mailgun.

---

## What this stack does

- Runs a small SMTP relay using [`crazymax/msmtpd`](https://github.com/crazy-max/docker-msmtpd).
- Listens on port **2500** inside the `traefik_proxy` Docker network.
- Authenticates and sends mail to Mailgun’s SMTP endpoint (`smtp.mailgun.org:587`) using TLS and SMTP auth.
- Centralizes outbound email config so individual apps only need:
  - `SMTP_HOST=mail`
  - `SMTP_PORT=2500`
- Keeps the relay **internal-only** (no Traefik / public exposure).

---

## Required Environment Variables (stack-specific)

| Variable                | Purpose                        |
|-------------------------|--------------------------------|
| `MAILGUN_SMTP_USER`     | Mailgun SMTP user              |
| `MAILGUN_SMTP_PASSWORD` | Mailgun SMTP password / secret |

---

## Routing

### Traefik

This stack is **internal-only** and exposes no HTTP services.  
There are **no Traefik routers** or public hostnames for `mailgun`.

### Internal

| Endpoint           | Purpose                                   | Network         |
| ------------------ | ----------------------------------------- | --------------- |
| `smtp://mail:2500` | SMTP relay for app notifications & alerts | `traefik_proxy` |

Configure apps (Radarr, Sonarr, Paperless, Mealie, etc.) to use:

- Host: `mail`
- Port: `2500`
- Security/Auth: **disabled** (they’re just talking to the internal relay)

The relay itself takes care of TLS and authentication to Mailgun.

---

## Notes for future

- To switch providers (e.g., Mailgun → SES), just update the `SMTP_*` env vars in this stack; apps can keep using `mailgun:2500`.
- Keep `MAILGUN_SMTP_USER` / `MAILGUN_SMTP_PASSWORD` out of git by defining them in a `.env` file or Portainer’s env UI.
- If another internal network is added later (not on `traefik_proxy`), either:
  - Attach `mailgun` to that network as well, or
  - Add a dedicated “infra” network just for utility services and join apps that need email.
- If outbound mail ever misbehaves, check:
  - Relay logs for TLS/auth errors.
  - Mailgun dashboard for rate limits or bounces.