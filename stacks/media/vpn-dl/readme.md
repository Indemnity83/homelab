# media-vpn-dl

![Exposure-Private](https://img.shields.io/badge/Exposure-Private-blue)

Download stack providing **VPN‑routed qBittorrent and SABnzbd** behind a single **Gluetun** VPN container.

Gluetun handles all VPN connectivity, firewalling, IP leak protection, and exposes the two web interfaces (qBittorrent & SABnzbd) through Traefik with an allowlist.

---

## What This Stack Does

- Routes all BitTorrent and Usenet downloading through **Gluetun**.
- Ensures all traffic is blocked unless the VPN is connected.
- Exposes qBittorrent (`qb.*`) and SABnzbd (`sab.*`) safely via Traefik.
- Shares the `media` volume for consistent download locations.
- Uses `network_mode: service:gluetun` so qBittorrent and SABnzbd inherit Gluetun's network stack.
- Uses global defaults for `PUID`, `PGID`, `UMASK`, and `TZ`.

---

## Required Environment Variables (stack-specific)

| Variable           | Purpose                                 |
|--------------------|-----------------------------------------|
| `OPENVPN_USER`     | VPN provider username                    |
| `OPENVPN_PASSWORD` | VPN provider password                    |
| `SERVER_COUNTRIES` | Controls Gluetun exit region             |
| `VPN_SERVICE_PROVIDER` | Provider selection (Privado here)    |

---

## Routing

### Traefik

| Hostname                       | Purpose              | Middleware       |
|--------------------------------|----------------------|------------------|
| `qb.stephandkyle.us`           | qBittorrent UI       | `ip-allowlist`   |
| `sab.stephandkyle.us`          | SABnzbd UI           | `ip-allowlist`   |

### Internal

Since qBittorrent and SABnzbd use `network_mode: service:gluetun`, they only have internal reachability via Gluetun.

| URL                    | Purpose                 | Network         |
|------------------------|-------------------------|-----------------|
| `http://gluetun:8081`  | qBittorrent UI         | `traefik_proxy` |
| `http://gluetun:8080`  | SABnzbd UI             | `traefik_proxy` |

These ports match the `FIREWALL_INPUT_PORTS` exposed inside Gluetun.

---

## Notes for Future Reference

- `FIREWALL_INPUT_SUBNETS` must include the Traefik network CIDR so Traefik can reach the services.
- qBittorrent and SABnzbd **inherit** Gluetun’s network namespace, so they do not get separate IPs.
- The healthcheck on Gluetun ensures:
  - Tun interface is up
  - External connectivity works via VPN
- If downloads ever hang, check whether your VPN provider rate limits or restricts certain protocols.
- Add more containers (e.g., NZBHydra2, Flood) by placing them behind this same Gluetun service.