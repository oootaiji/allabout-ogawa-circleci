FROM php:8.0-apache-buster

# instal
RUN apt update && apt install -y software-properties-common

# compoer install
COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer

# working dir
WORKDIR /var/www