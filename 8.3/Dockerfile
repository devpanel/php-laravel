FROM php:8.3-apache

ARG POLYVER_CUSTOMER_KEY

#== System Environments
ENV USER www
ENV GROUP www
ENV UID 1000
ENV GID 1000
ENV HOME /home/www
ENV SHELL /bin/bash

#== Default Environments
ENV APP_ROOT /var/www/html
ENV WEB_ROOT $APP_ROOT
ENV PHP_CUSTOM_INI $APP_ROOT/php.ini
ENV PHP_EXT_DIR /usr/local/etc/php/conf.d

ENV PHP_MEMORY_LIMIT 4096M
ENV PHP_MAX_EXECUTION_TIME 600
ENV PHP_MAX_INPUT_TIME 600
ENV PHP_MAX_INPUT_VARS 3000
ENV PHP_UPLOAD_MAX_FILESIZE 64M
ENV PHP_POST_MAX_SIZE 64M
ENV PHP_CLEAR_ENV false

ENV SERVER_NAME default

ENV CODES_PORT 8080
ENV CODES_WORKING_DIR $APP_ROOT
ENV CODES_USER_DATA_DIR $APP_ROOT/.vscode
ENV CODES_ENABLE yes

ENV GIT_BRANCH main
ENV PHP_EXTENSIONS bcmath bz2 calendar exif gd gettext intl mysqli opcache pdo_mysql redis soap sockets sysvmsg sysvsem sysvshm xsl zip pcntl

RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    git \
    curl \
    libzip-dev \
    libssl-dev \
    libcurl4-openssl-dev \
    libbz2-dev \
    && docker-php-ext-install pdo_mysql mbstring tokenizer xml ctype json openssl fileinfo bcmath curl zip

RUN a2enmod rewrite

RUN groupadd -g ${GID} ${GROUP} \
    && useradd --system --shell /bin/bash --uid ${UID} --gid ${GROUP}  --create-home --home-dir /home/${USER} ${USER} \
    && adduser ${USER} www-data \
    && apt install -y sudo \
    && usermod -aG sudo ${USER} \
    && echo "www    ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN mkdir -p ${APP_ROOT} \
    && chown -R ${UID}:${GID} ${APP_ROOT}

#== Install Codeserver [AMD Chip]
ARG CODESERVER_VERSION="4.99.4"
RUN wget -O /tmp/code-server_${CODESERVER_VERSION}_amd64.deb https://github.com/coder/code-server/releases/download/v${CODESERVER_VERSION}/code-server_${CODESERVER_VERSION}_amd64.deb
RUN dpkg -i /tmp/code-server_${CODESERVER_VERSION}_amd64.deb \
    && rm /tmp/code-server_${CODESERVER_VERSION}_amd64.deb

#== Install Composer
RUN curl -fsSL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=2.7.7

WORKDIR ${APP_ROOT}

USER ${USER}

RUN bash ~/.bashrc
#== Start apache.
COPY templates /templates
COPY scripts /scripts

RUN sudo rm -rf ${APP_ROOT} && sudo sudo mkdir -p ${APP_ROOT}

CMD ["sudo", "-E", "/bin/bash", "/scripts/apache-start.sh" ]