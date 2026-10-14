# Development environment (private maintenance)

The existing application uses MySQL-specific SIDB adapters. This setup uses MySQL 8.0, matching the existing DDEV configuration, and does not change the existing schema or application routing.

## Quick start

```sh
docker compose up -d --build
docker compose exec app composer install
docker compose exec app php -m
```

App URL: http://localhost:8080. The database host **inside the app container** is `database`, port `3306`, database `fever`, username `fever`, and password `fever-dev-only` unless overridden.

**Important:** Fever still requires its original application configuration and installation/database schema. Compose does not automatically create Fever's tables or `firewall/config/db.php` and `firewall/config/key.php`. Do not commit credentials or live database dumps. Restore a legally obtained development installation/configuration or adapt the legacy installer privately. The project root is mounted as the Apache document root; it is suitable for local development only, not production hardening.

Environment variables in compose configure MySQL, **not** Fever's existing PHP configuration. Change credentials via `FEVER_DB_PASSWORD` and `FEVER_DB_ROOT_PASSWORD` before first initialization; changing them later does not reset an existing MySQL volume.

## Dependency audit

Composer currently installs **no third-party packages**. The original source vendors a customized/partial SimplePie date parser, PclZip and bespoke SIDB/HTTP/XML utilities in `firewall/app/libs`; replacing these blindly could break compatibility. The `src/` PSR-4 namespace is reserved for new, independently developed classes. Composer autoloading is optional until `composer install` runs.

## PostgreSQL

Not drop-in compatible. The SIDB factory selects PDO/MySQL or MySQLi, and application SQL must be audited (DDL, quoting, types, functions, upserts, IDs, schema evolution) before adding a PostgreSQL adapter. Keep MySQL as the verified baseline.

## Cleanup

```sh
docker compose down
```

`docker compose down -v` **deletes the local database volume**.
