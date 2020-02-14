################
# Stage : BASE #
################
FROM php:7.3-fpm-buster as base

WORKDIR /var/www/app

# Install dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    openssl \
    zip unzip \
    git \
    wget \
    postgresql-client \
    libpq-dev

# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Postgre PDO
RUN docker-php-ext-install pdo_pgsql

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

EXPOSE 9000

#################
# Stage : BUILD #
#################
FROM base as build

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN chown www:www .
USER www

# Install composer packages
COPY --chown=www:www composer.* ./
RUN composer install --no-scripts --no-autoloader

## Copy application sources & run composer post-install scripts
COPY --chown=www:www . .
RUN composer dump-autoload --optimize

FROM base as production
COPY --from=build /var/www/app .
CMD ["php-fpm"]
