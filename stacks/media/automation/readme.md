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
  - **Whisparr** for adult media
  - **Bazarr** for subtitles (integrates with Radarr/Sonarr)
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

## Notes for Future Reference

- All *Arr services share the same `media` volume so that download and final library paths stay consistent with the downloader/VPN stack.
- Prowlarr should be the single place to manage indexers; Sonarr/Radarr/Lidarr/Readarr/Whisparr should mostly receive indexers via Prowlarr sync.
- Bazarr connects to Sonarr and Radarr using the same internal hostnames and ports defined above.
- Recyclarr runs on a cron schedule (`CRON_SCHEDULE`) and reads its config from `/config` in `appdata/recyclarr`.
- All UIs are protected by `ip-allowlist@docker` in Traefik, so external access is limited to trusted networks.
- When adding new automation services, follow the same label and hostname pattern for easy discovery and consistency.