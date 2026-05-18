KidsEduPlay Backend Skeleton

Quick start:

1. Install dependencies locally:

```bash
composer install
```

2. Copy `.env.example` to `.env` and set DB and JWT secrets.

3. Run migrations (MySQL) and then start a PHP built-in server for testing:

```bash
php -S 127.0.0.1:8000 -t public
```

Endpoints:
- `POST /api/v1/auth/login` { email, password }
- `POST /api/v1/auth/refresh` { refresh_token }
- `GET /api/v1/me` (requires `Authorization: Bearer <token>`)

Notes:
- This is a minimal scaffold for development and demonstration. Replace with a proper router, DI container, and full services for production.
- This is a minimal scaffold for development and demonstration. Replace with a proper router, DI container, and full services for production.

Production checklist

- **Environment:** create `.env` from `.env.production.example` and set all secrets (especially `JWT_SECRET`).
- **Dependencies:** run `composer install --no-dev --optimize-autoloader` on the server or build locally and upload `vendor/`.
- **Document root:** ensure the web server serves the `public/` directory.
- **Migrations:** run `php migrations/run_migrations.php` (or import `migrations/*.sql` via phpMyAdmin) to create the database schema.
- **Uploads:** ensure `UPLOAD_PATH` exists and is writable by the webserver (chmod 755/775 as needed).
- **Security:** set `APP_ENV=production`, `APP_DEBUG=false`, enable HTTPS, and keep `JWT_SECRET` private.

Quick deploy steps

```bash
# on server (with composer/ssh)
composer install --no-dev --optimize-autoloader
cp .env.production.example .env
# edit .env to set DB credentials and JWT_SECRET
php migrations/run_migrations.php --host=127.0.0.1 --user=dbuser --pass=dbpass --db=kidseduplay --dir=migrations
```

See `README-hostinger.md` for Hostinger-specific instructions.
