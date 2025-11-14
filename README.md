🏡 Homelab Stacks

Infrastructure-as-code for my Unraid + Portainer environment

This repository contains all Docker Compose stacks used in my homelab, managed through Portainer. The goal is to keep my entire container ecosystem version-controlled, reproducible, and portable — independent of hardware or Unraid-specific UI.

⸻

📦 What’s Inside

The repo is organized by functional namespaces, with each directory containing the compose stack, and readme files:

infra-traefik/        # Reverse proxy + Cloudflare DNS ACME
infra-portainer/      # Portainer agent deployment
infra-watchtower/     # Automated container updates
infra-mail/           # An email relay server
infra-backup/         # Off-site backups (Backblaze)

media-vpn-dl/         # Gluetun VPN + qBittorrent + SABnzbd
media-automation/     # Sonarr/Radarr/Lidarr/Readarr/Bazarr/Prowlarr/Recyclarr
media-servers/        # Plex Media Server
media-requests/       # Ombi request portal

app-ai-chat/          # Open-WebUI (AI front-end)
app-webtrees/         # Webtrees genealogy site
app-mealie/           # Mealie recipe manager
app-speedtest/        # Speedtest front-end

⸻

🧩 Architecture Principles

✔ Everything is Stack-based

All services run via Compose stacks, not individual containers.

✔ Namespaced for sanity

Each stack follows the namespace-thing format:
	•	infra-* → foundational infrastructure
	•	media-* → Plex + automation ecosystem
	•	app-* → standalone applications
	•	iot-* → embedded / home automation

✔ Traefik handles all routing

Every publicly-exposed service uses:
	•	ACME DNS-01 with Cloudflare
	•	Wildcard certificates
	•	Secure, automatic routing
	•	IP allowlists / rate limits where needed

✔ Portainer manages — Git defines

Portainer deploys stacks, but this repo is the source of truth.
If the server is replaced, restoring is as simple as:
	1.	Clone repo
	2.	Connect Portainer
	3.	Deploy stacks from Git
	4.	Restore volumes

⸻

🚀 Deployment Flow

Local → Git → Portainer
	1.	Edit stack in VS Code
	2.	Commit + push
	3.	Portainer auto-pulls updates (if configured)
	4.	Changes roll out cleanly

Or apply manually:
Portainer → Stacks → Pull latest → Update

⸻

🔐 Secrets Handling

This repo avoids storing real secrets.
For services that require passwords:
	•	Use .env files (in env-template form)
	•	Or Portainer’s built-in secrets
	•	Or Unraid’s pass-through environment variables

Anything sensitive goes into:

.env
*/secrets/*

…and should be listed in .gitignore.

⸻

🛠 Requirements
	•	Docker (managed via Unraid)
	•	Portainer CE
	•	Cloudflare-managed DNS (ACME DNS-01)
	•	A Traefik ingress stack (defined in infra-traefik/)
	•	Persistent storage under /mnt/user/appdata/

⸻

🧯 Disaster Recovery

To rebuild the homelab on new hardware:
	1.	Reinstall Docker + Portainer
	2.	Clone this repo
	3.	Deploy stacks from Git
	4.	Restore /mnt/user/appdata/*
	5.	Restore media libraries (if applicable)

Everything else self-heals.

⸻

📋 Roadmap / Wishlist
	•	Automated pre-flight validation for stacks
	•	GitHub Actions for linting compose files
	•	Optional staging cluster for testing upgrades

⸻

🧑‍💻 Author

Kyle Klaus
A man on a multi-month journey to refactor 40+ rogue containers into a civilized, elegant, version-controlled homelab.
