FROM php:8.0-apache-buster

# instal
RUN set -x \
  && apt update && apt install -y software-properties-common

# compoer install
COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer
RUN composer --version

# node install
RUN apt install -y nodejs && nodejs -v

# working dir
WORKDIR /var/www