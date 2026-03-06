ARG WORDPRESS_IMAGE_VERSION=latest-fpm-alpine

FROM wordpress:${WORDPRESS_IMAGE_VERSION}

RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp

RUN apk add --no-cache shadow && \
    usermod -u ${USER_ID:-1000} www-data && \
    groupmod -g ${USER_ID:-1000} www-data

RUN apk add --no-cache \
    libzip-dev \
    libpng-dev \
    zip \
    autoconf \
    g++ \
    make \
    && docker-php-ext-install zip gd \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && apk del autoconf g++ make

COPY ./php/*.ini /usr/local/etc/php/conf.d/

RUN apk add --no-cache mariadb-client msmtp

RUN chown -R www-data:www-data /var/www/html && chmod -R 775 /var/www/html