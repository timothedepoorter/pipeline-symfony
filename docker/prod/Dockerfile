FROM composer:2 AS vendor

WORKDIR /symfony

COPY symfony/composer.json symfony/composer.lock symfony/symfony.lock ./

RUN composer install --no-dev --no-scripts --prefer-dist --optimize-autoloader

FROM php:8.4-fpm-alpine3.24 AS symfony

RUN apk add --no-cache libpq-dev \
    && docker-php-ext-install pdo pdo_pgsql opcache

WORKDIR /var/www/symfony

COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer
COPY --from=vendor /symfony/vendor ./vendor
COPY symfony/ .

RUN composer dump-autoload --no-dev --optimize
RUN php bin/console cache:clear --env=prod --no-debug || true
RUN mkdir -p var/cache var/log
RUN chown -R www-data:www-data var

USER www-data

EXPOSE 9000
