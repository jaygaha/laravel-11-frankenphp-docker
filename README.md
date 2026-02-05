# FrankenPHP and Laravel Octane with Docker + Laravel 11 & Laravel 12

This repo is a docker boilerplate to use for Laravel projects. Containers included in this docker:

1. [Laravel 11 & 12](https://laravel.com/docs/)
2. [FrankenPHP](https://frankenphp.dev/docs/docker/)
3. MySQL
4. Redis
5. Supervisor
6. [Octane](https://laravel.com/docs/octane)
7. Minio for S3
8. MailPit

The purpose of this repo is to run [Laravel 11 & Laravel 12](https://laravel.com/docs/) in a Docker container using [Octane](https://laravel.com/docs/octane) and [FrankenPHP](https://frankenphp.dev/docs/docker/).

## Installation

Use the package manager [git](https://git-scm.com/downloads) to install Docker boilerplate.

```bash
# setup project locally
$ git clone https://github.com/jaygaha/laravel-11-frankenphp-docker.git
# Navigate to project directory:
$ cd laravel-11-frankenphp-docker
```

## Application Setup

Copy the .env.example file to .env:

```bash
# Linux
$ cp .env.example .env
# OR
# Windows
$ copy .env.example .env
```

Edit the `.env` file to configure your application settings. At a minimum, you should set the following variables:

- `APP_NAME`: The name of your application.
- `APP_ENV`: The environment your application is running in (e.g., local, production).
- `APP_KEY`: The application key (will be generated in the next step).
- `APP_DEBUG`: Set to `true` for debugging.
- `APP_URL`: The URL of your application.
- `DB_CONNECTION`: The database connection (e.g., mysql).
- `DB_HOST`: The database host.
- `DB_PORT`: The database port.
- `DB_DATABASE`: The database name.
- `DB_USERNAME`: The database username.
- `DB_PASSWORD`: The database password.

**Edit docker related setting according to your preferences.**

Run composer to install the required packages:

```bash
# install required packages
$ composer install
```

Generate a new application key:

```bash
# app key setup
$ php artisan key:generate
```

## Usage

Build the Docker images:

```bash
# build docker images
$ docker compose build
```

Run the containers:

```bash
# Run containers
$ docker compose up -d
```

To stop the containers, run:

```bash
# Stop containers
$ docker compose down
```

To view the logs of a specific container, run:

```bash
# View logs
$ docker compose logs <container_name>
```

**If you are using podman replace `docker` with `podman`**

To access the application, open your browser and navigate to the URL specified in the `APP_URL` variable in your `.env` file.


## Upgrading

Upgrading To 12.0 From 11.x

```bash
$ composer update
```

## Production Deployment

This repository includes a production-ready Docker configuration with security hardening and performance optimizations.

### Quick Start (Production)

```bash
# 1. Create production env file
cp .env.production.example .env.production

# 2. Generate and set APP_KEY in .env.production
php artisan key:generate --show

# 3. Set DB_PASSWORD in .env.production (required)

# 4. Build and run
docker compose -f docker-compose.prod.yml build
docker compose -f docker-compose.prod.yml up -d

# 5. Check status
docker compose -f docker-compose.prod.yml ps
```

### Production Files

| File | Description |
|------|-------------|
| `.docker/php/Dockerfile.prod` | Multi-stage production Dockerfile with FrankenPHP |
| `.docker/php/php.prod.ini` | Hardened PHP configuration with OPcache |
| `.docker/etc/supervisor.d/supervisord.prod.conf` | Production supervisor with Octane + 2 queue workers |
| `docker-compose.prod.yml` | Production compose with resource limits and health checks |
| `.env.production.example` | Production environment template |
| `.env.production` | Your production environment config (create from example) |
| `.dockerignore` | Excludes dev files from production image |

### Production Setup

1. Copy the production environment file:

```bash
cp .env.production.example .env.production
```

2. Configure production environment variables in `.env.production`:

```bash
# Generate APP_KEY
php artisan key:generate --show
# Copy the output and set it in .env.production
```

Required settings:
   - `APP_KEY`: Application encryption key (required)
   - `DB_PASSWORD`: Set a strong database password
   - `APP_URL`: Your production domain (e.g., `https://your-domain.com`)
   - `SESSION_DOMAIN`: Your domain for cookies (e.g., `your-domain.com`)
   - Configure mail and S3 credentials as needed

3. Build the production image:

```bash
docker compose -f docker-compose.prod.yml build
```

4. Run production containers:

```bash
docker compose -f docker-compose.prod.yml up -d
```

5. Verify all containers are healthy:

```bash
docker compose -f docker-compose.prod.yml ps
```

### Apple Silicon / ARM64 Notes

The production configuration includes `platform: linux/amd64` for the web container. This is required because FrankenPHP has compatibility issues on ARM64 architecture that cause segmentation faults. The x86_64 emulation via Rosetta 2 works reliably.

If deploying to an AMD64/x86_64 server, you can optionally remove the `platform` line for native performance.

### Troubleshooting Production

**Container keeps restarting with exit code 139:**
- This is a SIGSEGV (segmentation fault), typically caused by FrankenPHP on ARM64
- Ensure `platform: linux/amd64` is set in docker-compose.prod.yml

**"No application encryption key" error:**
- Ensure `APP_KEY` is set in `.env.production`
- The `.env.production` file must be mounted to the container (configured in docker-compose.prod.yml)

**MySQL fails to start:**
- Use the official `mysql` image instead of `mysql/mysql-server`
- Ensure `DB_PASSWORD` is set (empty passwords are not allowed)

**Dev dependencies not found during build:**
- The `composer.json` includes a `dont-discover` list for dev-only packages
- Packages like `laravel/sail`, `nunomaduro/collision`, and `spatie/laravel-ignition` are excluded from auto-discovery

### Production Security Features

- **Multi-stage build**: Smaller image without build dependencies
- **Non-root workers**: Queue workers run as `appuser` (UID 1000)
- **No debug tools**: Xdebug and dev dependencies excluded
- **Hardened PHP**: `display_errors=off`, `expose_php=off`
- **OPcache enabled**: JIT compilation for performance
- **FrankenPHP + Octane**: High-performance request handling with 4 workers
- **Resource limits**: CPU and memory constraints prevent runaway processes
- **Health checks**: Container-level health monitoring with `/up` endpoint
- **Secure sessions**: HTTP-only, secure cookies enabled
- **Log rotation**: JSON file logging with size limits
- **Dev packages excluded**: Auto-discovery disabled for dev-only service providers

### Production Checklist

Before deploying to production, ensure:

- [ ] Strong, unique passwords for database and Redis
- [ ] `APP_DEBUG=false` and `APP_ENV=production`
- [ ] HTTPS configured (use a reverse proxy like Nginx/Traefik)
- [ ] Secrets managed externally (Docker secrets, Vault, etc.)
- [ ] Database backups configured
- [ ] Monitoring and alerting set up
- [ ] Log aggregation configured (ELK, CloudWatch, etc.)

### Differences: Local vs Production

| Aspect | Local (`Dockerfile.local`) | Production (`Dockerfile.prod`) |
|--------|---------------------------|-------------------------------|
| Base image | `dunglas/frankenphp:1.1-builder-php8.2` | `dunglas/frankenphp:latest-php8.3` |
| Platform | Native | `linux/amd64` (for ARM64 compatibility) |
| Xdebug | Installed | Not included |
| Composer | With dev deps | `--no-dev` |
| display_errors | On | Off |
| OPcache | Disabled | Enabled with JIT |
| User | Root | appuser (1000) for workers |
| Volumes | Source mounted | Image contains code + .env mounted |
| Resource limits | None | CPU/Memory constrained |
| MySQL image | `mysql/mysql-server` | `mysql` (official) |
| Queue workers | 1 worker | 2 workers |

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

FREE TO USE

### Happy Coding :)
