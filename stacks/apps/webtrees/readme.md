[drop readme here]
# app-webtrees

![Exposure-Public](https://img.shields.io/badge/Exposure-Public-orange)

Self-hosted collaborative family tree website powered by **webtrees**.

This stack provides a simple, SQLite‑backed deployment suitable for small family history projects, publicly accessible via Traefik.

---

## What This Stack Does

- Runs the latest webtrees image with persistent storage.
- Exposes the site at **tracystree.com** via Traefik.
- Automatically redirects **www.tracystree.com** → **tracystree.com**.
- Stores all application data (including the SQLite database) under Unraid `appdata`.

---

## Required Environment Variables (stack‑specific)


> **Note:** On first‑time setup, webtrees normally requires additional installation variables (database configuration, admin account, etc.). These are *not* included here because this stack is intended to run against an already‑initialized deployment. If deploying fresh, temporarily supply the required variables, complete setup, and then remove them for ongoing use.

---

## Routing

### Traefik

| Hostname               | Purpose               | Middleware                |
|------------------------|-----------------------|---------------------------|
| `tracystree.com`       | Primary webtrees site | _None_                    |
| `www.tracystree.com`   | Redirect to apex      | `wt-redirect-www-to-apex` |

### Internal

| URL                   | Purpose               | Network        |
|-----------------------|------------------------|----------------|
| `http://webtrees:80`  | webtrees HTTP service | `traefik_proxy` |

---

## Notes for Future Reference

- Current deployment uses **SQLite**, which is ideal for simple setups and easy backups.
- For larger family trees or heavy multi‑user editing, consider migrating to MariaDB/PostgreSQL in the future.
- Redirect middleware handles all `www.` traffic without needing a separate container.
- Webtrees stores all data inside `/var/www/webtrees/data`; this directory is fully persisted.