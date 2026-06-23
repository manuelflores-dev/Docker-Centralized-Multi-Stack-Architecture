# ── BASE DE PHP 7.2 (FPM) ─────────────────────────────────────────────────────
FROM php:7.2-fpm

LABEL authors="mflores"

# ── 1. REPOSITORIOS HISTÓRICOS (BLINDAJE CONTRA ERRORES 404) ──────────────────
# Esto fuerza a Docker a buscar paquetes en el archivo histórico de Debian
RUN echo "deb http://archive.debian.org/debian/ buster main" > /etc/apt/sources.list \
    && echo "deb http://archive.debian.org/debian-security buster/updates main" >> /etc/apt/sources.list \
    && apt-get update && apt-get install -y \
    git \
    unzip \
    curl \
    gnupg \
    libzip-dev \
    libicu-dev \
    libonig-dev \
    libxml2-dev \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    --no-install-recommends \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ── 2. EXTENSIONES CRÍTICAS PARA YII2 ─────────────────────────────────────────
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) \
    pdo \
    pdo_mysql \
    mysqli \
    zip \
    intl \
    mbstring \
    bcmath \
    soap \
    gd

# ── 3. COMPOSER (Versión 2 compatible con PHP 7.2) ────────────────────────────
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# ── 4. XDEBUG (Versión 2.9.8) ────────────────────────────────────────────────
RUN pecl install xdebug-2.9.8 \
    && docker-php-ext-enable xdebug

# ── 5. CONFIGURACIÓN DE PHP ───────────────────────────────────────────────────
COPY ./php.ini /usr/local/etc/php/conf.d/custom.ini

# ── 6. NODE.JS (Herramientas de compilación) ─────────────────────────────────
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs --no-install-recommends \
    && npm install -g sass terser \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ── 7. DIRECTORIO DE TRABAJO Y PERMISOS ──────────────────────────────────────
WORKDIR /var/www/html
RUN usermod -u 1000 www-data