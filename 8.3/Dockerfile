# Sử dụng image PHP 8.3 với Apache
FROM php:8.3-apache

ENV USER=www
ENV GROUP=www
ENV UID=1000
ENV GID=1000
ENV HOME=/home/www
ENV SHELL=/bin/bash

# Cài đặt các tiện ích hệ thống
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    zip \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    libpq-dev \
    libjpeg-dev \
    libfreetype6-dev \
    && docker-php-ext-install pdo pdo_mysql mbstring zip exif pcntl gd
# Bật mod_rewrite cho Apache
RUN a2enmod rewrite
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y nodejs \
    && ln -s /usr/bin/npm /usr/local/bin/npm

# Cài đặt Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
RUN composer global require laravel/installer

RUN echo '<Directory /var/www/html>' \
         '\n\tOptions Indexes FollowSymLinks' \
         '\n\tAllowOverride All' \
         '\n\tRequire all granted' \
         '\n</Directory>' \
         > /etc/apache2/conf-available/laravel.conf \
    && a2enconf laravel

RUN groupadd -g ${GID} ${GROUP} \
    && useradd --system --shell /bin/bash --uid ${UID} --gid ${GROUP}  --create-home --home-dir /home/${USER} ${USER} \
    && adduser ${USER} www-data \
    && usermod -aG sudo ${USER} \
    && echo "www ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN chown -R ${USER}:${GROUP} /home/${USER} /var/www/html
USER ${USER}
# Tạo thư mục Laravel app
RUN touch ~/log
ENV PATH="$PATH:/root/.composer/vendor/bin"
WORKDIR /var/www/html

# Cổng mặc định
EXPOSE 80
