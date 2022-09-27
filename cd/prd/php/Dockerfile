FROM php:8.0-apache-buster

ARG APP_ENV
ARG APP_PATH

WORKDIR /

# instal tool
RUN set -x \
  && apt-get update \
  && apt-get install -y libzip-dev git software-properties-common \
  && docker-php-ext-install pdo_mysql \
  && docker-php-ext-install zip

# install redis
RUN set -x \
  && pecl install redis \
  && docker-php-ext-enable redis

# install compoer
COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer
RUN composer --version

# install node
RUN set -x \
  && apt-get install -y nodejs \
  && nodejs -v

## --------------環境別--------------
# copy app
COPY . ${APP_PATH}


# copy config
COPY ./cd/${APP_ENV}/php/apache2/apache2.conf /etc/apache2/apache2.conf
COPY ./cd/${APP_ENV}/php/apache2/sites-available/ /etc/apache2/sites-available/
COPY ./cd/${APP_ENV}/php/apache2/mods-available/ /etc/apache2/mods-available/
COPY ./cd/${APP_ENV}/php/apache2/conf-available/ /etc/apache2/conf-available/
COPY ./cd/${APP_ENV}/php/php.ini /usr/local/etc/php/php.ini
## --------------環境別--------------

# create apache config symboliclink
RUN set -x \
  && a2enmod rewrite headers \
  && a2ensite app 000-default

# healthcheck
RUN echo '<?php echo "ok"; ?>' > /var/www/html/index.php

# declare container publish port
EXPOSE 80