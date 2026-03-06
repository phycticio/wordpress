This is the comprehensive documentation for the **PHP WordPress Stack**. This documentation covers the architecture, setup, and custom automation tools we've built to ensure a "Plug-and-Play" experience across both macOS and Ubuntu 24.04.

---

# 🌿 WordPress Stack

A high-performance, developer-friendly Docker stack designed for local WordPress development. It uses **PHP-FPM Alpine**, **Nginx**, **MariaDB**, and **Redis**, with automated SSL and project management.

## 🏗 Architecture Overview

* **Engine:** PHP 8.3 FPM (Alpine Linux)
* **Web Server:** Nginx (with automated proxy support)
* **Database:** MariaDB 11.4
* **Cache:** Redis 7 (Alpine)
* **Mail:** Mailpit (SMTP capture for testing)
* **Tools:** WP-CLI, `mkcert` (Local SSL), Custom Bash Management Scripts.

---

## 🚀 Quick Start

### 1. Prerequisites

* **Docker & Docker Compose**
* **mkcert**: `brew install mkcert` (Mac) or `sudo apt install mkcert` (Ubuntu). Run `mkcert -install` once.
* **Local DNS**: Ensure your `HOST_NAME` is in `/etc/hosts` pointing to `127.0.0.1`.

### 2. Environment Setup

Clone a project and create your `.env` file:

```bash
cp sample.env .env

```

Key variables to define:

* `CONTAINER_PREFIX`: Unique prefix for the project (e.g., `wp_`).
* `HOST_NAME`: Your local domain (e.g., `my-site.local`).
* `WORDPRESS_IMAGE_VERSION`: Usually `6.8-fpm-alpine`.

### 3. Initialize SSL

Run the `gcert` script inside the project folder:

```bash
gcert

```

*This generates trusted certificates in `./config/ssl/` based on your `.env` domain.*

### 4. Launch

```bash
docker compose up -d

```

The stack will automatically detect an empty `./app` folder and populate it with WordPress core files.

---

## 🛠 Automation Tools (The "Helper" Scripts)

To maintain a consistent workflow, we use the following global scripts. Place these in `/usr/local/bin/`.

### `container` (Project Manager)

Manages projects via a central registry (`~/.registry`).

* **Usage:** `container [alias] [start|stop|restart|logs]`
* **Example:** `container my_site start`
* **Why:** It finds the folder automatically, runs compose, and opens the browser.

### `gcert` (SSL Generator)

Reads your `.env` and generates certificates.

* **Usage:** Run `gcert` inside any project folder.
* **Logic:** Creates `local-cert.pem` and `local-key.pem` inside `./config/ssl/`.

### `wp-version` (Core Manager)

Downgrades or updates WordPress without touching `wp-content`.

* **Usage:** `wp-version 6.2.2`
* **Logic:** Surgically replaces `wp-admin` and `wp-includes` to match the specific version.

---

## 🐧 Ubuntu 24.04 Compatibility (Permissions)

This stack is optimized for Linux native Docker. To avoid "Permission Denied" errors while keeping files editable:

1. **UID Synchronization:** The `Dockerfile` is configured to map the internal `www-data` user to UID `1000`.
2. **File Ownership:** If you see "Forbidden" errors, run:
```bash
sudo chown -R $USER:$USER app/

```


Since the container user and your Ubuntu user share the same ID (1000), both can now read/write simultaneously.

---

## 📁 Directory Structure

```text
.
├── app/                # WordPress Source Code (Volume)
├── config/
│   ├── php/            # Custom .ini files (uploads, mail, redis)
│   ├── nginx/          # Nginx templates and config
│   ├── ssl/            # Generated SSL certificates
│   └── Dockerfile      # Custom PHP-FPM Image
├── .env                # Project Configuration
└── compose.yml         # Docker Orchestration

```

---

## 💡 Common Commands

**Accessing WP-CLI:**

```bash
docker exec -it [prefix]app wp --info

```

**Viewing Mailpit (Captured Emails):**
Open `https://mail.yourdomain.local` in your browser.

**Cleaning up Docker (Idle resources):**

```bash
docker system prune -a

```

**Reloading Nginx config without restart:**

```bash
docker exec [prefix]server nginx -s reload

```

---

## 📝 Best Practices for Developers

1. **Always use the Registry:** Add your project to `~/.registry` to use the `container` shortcuts.
2. **Don't Edit Core:** Keep all custom logic in `wp-content`.
3. **Environment Variables:** If a script needs a database password, read it from the environment (`$WORDPRESS_DB_PASSWORD`) rather than hardcoding it.