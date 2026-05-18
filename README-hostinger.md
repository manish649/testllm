KidsEduPlay — Hostinger Deployment Guide

Overview

This guide explains how to deploy the KidsEduPlay PHP/MySQL backend to Hostinger (shared hosting or VPS). It assumes PHP 8+, MySQL, and that you can change the site document root to the `public/` folder. Follow the steps below for a secure production deployment.

Requirements

- PHP 8.0 or later (with PDO MySQL, OpenSSL, mbstring)
- MySQL 5.7+ / MariaDB equivalent
- Composer (optional on Hostinger; you can run locally then upload `vendor/`)
- SSH access recommended but not required

Prepare locally (recommended)

1. Create a strong JWT secret:

```bash
# Linux / macOS
openssl rand -hex 32
# or PHP
php -r "echo bin2hex(random_bytes(32));"
```

2. Install PHP dependencies locally (if Hostinger doesn't provide composer/SSH):

```bash
composer install --no-dev --optimize-autoloader
```

3. Create `.env` from `.env.example` and set required values (see "Environment Variables").

Upload options

- Git (Hostinger Git or GitHub integration) — ideal for repeatable deploys.
- FTP / SFTP — upload project files.
- SSH and Composer — best-case: run `composer install` on server.

Important: The webserver document root must point to the project `public/` directory.
In Hostinger hPanel: Websites → Manage → Advanced → Set Document Root to `public` (or place project inside `public_html` with `public` contents moved as appropriate).

Environment Variables

Required variables (minimum):

- `DB_HOST` — MySQL host
- `DB_PORT` — MySQL port (3306)
- `DB_NAME` — database name
- `DB_USER` — database username
- `DB_PASS` — database password
- `JWT_SECRET` — long random secret (see above)
- `JWT_ALGO` — e.g. `HS256`
- `UPLOAD_PATH` — path for uploads (defaults to `storage/uploads`)

On Hostinger, you can store secrets in a `.env` file at the project root. Ensure `.env` is not published publicly and is included in `.gitignore`.

Database setup / migrations

Option A — SSH / CLI (recommended):

```bash
# from project root on server
php migrations/run_migrations.php
```

Option B — phpMyAdmin (no CLI):

1. Open Hostinger hPanel → Databases → phpMyAdmin.
2. Create a new database and user, assign privileges.
3. Import SQL files from the `migrations/` folder in order (01_.. then 02_.. etc.).

If importing fails due to foreign keys, import base tables first (users, organizations), then dependent tables.

File permissions

- `storage/uploads` (or `UPLOAD_PATH`) must be writable by PHP (chmod 755 or 775 depending on host).
- Do NOT make directories world-writable (777) unless absolutely necessary.

Composer on Hostinger

- If Hostinger provides SSH and Composer, run `composer install --no-dev --optimize-autoloader` on the server.
- If not, run Composer locally and upload the `vendor/` directory.

Set document root

Ensure the site serves from the repository `public/` directory. If Hostinger only accepts `public_html`, move or point content so that `public/index.php` becomes the served index.

Running and testing

- Local test server:

```bash
php -S localhost:8000 -t public
# then visit http://localhost:8000/api/v1/auth/login
```

- On Hostinger, open your domain over HTTPS and test the endpoints with curl or Postman.

Example curl (login):

```bash
curl -X POST https://yourdomain.com/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"secret"}'
```

Troubleshooting

- 500 errors: check PHP error log (Hostinger hPanel → Logs). Ensure `display_errors` is off in production and check error log instead.
- Missing vendor/: ensure Composer was run or `vendor/` uploaded.
- Database connection errors: verify `DB_*` env values and user privileges.
- Wrong document root: ensure web root is `public/` or contents of `public/` are exposed as site root.

Security recommendations

- Always use HTTPS (enable SSL in hPanel).
- Use a long `JWT_SECRET` and keep it private.
- Turn off `display_errors` in production PHP settings.
- Regularly back up the database and `storage/uploads`.
- Keep Composer dependencies up-to-date and monitor CVEs.

Misc notes & housekeeping

- If you rely on background jobs or scheduled tasks, use Hostinger Cron Jobs with appropriate PHP commands.
- For uploading large files, consider uploading directly to an object store (S3 compatible) rather than host filesystem.
- Monitor logs via hPanel and rotate logs periodically.

If you'd like, I can:
- Add a small `deploy.sh` script for Git/SFTP uploads,
- Generate a production-ready `.env.example` with required keys,
- Or prepare step-by-step screenshots for Hostinger's hPanel.


---
Generated for the KidsEduPlay project. Follow these steps to get the app running on Hostinger. Good to go—want me to add a `deploy.sh` or commit this file for you?
