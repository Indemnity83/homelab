# Infra – Backup Stack

![Private Stack](https://img.shields.io/badge/Exposure-Private-blue?style=flat-square)

This stack provides automated backup tooling for the homelab, including Duplicacy Web for managing and monitoring backups and an S3 backup helper for pushing data to object storage.

---

## What This Stack Does

- Runs **Duplicacy Web** for managing backup schedules, retention, and restores.
- Runs a lightweight **S3 backup helper** that syncs selected directories to an S3-compatible backend.
- Centralizes homelab backup operations behind a single, IP‑restricted endpoint.

---

## Required Environment Variables

Ensure these are set in Portainer’s global environment or a stack‑local `.env` file:

| Variable        | Purpose                                |
|-----------------|----------------------------------------|
| `ACCESS_KEY`    | S3 access key used by the s3backup job |
| `SECRET_KEY`    | S3 secret key used by the s3backup job |

> Note: Duplicacy-specific credentials are configured inside the Duplicacy UI, not via environment variables.

---

## Routing

### Traefik

| Hostname                      | Purpose             | Middleware         |
|-------------------------------|---------------------|--------------------|
| `backup.stephandkyle.us`      | Duplicacy Web UI    | `ip-allowlist`     |

### Internal

| URL                         | Purpose                  | Network         |
|-----------------------------|--------------------------|-----------------|
| `http://duplicacy:3875`     | Duplicacy Web UI         | `traefik_proxy` |

---

## Notes for Future Reference

- Duplicacy stores all backup configs under `/config`; ensure this is included in Unraid system backups.
- Backing up the Duplicacy `/config` directory is essential.  
  Duplicacy Web does **not** preserve job settings inside backup destinations.  
  Maintain a **nightly tar.gz snapshot** of `/config` (e.g., in `/mnt/user/backups/duplicacy-config/`), keeping the most recent one or two copies.  
  This ensures a fast, clean restore of schedules, destinations, retention rules, and preferences.
- The S3 backup helper runs on a cron schedule defined by `CRON_SCHEDULE`. Adjust as needed.
- This stack assumes an existing S3 bucket and credentials already configured.
- Only Duplicacy is routed through Traefik; the s3backup container has no external interface.