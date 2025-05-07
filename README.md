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

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

FREE TO USE

### Happy Coding :)
