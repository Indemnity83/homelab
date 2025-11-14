# media-requests

![Exposure-Public](https://img.shields.io/badge/Exposure-Public-orange)

Public-facing media request portal using **Ombi**.

Provides a simple web page where friends and family can request movies and TV shows without needing Plex accounts or Discord bots.

---

## What This Stack Does

- Runs **Ombi** as a centralized request portal for your media library.
- Lets users submit requests for movies and TV shows via a friendly web UI.
- Integrates with Radarr/Sonarr (and others) using internal hostnames and API keys.
- Exposes the Ombi UI through Traefik at `requests.stephandkyle.us`.

---

## Required Environment Variables (stack-specific)

No additional stack-specific environment variables are required.

---

## Routing

### Traefik

| Hostname                    | Purpose                     | Middleware |
|-----------------------------|-----------------------------|------------|
| `requests.stephandkyle.us`  | Ombi media request portal   |      |

### Internal

| URL                        | Purpose                    | Network         |
|----------------------------|-----------------------------|-----------------|
| `http://ombi:3579`         | Ombi HTTP API/UI           | `traefik_proxy` |

---

## Notes for Future Reference

- Use Ombi's built-in authentication and invite features to control who can submit requests.
- Connect Ombi to Radarr/Sonarr using the internal URLs and ports shown above (not the public domains).
- Requests can be auto-approved or require manual approval, depending on Ombi configuration.
- This stack is intentionally public-facing so non-technical family members can use it easily; rely on Ombi's login and permissions for security.
