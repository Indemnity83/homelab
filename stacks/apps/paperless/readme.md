# app-paperless

![Exposure-Private](https://img.shields.io/badge/Exposure-Private-blue)

Self-hosted document archive using Paperless-ngx.

Digitizes and organizes scanned documents into a searchable archive, with access restricted to trusted networks via Traefik IP allowlisting.

---

## What This Stack Does

- Runs Paperless-ngx for ingesting, tagging, and searching documents.
- Uses Redis for task queueing and background processing.
- Stores application data and scanned documents in Unraid shares.
- Exposes the web UI through Traefik over HTTPS, locked down to internal networks.

---

## Required Environment Variables (stack-specific)

| Variable               | Purpose                                            |
|------------------------|----------------------------------------------------|
| `PAPERLESS_TIME_ZONE`  | Timezone for document timestamps and UI            |
| `PAPERLESS_URL`        | External URL used for links and callbacks          |
| `PAPERLESS_REDIS`      | Redis connection string for Paperless background tasks |
| `PAPERLESS_OCR_LANGUAGE` | Default OCR language for document processing    |

> _Optional but recommended_: configure `PAPERLESS_SECRET_KEY` in a separate `.env` file for stronger security.

---

## Routing

### Traefik

| Hostname                    | Purpose                    | Middleware        |
|-----------------------------|----------------------------|-------------------|
| `paperless.stephandkyle.us` | Paperless-ngx web interface | `ip-allowlist`    |

### Internal

| URL                        | Purpose                        | Network         |
|----------------------------|---------------------------------|-----------------|
| `http://paperless:8000`    | Main Paperless-ngx HTTP service | `traefik_proxy` |
| `redis://paperless-redis:6379` | Redis for background tasks  | `paperless_net` |

---

## Notes for Future Reference

- The `consume` directory is watched for new files and will automatically import anything dropped there.
- The `export` directory can be used to bulk export documents and metadata.
- Adjust `PAPERLESS_OCR_LANGUAGE` if more languages are needed; multiple codes can be configured in the UI.
- Redis is isolated on the `paperless_net` network and only reachable by the Paperless container.
- If performance degrades with a growing archive, consider moving from SQLite to a dedicated database backend and updating the stack accordingly.