# Sử dụng image PHP 8.3 với Apache
FROM php:8.3-apache

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
    nodejs \
    npm \
    && docker-php-ext-install pdo pdo_mysql mbstring zip exif pcntl gd

# Cài đặt Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Tạo thư mục Laravel app
WORKDIR /var/www/html

# Bật mod_rewrite cho Apache
RUN a2enmod rewrite

# Thiết lập cấu hình Apache cho Laravel
RUN echo '<Directory /var/www/html>' \
         '\n\tOptions Indexes FollowSymLinks' \
         '\n\tAllowOverride All' \
         '\n\tRequire all granted' \
         '\n</Directory>' \
         > /etc/apache2/conf-available/laravel.conf \
    && a2enconf laravel

# Cổng mặc định
EXPOSE 80
