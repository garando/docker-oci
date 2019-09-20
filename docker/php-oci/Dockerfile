FROM php:7.1-fpm

# base php image
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libicu-dev \
        libcurl4-openssl-dev \
        nano \
        git \
        libexif-dev \
        libxrender1 \
        libfontconfig \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) mbstring zip intl curl pdo_mysql tokenizer \
    && docker-php-ext-install exif \
    && rm -rf /var/lib/apt/lists/*

RUN curl -sS https://getcomposer.org/installer |  php -- --install-dir=/usr/local/bin --filename=composer
RUN mkdir /app
WORKDIR /app

# Get repository and install wget and vim
RUN apt-get update && apt-get install --no-install-recommends -y \
        wget \
        vim \
        git \
        unzip \
        nginx

RUN mkdir /opt/oracle \
    && cd /opt/oracle

COPY instantclient-basic-linux.x64-12.1.0.2.0.zip /opt/oracle/instantclient-basic.zip
COPY instantclient-sdk-linux.x64-12.1.0.2.0.zip /opt/oracle/instantclient-sdk.zip

RUN unzip /opt/oracle/instantclient-basic.zip -d /opt/oracle \
    && unzip /opt/oracle/instantclient-sdk.zip -d /opt/oracle \
    && ls -lah /opt/oracle \
    && ln -s /opt/oracle/instantclient_12_1/libclntsh.so.12.1 /opt/oracle/instantclient_12_1/libclntsh.so \
    && ln -s /opt/oracle/instantclient_12_1/libclntshcore.so.12.1 /opt/oracle/instantclient_12_1/libclntshcore.so \
    && ln -s /opt/oracle/instantclient_12_1/libocci.so.12.1 /opt/oracle/instantclient_12_1/libocci.so \
    && rm -rf /opt/oracle/*.zip

# Install PHP extensions deps
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        zlib1g-dev \
        libicu-dev \
        g++ \
        unixodbc-dev \
        libxml2-dev \
        libaio-dev \
        libmemcached-dev \
        freetds-dev \
	libssl-dev \
	openssl

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8 \
    && docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient_12_1,12.1 \
    && docker-php-ext-configure pdo_dblib --with-libdir=/lib/x86_64-linux-gnu \
    && docker-php-ext-install \
            iconv \
            mbstring \
            intl \
            mcrypt \
            gd \
            pdo_oci \
            pdo_dblib \
            zip \
            pcntl \
            ftp \
    && docker-php-ext-enable \
            oci8 \
            opcache



WORKDIR /app
RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_connect_back=on" >> /usr/local/etc/php/conf.d/xdebug.ini

RUN usermod -u 1000 www-data
COPY uploads.ini /usr/local/etc/php/conf.d
COPY disable_short_open_tag.ini /usr/local/etc/php/conf.d

ENV LD_LIBRARY_PATH /opt/oracle/instantclient_12_1/
