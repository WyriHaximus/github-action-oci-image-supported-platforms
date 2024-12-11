FROM ghcr.io/wyrihaximusnet/php:8.3-zts-alpine-dev AS install-dependencies

WORKDIR /opt/app

COPY ./composer.* /opt/app/
COPY ./etc/ /opt/app/etc/
COPY ./src/ /opt/app/src/
RUN composer install --ansi --no-interaction --prefer-dist --no-progress -o --no-cache --no-scripts

FROM ghcr.io/wyrihaximusnet/php:8.3-zts-alpine AS runtime

WORKDIR /opt/app

COPY --from=install-dependencies /opt/app/ /opt/app/
