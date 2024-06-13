# dev-kit-containers

- [Install Podman](https://podman.io/docs/installation)
- podman network create dev-kit


## Databases
```bash
podman run -d \
    --name dev-kit-postgresql-16 \
    -e POSTGRES_USER=root \
    -e POSTGRES_PASSWORD=root \
    -e POSTGRES_DB=db \
    -v ./data/postgresql-16:/var/lib/postgresql/data \
    -p 127.0.0.1:5432:5432 \
    --network=dev-kit \
    postgres:16-alpine

podman run -d \
    --name dev-kit-adminer \
      -e ADMINER_DEFAULT_DB_DRIVER=pgsql \
      -e ADMINER_DEFAULT_SERVER=dev-kit-postgresql-16 \
      -e ADMINER_DEFAULT_DB_HOST=postgres \
      -e ADMINER_DEFAULT_DB_NAME=dev.atendata.xyz \
      -p 127.0.0.1:8080:8080 \
    --network=dev-kit \
    adminer:latest
```

## Tools
```bash
podman run -d \
    --name dev-kit-mail \
      -e TZ=Europe/Andorra \
      -p 127.0.0.1:8025:8025 \
      -p 127.0.0.1:1025:1025 \
    --network=dev-kit \
    axllent/mailpit:latest
```


## PHP
```bash
podman build --platform linux/arm64,linux/amd64 -f php8-1/Podmanfile --manifest pcasaspere/dev-kit-containers:php81-laravel
podman push pcasaspere/dev-kit-containers:php81-laravel
podman run -d \
    --name dev-kit-php1-2 \
    -v $HOME/code:/app \
    -p 127.0.0.1:8000:8000 \
    --network=dev-kit \
    pcasaspere/dev-kit-containers:php81-laravel

podman exec dev-kit-php1-2 php /app/repo/artisan serve --host=0.0.0.0 --port=8000

```

```bash
podman build --platform linux/arm64,linux/amd64 -f php8-2/Podmanfile --manifest pcasaspere/dev-kit-containers:php82-laravel
podman push pcasaspere/dev-kit-containers:php82-laravel

podman run -d \
    --name dev-kit-php8-2 \
    -v $HOME/code:/app \
    -p 127.0.0.1:8000:8000 \
    --network=dev-kit \
    pcasaspere/dev-kit-containers:php82-laravel

podman exec dev-kit-php8-2 php /app/repo/artisan serve --host=0.0.0.0 --port=8000
```