# Speed Test

![<Public Stack](https://img.shields.io/badge/Exposure-Public-orange?style=flat-square)

A lightweight OpenSpeedTest server exposed publicly so friends, family, and support folks can easily run internet speed tests against your home connection.

Useful for quick diagnostics without requiring VPN access.

---

## What This Stack Does

 - Runs a containerized OpenSpeedTest instance
 - Exposes it publicly through Traefik
 - Applies rate-limiting middleware to protect your upstream bandwidth

---

## Stack-Specific Environment Variables

This stack does not use any unique environment variables.
Global defaults (PUID, PGID, UMASK, TZ) are inherited.

---

## Routing

### Traefik

| Hostname                   | Purpose                      | Middleware |
|----------------------------|------------------------------|------------|
| `speedtest.stephandkyle.us` | Public speed test server     | `rate-limit` |

### Internal

| URL                         | Purpose                  | Network         |
|-----------------------------|--------------------------|-----------------|
| `http://openspeedtest:3000` | Primary application UI   | `traefik_proxy` |


---

## Notes for Future Reference

 - Rate limits are currently configured as average 5 requests and burst 10, which is plenty for human use while preventing automated hammering.
 - The upstream image (openspeedtest/latest) is small and very stable — rarely needs maintenance.
 - This service is intentionally public; if you ever want to restrict it, swap the middleware to your IP allowlist.
