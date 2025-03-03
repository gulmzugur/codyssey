FROM php:8.2-fpm

# Sistem güncellemesi ve temel bağımlılıklar
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    curl \
    libzip-dev \
    zlib1g-dev \
    libicu-dev \
    libonig-dev \
    libbrotli-dev \
    autoconf \
    build-essential \
    libbz2-dev \
    libmemcached-dev \
    gettext \
    zip \
    unzip \
    libssl-dev \
    libnghttp2-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libmagickwand-dev \
    redis-tools \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# PHP uzantıları
RUN docker-php-ext-install \
    bz2 \
    exif \
    fileinfo \
    ftp \
    gettext \
    intl \
    pdo_mysql \
    sockets \
    zip \
    pcntl \
    opcache \
    bcmath \
    ctype \
    mbstring \
    xml \
    gd \
    && pecl install redis \
    && pecl install imagick \
    && pecl install memcached \
    && docker-php-ext-enable redis imagick memcached

# Composer kurulumu (optimizasyon için paralel indirme)
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Çalışma dizini
WORKDIR /var/www

# Özel yapılandırma dosyaları
COPY ./dockify/config/php/php.ini /usr/local/etc/php/php.ini
COPY ./dockify/config/php/php-fpm.conf /usr/local/etc/php-fpm.d/php-fpm-custom.conf