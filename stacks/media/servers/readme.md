# media-servers

![Exposure-Private](https://img.shields.io/badge/Exposure-Private-blue)

Media server stack providing **Plex** for general media playback and **Audiobookshelf** for audiobook and podcast hosting.

Plex runs in host networking mode for best LAN discovery performance. Audiobookshelf is exposed through Traefik but restricted using an IP allowlist.

---

## What This Stack Does

- Runs **Plex Media Server** with hardware transcoding via NVIDIA GPU.
- Exposes Plex through host networking for DLNA, local discovery, and casting support.
- Runs **Audiobookshelf**, a web-based audiobook and podcast server.
- Provides consistent `/media` paths using the shared `media` volume.
- Uses global defaults for `PUID`, `PGID`, `UMASK`, and `TZ`.

---

## Required Environment Variables (stack-specific)

| Variable                      | Purpose                                   |
|------------------------------|-------------------------------------------|
| `NVIDIA_VISIBLE_DEVICES`     | Defines which GPU Plex can use            |
| `NVIDIA_DRIVER_CAPABILITIES` | Capabilities required for HW transcoding  |

---

## Routing

### Traefik

| Hostname                        | Purpose                       | Middleware       |
|---------------------------------|-------------------------------|------------------|
| `audiobooks.stephandkyle.us`    | Audiobookshelf web interface | `ip-allowlist`   |

Plex is **not** routed through Traefik and is accessed directly or more easily via the plex proxy at https://app.plex.tv.

### Internal

| URL                        | Purpose                          | Network         |
|----------------------------|-----------------------------------|-----------------|
| `http://audiobookshelf:80` | Audiobookshelf API/UI             | `traefik_proxy` |
| `http://calcifer.local:32400/web` | Plex Web UI (host networking) | _Host network_  |

---

## Notes for Future Reference

- Plex benefits significantly from host networking for LAN discovery, native apps, Chromecast, and DLNA.
- `/tmp` is used as Plex’s transcode directory; on Unraid this is RAM-backed. Change to a disk path if memory is limited.
- Audiobookshelf uses the shared `media` volume so it can see audiobooks stored in the same structure used by Radarr/Sonarr/Lidarr if desired.
- Audiobookshelf UI access is restricted by `ip-allowlist@docker` in Traefik, it probably needs to be made public to be useful.
- Ensure the NVIDIA plugin is installed and GPU is available to Docker on the host.