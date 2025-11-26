# infra-homepage

![<Public Stack](https://img.shields.io/badge/Exposure-Public-orange?style=flat-square)
![<Private> Stack](https://img.shields.io/badge/Exposure-Private-blue?style=flat-square)

<Onetwo sentence description of what this stack is / why it exists.>

---

## What This Stack Does

- <Bullet 1  main function>
- <Bullet 2  supporting function>
- <Bullet 3  any key design decision (VPN, no local models, etc.)>

---

## Required Environment Variables

Ensure these exist in the global Portainer environment or in a stack-local `.env` file:

| Variable        | Purpose                            |
|-----------------|------------------------------------|
| `<VAR_NAME>`    | <What it does / who issues it>     |

---

## Routing

### Traefik

| Hostname            | Purpose                 | Middleware      |
|---------------------|-------------------------|-----------------|
| `<host.example>`    | <What lives there>      | `<middleware>`  |

### Internal

| URL                      | Purpose                 | Network         |
|--------------------------|-------------------------|-----------------|
| `http://service:port`    | <What this endpoint is> | `<docker-net>`  |

---

## Notes for Future Reference

- <Gotcha or recovery step #1>
- <Gotcha or recovery step #2>
