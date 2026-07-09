# Larapip

**Laravel + Docker — Automated Development Environment**

Larapip is an interactive shell script that scaffolds a complete Docker-based Laravel development environment in under two minutes. Answer a few questions and get a production-like local setup with [FrankenPHP](https://frankenphp.dev/), your choice of database, queues, HTTPS, and optional services — all containerized.

```bash
curl -fsSL https://raw.githubusercontent.com/youruser/larapip/main/install | bash
```

Or download the script and run it locally:

```bash
chmod +x install && ./install
```

---

## Features

- **Interactive wizard** — language selection (English / Spanish), guided prompts with validation
- **Multiple Laravel versions** — 11 (EOL), 12, 13 (latest)
- **PHP version selection** — 8.2, 8.3, or 8.4 via FrankenPHP
- **Database engines** — MySQL 8.0, MariaDB 11, PostgreSQL 16, SQLite, SQL Server 2022
- **Optional services** — Mailpit, Redis, phpMyAdmin, pgAdmin, Adminer, MinIO
- **SSL modes** — none (HTTP), automatic self-signed, manual certificates
- **Node.js** — 18, 20, or 22 for frontend builds (Vite/Hot Module Replacement)
- **Task scheduler & queue worker** — separate containers for `schedule:work` and `queue:work`
- **Port conflict detection** — checks for in-use ports before starting containers
- **Database user verification** — automatically creates or verifies database credentials
- **Automated migrations** — runs `php artisan migrate --force` after database is ready
- **Persistent storage** — all database engines use Docker volumes for data persistence
- **Environment manager** — the generated `larapip.sh` wraps `docker compose` with convenient commands
- **Bridge scripts** — `./artisan` and `./composer` run commands inside the app container
- **Backup & restore** — built-in database backup and restore via `./larapip.sh db:backup`

---

## What Gets Generated

After running the installer, the following files are created in your project directory:

```
your-project/
├── docker-compose.yml           # Main Docker services
├── docker-compose.override.yml  # Optional services (if selected)
├── larapip.sh                   # Environment manager
├── artisan                      # Bridge: docker compose exec app php artisan
├── composer                     # Bridge: docker compose exec app composer
├── config_docker/
│   ├── php/
│   │   ├── Dockerfile           # FrankenPHP with extensions
│   │   └── local.ini            # PHP configuration
│   ├── frankenphp/
│   │   └── Caddyfile            # Caddy / FrankenPHP config
│   ├── mysql/
│   │   └── my.cnf               # MySQL configuration
│   └── ssl/                     # SSL certificates (if configured)
└── .env.example                 # Synced from .env
```

### Docker Services

| Service | Container | Description |
|---------|-----------|-------------|
| **app** | `{name}-app` | FrankenPHP serving the Laravel application (ports 80, 443) |
| **task** | `{name}-task` | Runs `php artisan schedule:work` |
| **jobs** | `{name}-jobs` | Runs `php artisan queue:work --tries=3` |
| **node** | `{name}-node` | Node.js with `npm run dev` (Vite HMR on port 5173) |
| **database** | `{name}-database` | Selected database engine |

---

## Usage

### Environment Manager

```bash
./larapip.sh up          # Start all containers
./larapip.sh down        # Stop all containers
./larapip.sh restart     # Restart all containers
./larapip.sh build       # Rebuild app image
./larapip.sh shell       # Open bash in app container
./larapip.sh logs        # Tail logs from all services
./larapip.sh logs app    # Tail logs from app service
./larapip.sh ps          # List running containers
```

### Database Commands

```bash
./larapip.sh db:shell          # Connect to database CLI
./larapip.sh db:volume         # Show volume information
./larapip.sh db:backup         # Dump database to backups/
./larapip.sh db:restore <file> # Restore database from dump
```

### Laravel Commands

```bash
./artisan migrate              # Run migrations (inside container)
./composer require package     # Install Composer packages
./larapip.sh npm run build     # Run npm commands
```

---

## Requirements

- **Docker** (with Compose plugin) — [install guide](https://docs.docker.com/engine/install/)
- **No PHP required on host** — everything runs inside containers
- **OS** — Linux, macOS, or Windows via WSL2

---

## FAQ

**How do I change the database password after setup?**

Edit `.env` and run `./larapip.sh up` to restart services. If a persistent volume already exists, the old credentials still apply — run `docker compose down -v` to recreate the database from scratch.

**The `artisan` file is a shell script?**

Larapip creates `artisan` and `composer` bridge scripts for host-side convenience. The real PHP `artisan` is restored automatically during `initialize_project()` inside the container.

**What is `larapip.sh`?**

An environment manager generated during installation. It wraps `docker compose` and provides convenience commands for database management, running Artisan/Composer/npm, and controlling containers.

**Where is database data stored?**

All database engines store data in a Docker volume named `<project>_database`. Inspect it with `./larapip.sh db:volume`. This ensures consistent backup and volume management regardless of engine.

**Supported on Windows?**

Yes, via WSL2. Install Docker Desktop with WSL2 backend and run the script from your Linux distribution.

---

## Repository Structure

```
larapip/
├── install               # Interactive installer (bash)
├── index.html            # Landing page (GitHub Pages)
└── readme.md             # This file
```

---

## License

MIT
