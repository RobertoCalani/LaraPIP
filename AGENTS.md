# LaraPip AGENTS.md

## What is this?

A bash script installer (`install`) that scaffolds Docker-based Laravel development environments. Generates `docker-compose.yml`, `Dockerfile`, `larapip.sh`, and other config files.

## Two translation systems to maintain

When adding new UI strings, update **both**:

- **Bash script** (`install`): `T[key_en]` / `T[key_es]` arrays with `t()` function
- **HTML page** (`index.html`): `LANG.en[key]` / `LANG.es[key]` JavaScript objects

## Development

```bash
bash -n install      # Syntax check
bash install         # Test locally (interactive prompts)
```

**No automated tests** - manual verification required by running the script.

## Key Architecture Points

- **`install` is a generator** - running it creates new files in target directory; it doesn't modify itself
- **Docker images**: `dunglas/frankenphp:{version}-php{php_version}`, `mysql:8.0`, `mariadb:11.4`, `postgres:16-alpine`, `sqlserver:2022-latest`, `node:{18|20|22}`
- **SSL modes**: 
  - `none`: HTTP port 80
  - `auto`: Caddy self-signed certs
  - `manual`: Requires existing `config_docker/ssl/cert.pem` and `config_docker/ssl/key.pem`

## Generated Files Overview

| File | Source Function |
|------|-----------------|
| `docker-compose.yml` | `generate_docker_compose()` |
| `docker-compose.override.yml` | `generate_override()` (only if optional services selected) |
| `larapip.sh` | `generate_launcher()` |
| `artisan` (bridge script) | `generate_bridge_scripts()` |
| `composer` (bridge script) | `generate_bridge_scripts()` |
| `config_docker/php/Dockerfile` | `generate_dockerfile()` |
| `config_docker/frankenphp/Caddyfile` | `generate_caddyfile()` |
| `config_docker/mysql/my.cnf` | `generate_mysql_conf()` |

## Critical Functions to Understand

- `ensure_db_user()` - Complex MySQL/MariaDB user creation with `mysql_native_password` auth plugin
- `detect_existing_db_config()` - Reads `.env` for existing DB_* variables
- `initialize_project()` - Runs `composer install` and `php artisan migrate --force`
- `check_existing_docker_resources()` - Detects and handles existing containers/images with same project name

## Port Mappings

These are checked for conflicts during installation:

| Service | Port |
|---------|------|
| App (HTTP/HTTPS) | 80, 443 |
| Database | 3306 (MySQL/MariaDB), 5432 (PostgreSQL), 1433 (SQL Server) |
| Node/Vite | 5173 |
| Mailpit | 1025, 8025 |
| Redis | 6379 |
| phpMyAdmin/pgAdmin/Adminer | 8080 |
| MinIO | 9000, 9001 |

## Publishing

GitHub Pages from `gh-pages` branch. URL: `https://robertocalani.github.io/LaraPIP/`

Update with:
```bash
git push origin HEAD:refs/heads/gh-pages
```

## Gotchas

1. **Bridge scripts are wrappers** - `./artisan` and `./composer` run `docker compose exec app` - they're not the real Laravel files
2. **Container names use project prefix** - e.g., `myproject-app`, `myproject-database`
3. **Database volume is always `<project>_database`** - inspect with `./larapip.sh db:volume`
4. **Laravel 11 is EOL** since March 12, 2026 - warn users to prefer Laravel 12 or 13
5. **Existing resources detection** - Before generation, the script checks for existing containers/images. If found, prompts to clean or rename project

## New Translations Added

For `check_existing_docker_resources()` function, added to `install` script:

| Key | English | Spanish |
|-----|---------|---------|
| `existing_res_0/1` | "Existing Docker resources detected for project" | "Recursos Docker existentes detectados para el proyecto" |
| `existing_res_found_0/1` | "Found:" | "Encontrados:" |
| `existing_res_clean_0/1` | "Do you want to clean up existing resources? [Y/n]" | "¿Desea limpiar los recursos existentes? [Y/n]" |
| `existing_res_remove_0/1` | "Removing existing containers and images..." | "Eliminando contenedores e imágenes existentes..." |
| `existing_res_removed_0/1` | "Existing resources cleaned up" | "Recursos existentes eliminados" |
| `existing_res_rename_0/1` | "Enter a new project name" | "Ingrese un nuevo nombre de proyecto" |