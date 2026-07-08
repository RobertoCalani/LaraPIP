# Larapip

**Laravel + Docker — Automated Development Environment**

Larapip is an interactive shell script that scaffolds a complete Docker-based Laravel development environment. Answer a few questions and get a production-like local setup with FrankenPHP, databases, queues, HTTPS, and more — in under two minutes.

```bash
curl -fsSL https://yourdomain.com/installer.sh | bash
```

---

## Repository Structure

```
larapip/
├── installer.sh               # Interactive installer (bash)
├── index.html                 # Landing page (GitHub Pages root)
└── readme.md                  # This file
```

### Landing Page (`index.html`)

Built with **Tailwind CSS** (CDN), the landing page provides documentation, version matrices, and FAQ — all translated to English and Spanish. Features:

- **Dark / Light theme** — toggled by the user, persisted via `localStorage` (with cookie fallback). Default is dark.
- **Language toggle** — EN / ES, also persisted. i18n via `data-i18n` (text content) and `data-i18n-html` (innerHTML) attributes.
- **Tabs** — Features, Versions, Services, Usage, Vanilla Docker, FAQ.
- **Accordion FAQ** — click to expand/collapse, SVG chevron indicators swap between right (collapsed) and down (expanded).

---

## Requirements

**Q: Do I need PHP installed on my host?**

No. Everything runs inside Docker containers. The script uses a temporary Composer container to create the Laravel project.

**Q: Can I use this in production?**

This is designed for local development. The FrankenPHP server is capable of production use, but you should review the SSL and security settings first.

**Q: How do I change the database password after setup?**

Edit `.env` and run `docker compose up -d` to restart the services. If using a persistent volume, the old database credentials will still apply — run `docker compose down -v` to recreate the database from scratch.

**Q: The `artisan` file is a shell script?**

Larapip creates `artisan` and `composer` bridge scripts (`docker compose exec app php artisan "$@"`) for convenience. If the real Laravel artisan PHP file is needed (e.g., `php artisan` inside the container), the `restore_artisan()` function in the installer detects the wrapper and restores the original. This happens automatically during `initialize_project`.

**Q: What is `larapip.sh`?**

It is an environment manager generated during installation. It wraps `docker compose` and provides convenient commands for database management (`db:shell`, `db:volume`, `db:backup`, `db:restore`), running Artisan/Composer/npm, and controlling the containers.

**Q: Where is the database data stored?**

All database engines (MySQL, MariaDB, PostgreSQL, SQL Server) store their data in a single Docker volume named `<project>_database`. You can inspect it with `./larapip.sh db:volume`. This makes backups and volume management consistent regardless of which engine you chose.

**Q: Supported on Windows?**

Yes, via WSL2. Install Docker Desktop with WSL2 backend and run the script from your Linux distribution.

---

## License

MIT
