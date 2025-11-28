# media-automation

![Exposure-Private](https://img.shields.io/badge/Exposure-Private-blue)

Automation stack for managing media libraries using the *Arr ecosystem.

Handles indexers, movies, TV, music, books, adult content, subtitles, and periodic quality/profile sync — all running behind Traefik with IP allowlisting.

---

## What This Stack Does

- Runs **Prowlarr** as a central indexer and search aggregator.
- Automates libraries with:
  - **Radarr** for movies
  - **Sonarr** for TV series
  - **Lidarr** for music
  - **Readarr** for ebooks & audiobooks
  - **Whisparr** for adult media *(optional; disabled by default in this repo)*
  - **Bazarr** for subtitles (integrates with Radarr/Sonarr)
  - **Decluttarr** to automatically clean stalled, failed, orphaned, or metadata-missing downloads
- Uses **Recyclarr** to keep quality profiles and custom formats in sync (e.g., with TRaSH guides).
- Shares a common `media` volume for consistent paths across automation and downloader stacks.
- Exposes all UIs via Traefik with HTTPS and IP allowlisting.

---

## Required Environment Variables (stack-specific)

| Variable          | Purpose                                      |
|-------------------|-----------------------------------------------|
| `CRON_SCHEDULE`   | Recyclarr schedule for syncing configs (e.g. `@daily`) |

---

## Routing

### Traefik

| Hostname                      | Purpose                              | Middleware       |
|-------------------------------|--------------------------------------|------------------|
| `prowlarr.stephandkyle.us`    | Indexer manager & search aggregator  | `ip-allowlist`   |
| `radarr.stephandkyle.us`      | Automated movie management           | `ip-allowlist`   |
| `sonarr.stephandkyle.us`      | Automated TV series management       | `ip-allowlist`   |
| `lidarr.stephandkyle.us`      | Music library manager                | `ip-allowlist`   |
| `readarr.stephandkyle.us`     | E-book & audiobook manager           | `ip-allowlist`   |
| `bazarr.stephandkyle.us`      | Subtitle management                  | `ip-allowlist`   |
| `whisparr.stephandkyle.us`    | Adult media manager                  | `ip-allowlist`   |

### Internal

| URL                          | Purpose                              | Network         |
|------------------------------|--------------------------------------|-----------------|
| `http://prowlarr:9696`       | Prowlarr HTTP API/UI                 | `traefik_proxy` |
| `http://radarr:7878`         | Radarr HTTP API/UI                   | `traefik_proxy` |
| `http://sonarr:8989`         | Sonarr HTTP API/UI                   | `traefik_proxy` |
| `http://lidarr:8686`         | Lidarr HTTP API/UI                   | `traefik_proxy` |
| `http://readarr:8787`        | Readarr HTTP API/UI                  | `traefik_proxy` |
| `http://bazarr:6767`         | Bazarr HTTP API/UI                   | `traefik_proxy` |
| `http://whisparr:6969`       | Whisparr HTTP API/UI                 | `traefik_proxy` |

---

## First-Time Initialization Notes

Some services (most notably **Decluttarr**) require API keys from Radarr, Sonarr, Prowlarr, etc.  
Because these applications do **not** support providing API keys via environment variables, the initial startup must follow this sequence:

1. Deploy the stack as-is. Errors from Decluttarr and Homepage widgets are expected on first boot.
2. Start Radarr/Sonarr/Lidarr/Readarr/Prowlarr and configure them normally.
3. Retrieve each application's API key from its UI.
4. Add the API keys to your `.env` file or Portainer environment variables.
5. Redeploy the stack (or restart only Decluttarr and Homepage) to pick up the new keys.

After this initial cycle, no further manual steps are needed.

---

## Notes for Future Reference

- All *Arr services share the same `media` volume so that download and final library paths stay consistent with the downloader/VPN stack.
- Prowlarr should be the single place to manage indexers; Sonarr/Radarr/Lidarr/Readarr/Whisparr should mostly receive indexers via Prowlarr sync.
- Bazarr connects to Sonarr and Radarr using the same internal hostnames and ports defined above.
- Recyclarr runs on a cron schedule (`CRON_SCHEDULE`) and reads its config from `/config` in `appdata/recyclarr`.
- All UIs are protected by `ip-allowlist@docker` in Traefik, so external access is limited to trusted networks.
- When adding new automation services, follow the same label and hostname pattern for easy discovery and consistency.

- **Decluttarr** runs periodic cleanup tasks (failed downloads, metadata-missing items, stalled torrents, orphaned files).  
  - Decluttarr depends on API keys from the *Arr apps.  
  - This repo includes Decluttarr fully configured, but Whisparr integration is commented out.

- **Whisparr** is included but commented out by default.  
  - Uncomment the Whisparr service and Whisparr block under Decluttarr if using adult‑media automation.
  - All routing, network, and internal URLs are already included in tables above.