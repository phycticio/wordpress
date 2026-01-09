FROM wordpress:6.9-fpm-alpine

RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp

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