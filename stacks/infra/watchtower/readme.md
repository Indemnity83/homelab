# infra-watchtower

Automatic container image updater using **Watchtower**.

Runs in the background on the Docker host and keeps selected containers up to date based on image tags and labels. This stack has no HTTP interface and is not exposed through Traefik.

---

## What This Stack Does

- Monitors running containers on the host via the Docker socket.
- Only updates containers explicitly opted-in with a label.
- Restarts updated containers one-by-one (rolling restart).
- Cleans up old images after successful updates.
- Runs on a daily cron schedule in the early morning.

---

## Required Environment Variables (stack-specific)

This stack does not require any stack-specific environment variables.

---

## Routing

This stack does **not** expose any HTTP services and does not define Traefik routes.

---

## Notes for Future Reference

- Watchtower is configured with `--label-enable`, so it only touches containers that have:
  - `com.centurylinklabs.watchtower.enable=true`
- The cron schedule is `0 17 3 * * *`, which means:
  - **Second**: 0
  - **Minute**: 17
  - **Hour**: 3 (3:17 AM)
  - **Day of month**: * (every day of the month)
  - **Month**: * (every month)
  - **Day of week**: * (every day of the week)
- `--rolling-restart` ensures containers are restarted one at a time instead of all at once.
- `--cleanup` removes old images after a successful update to save disk space.
- `--include-stopped=false` and `--revive-stopped=false` ensure containers that are intentionally stopped stay that way.
- To temporarily pause automated updates, stop the `infra-watchtower` stack in Portainer; nothing else needs to change in other stacks.