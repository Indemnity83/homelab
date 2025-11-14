# app-ai-chat

![Public Stack](https://img.shields.io/badge/Explosure-Public-orange?style=flat-square)

A lightweight AI chat interface powered by OpenWebUI, routed through Traefik, and backed by OpenAI’s API.  

This stack provides a clean, fast, self‑hosted UI for ChatGPT‑style models without running local LLMs. This setup provides a shared household interface to ChatGPT‑level models using the OpenAI API, allowing multiple users to benefit without each person needing their own $20/mo subscription.

---

## What This Stack Does

- Runs OpenWebUI using the **main‑slim** image (no local models).
- Configures OpenWebUI to use **OpenAI’s API**.
- Hides the UI elements for managing local models (`ENABLE_LOCAL_MODELS=false`)

---

## Required Environment Variables

Ensure these exist in the global Portainer environment or in a stack‑local `.env` file:

| Variable          | Purpose                                      |
|-------------------|----------------------------------------------|
| `OPENAI_API_KEY`  | OpenAI API key for authentication            |

---

## Routing

### Traefik

| Hostname               | Purpose                      | Middleware |
|------------------------|------------------------------|------------|
| `ai.stephandkyle.us`   | Main OpenWebUI interface     |            |
| `chat.stephandkyle.us` | Permanent redirect → `ai.*`  |            |

### Internal

| URL                     | Purpose                      | Network         |
|-------------------------|------------------------------|-----------------|
| `http://openwebui:8080` | Main OpenWebUI backend/API   | `traefik_proxy` |

---

## Notes for Future Reference

- If login/authentication issues occur, delete the file:  
  `.webui_secret_key` inside the data directory to regenerate it automatically.
