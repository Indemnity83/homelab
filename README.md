# 🏡 Homelab Stacks

Infrastructure-as-code for my Unraid + Portainer environment

This repository contains all Docker Compose stacks used in my homelab, managed through Portainer. The goal is to keep my entire container ecosystem version-controlled, reproducible, and portable — independent of hardware or Unraid-specific UI.

---

## 📦 What’s Inside

This repo is organized into namespaces to keep stacks logical and discoverable.  
Each namespace represents a functional area of the homelab.

---

### 🏛 infra-* — Core Infrastructure  
Foundational services required for the homelab to operate: networking, routing, backups, system management.

| Stack / Directory       | Description                                    |
|-------------------------|------------------------------------------------|
| infra-ai.               | Open-WebUI (AI front-end) & Claud Proxy        |
| infra-traefik           | Reverse proxy + Cloudflare DNS ACME           |
| infra-portainer         | Portainer agent deployment                     |
| infra-watchtower        | Automated container updates                    |
| infra-mail              | Internal email relay server                    |
| infra-backup            | Off-site backups (Backblaze / Duplicacy)      |

---

### 🎬 media-* — Media Servers & Automation  
Everything related to Plex, downloading, organizing, and managing media.

| Stack / Directory       | Description                                    |
|-------------------------|------------------------------------------------|
| media-vpn-dl            | Gluetun VPN + qBittorrent + SABnzbd           |
| media-automation        | Sonarr/Radarr/Lidarr/Readarr/Bazarr/Prowlarr/Recyclarr |
| media-servers           | Plex & Audiobookshelf Media Server             |
| media-requests          | Ombi request portal                            |
| media-photos            | Immich server and services                            |

---

### 📱 app-* — Standalone Applications  
Apps that serve a single purpose and do not belong to another ecosystem.

| Stack / Directory       | Description                                    |
|-------------------------|------------------------------------------------|
| app-webtrees            | Webtrees genealogy site                        |
| app-mealie              | Mealie recipe manager                          |
| app-speedtest           | Speedtest front-end                            |
| app-homepage            | Homepage Dashboard (built mostly by these stacks) |

---

### 🏠 iot-* — Home Automation (Future Namespace)  
Reserved for ESPHome, MQTT, Zigbee2MQTT, and other embedded/automation services if they're ever migrated to Portainer.

| Stack / Directory       | Description                                    |
|-------------------------|------------------------------------------------|
| iot-esphome             | ESPHome device manager/builder                  |

---

## 🧩 Architecture Principles

✔ Everything is Stack-based

All services run via Compose stacks, not individual containers.

✔ Namespaced for clarity  

Stacks are grouped using a simple prefix convention (infra-, media-, app-, iot-) to keep the repo organized without needing to restate each namespace elsewhere.

✔ Traefik handles all routing

Every publicly-exposed service uses:  
- ACME DNS-01 with Cloudflare  
- Wildcard certificates  
- Secure, automatic routing  
- IP allowlists / rate limits where needed  
- Designed to support future SSO via Authentik  

✔ Portainer manages — Git defines

Portainer deploys stacks, but this repo is the source of truth.  
If the server is replaced, restoring is as simple as:  
1. Clone repo  
2. Connect Portainer  
3. Deploy stacks from Git  
4. Restore volumes  

---

## 🚀 Deployment Flow

Local → Git → Portainer  
1. Edit stack in VS Code  
2. Commit + push  
3. Portainer auto-pulls updates (if configured)  
4. Changes roll out cleanly  

Or apply manually:  
`Portainer → Stacks → Pull latest → Update`

---

## 🔐 Secrets Handling

This repo avoids storing real secrets.  
For services that require passwords:  
- Use `.env` files (in env-template form)  
- Or Portainer’s built-in secrets  
- Or Unraid’s pass-through environment variables  

Anything sensitive goes into:  

```
.env
*/secrets/*
```

…and should be listed in `.gitignore`.

---

## 🛠 Requirements

- Docker (managed via Unraid)  
- Portainer CE  
- Cloudflare-managed DNS (ACME DNS-01)  
- A Traefik ingress stack (defined in infra-traefik/)  
- Persistent storage under `/mnt/user/appdata/`  

---

## 🧯 Disaster Recovery

To rebuild the homelab on new hardware:  
1. Reinstall Docker + Portainer  
2. Clone this repo  
3. Deploy stacks from Git  
4. Restore `/mnt/user/appdata/*`  
5. Restore media libraries (if applicable)  

Everything else self-heals.

---

## 📋 Roadmap / Wishlist

- Automated pre-flight validation for stacks  
- GitHub Actions for linting compose files  
- Optional staging cluster for testing upgrades  
- Integrate Authentik for unified authentication and SSO across all public-facing apps  

---

## 🧑‍💻 Author

Kyle Klaus  
A man on a multi-month journey to refactor 40+ rogue containers into a civilized, elegant, version-controlled homelab.
