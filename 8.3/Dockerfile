FROM php:8.3-apache

#== System Environments
ENV USER=www
ENV GROUP=www
ENV UID=1000
ENV GID=1000
ENV HOME=/home/www
ENV SHELL=/bin/bash

#== Default Environments
ENV APP_ROOT=/var/www/html
ENV WEB_ROOT=$APP_ROOT
ENV PHP_CUSTOM_INI=$APP_ROOT/php.ini
ENV PHP_EXT_DIR=/usr/local/etc/php/conf.d

ENV PHP_MEMORY_LIMIT=4096M
ENV PHP_MAX_EXECUTION_TIME=600
ENV PHP_MAX_INPUT_TIME=600
ENV PHP_MAX_INPUT_VARS=3000
ENV PHP_UPLOAD_MAX_FILESIZE=64M
ENV PHP_POST_MAX_SIZE=64M
ENV PHP_CLEAR_ENV=false
ENV SERVER_NAME=default
ENV NVM_DIR=~/.nvm
ENV NODE_VERSION_24=24.5.0
ENV NODE_VERSION_22=22.18.0
ENV NODE_VERSION_20=20.19.4

ENV CODES_PORT=8080
ENV CODES_WORKING_DIR=$APP_ROOT
ENV CODES_USER_DATA_DIR=$APP_ROOT/.vscode
ENV CODES_ENABLE=yes

RUN apt-get update && apt-get install -y \
    ca-certificates \
    libpng-dev \
    libjpeg-dev \
    libjpeg62-turbo-dev \
    libwebp-dev \
    libfreetype6-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    git \
    curl \
    wget \
    libzip-dev \
    libssl-dev \
    libcurl4-openssl-dev \
    libbz2-dev \
    sudo \
    && rm -rf /var/lib/apt/lists/*
# Configure the gd library
RUN docker-php-ext-configure \
  gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ --with-webp=/usr/include/

RUN docker-php-ext-configure \
  opcache --enable-opcache
# Install required PHP extensions
RUN docker-php-ext-install -j$(nproc) \
  bcmath \
  bz2 \
  calendar \
  exif \
  gd \
  gettext \
  intl \
  mysqli \
  opcache

RUN docker-php-ext-install -j$(nproc) \
  pdo_mysql \
  shmop \
  soap \
  sockets \
  sysvmsg \
  sysvsem \
  sysvshm \
  zip \
  pcntl \
  mbstring \
  xml \
  ctype \
  fileinfo \
  curl

RUN a2enmod rewrite

RUN groupadd -g ${GID} ${GROUP} \
    && useradd --system --shell /bin/bash --uid ${UID} --gid ${GROUP}  --create-home --home-dir /home/${USER} ${USER} \
    && adduser ${USER} www-data \
    && usermod -aG sudo ${USER} \
    && echo "www ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN mkdir -p ${APP_ROOT} \
    && chown -R ${UID}:${GID} ${APP_ROOT}

# #== Install Codeserver [AMD Chip]
ARG CODESERVER_VERSION="4.99.4"
RUN wget -O /tmp/code-server_${CODESERVER_VERSION}_amd64.deb https://github.com/coder/code-server/releases/download/v${CODESERVER_VERSION}/code-server_${CODESERVER_VERSION}_amd64.deb
RUN dpkg -i /tmp/code-server_${CODESERVER_VERSION}_amd64.deb \
    && rm /tmp/code-server_${CODESERVER_VERSION}_amd64.deb

RUN mkdir -p $NVM_DIR
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash \
    && . "$NVM_DIR/nvm.sh" \
    && nvm install $NODE_VERSION_20 \
    && nvm install $NODE_VERSION_22 \
    && nvm install $NODE_VERSION_24 \
    && nvm alias default $NODE_VERSION_24 \
    && nvm use default

#== Install Composer & Laravel command
RUN curl -fsSL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=2.7.7
RUN composer global require laravel/installer
RUN cp ~/.composer/vendor/bin/laravel /usr/local/bin/laravel

WORKDIR ${APP_ROOT}

USER ${USER}

RUN bash ~/.bashrc
#== Start apache.
COPY templates /templates
COPY scripts /scripts

RUN sudo rm -rf ${APP_ROOT} && sudo sudo mkdir -p ${APP_ROOT}

CMD ["sudo", "-E", "/bin/bash", "/scripts/apache-start.sh" ]