# Mautic on FrankenPHP

Production-ready Docker image for running **Mautic** on **FrankenPHP** with a
**release-driven CI pipeline**.

This repository provides:
- a multi-stage Docker build for Mautic
- role-based containers (web / cron / worker)
- production Docker Compose setup
- automated CI that rebuilds images on new Mautic releases

---

## 🚀 Features

- **FrankenPHP** runtime (Caddy + PHP in one process)
- **Multi-stage build** with prebuilt assets
- **Non-root runtime**
- **Role-based containers**:
  - `mautic_web`
  - `mautic_cron`
  - `mautic_worker`
- **Release-driven CI**
  - scheduled builds only when a new Mautic version is released
  - forced rebuilds on infrastructure changes
- Images published to **GitHub Container Registry**

---

## 📦 Docker Images

Images are published to:
```
ghcr.io/mapiiik/mautic-frankenphp
```

Available tags:
- `latest` – latest recommended version
- `<mautic-version>` – specific Mautic release
- `sha-<commit>` – infrastructure build reference

---

## 🐳 Usage (Production)

1. Copy environment template:

```bash
cp .env.example .env
```

2. Adjust database credentials and options.

3. Start the stack:

```bash
docker compose -f compose.production.yaml up -d
```
or
```bash
cp compose.production.yaml compose.yaml
docker compose up -d
```

## ⚙️ Configuration
### Environment variables
- `DOCKER_MAUTIC_RUN_MIGRATIONS`
- `DOCKER_MAUTIC_ENABLE_EMAIL_FETCH`
- `MAUTIC_CONFIG_PARAMETERS` (optional, JSON)

The image supports official Mautic configuration via environment variables
as an alternative to `config/local.php`.

## 🧠 CI Behavior
- Scheduled runs check for new Mautic releases and build images only when needed.
- Pushes to main always trigger a rebuild.
- No repository changes are made by CI.

## 📄 License
See individual components for their respective licenses.
