# Mautic on FrankenPHP

Production-ready Docker image for running **Mautic** on **FrankenPHP** with a
**release-driven CI pipeline**.

This repository provides:
- a multi-stage Docker build for Mautic
- role-based containers (web / cron / worker)
- production Docker Compose setup
- automated CI that rebuilds images on new Mautic releases and new base images

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
  - scheduled builds only when a new Mautic version or a new base image (PHP and Alpine fixes) is out
  - forced rebuilds on infrastructure changes
- Images published to **GitHub Container Registry**

---

## 📦 Docker Images

Images are published to:
```
ghcr.io/mapiiik/mautic-frankenphp
```

Available tags:
- `latest` – latest recommended Mautic version (uses the latest supported PHP)
- `<mautic-version>` – specific Mautic release (e.g. `7.0.1`)
- `<major>.<minor>` – latest patch release within a minor series (e.g. `7.0`)
- `<major>` – latest stable release within a major series (e.g. `7`)
- `sha-<commit>` – infrastructure build reference
- `build-<mautic-version>-<base-digest>` – the build key the scheduled runs compare against

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
- `SERVER_NAME` (automatic ACME certificates; otherwise self-signed HTTPS)
- `LISTENER_MODE` (`direct` by default; `behind-proxy` behind an SNI proxy such as HAProxy, which owns
  port 80, so certificates come by the TLS-ALPN challenge; `internal` only on an internal network: Caddy
  issues the certificate from its own CA and `SERVER_NAME` can be the server's IP address. Browsers warn
  until they trust its root certificate, `/data/caddy/pki/authorities/local/root.crt` in the container)
- `PROXY_ALLOW` (where the PROXY protocol header is trusted from: the proxy's network; by default
  only this machine. The header is optional, so it works with or without a proxy)
- `DATA_DIR` (configuration, logs and media; default `./data`)
- `DOCKER_MAUTIC_RUN_MIGRATIONS`
- `DOCKER_MAUTIC_ENABLE_EMAIL_FETCH`
- `MAUTIC_CONFIG_PARAMETERS` (optional, JSON)

The image supports official Mautic configuration via environment variables
as an alternative to `config/local.php`.

## 🧠 CI Behavior
- Scheduled runs build only when the Mautic release or the base image digest changed. `BASE_IMAGE`
  in the workflow has to match the `FROM` lines in `docker/Dockerfile.production`.
- Pushes to main always trigger a rebuild.
- No repository changes are made by CI.

## 📄 License
See individual components for their respective licenses.
