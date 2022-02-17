FROM php:8.1-fpm

RUN set -eu; \
    apt-get update; \
    apt-get install -y \
        curl \
        libonig-dev \
        libxml2-dev \
        libpq-dev \
        zip \
        unzip; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*

RUN set -eu; \
    pecl install redis-5.3.4; \
    docker-php-ext-enable redis; \
    docker-php-ext-install \
        opcache \
        pdo \
        pdo_pgsql \
        mbstring \
        pcntl \
        bcmath

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www

COPY artisan artisan
COPY tests tests
COPY public public
COPY resources resources
COPY storage storage

COPY composer.json composer.json
COPY composer.lock composer.lock
RUN composer install \
  --prefer-dist \
  --no-scripts \
  --no-dev \
  --classmap-authoritative \
  --no-autoloader

COPY bootstrap bootstrap
COPY database database
COPY config config
COPY app app
COPY routes routes

RUN set -eu; \
    chown -R www-data:www-data bootstrap/cache; \
    chown -R www-data:www-data storage; \
    composer dump-autoload; \
    php artisan clear-compiled

CMD [ "php-fpm" ]
