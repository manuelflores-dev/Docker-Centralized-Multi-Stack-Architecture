# ── BASE DE PHP 7.3 (FPM) ─────────────────────────────────────────────────────
# Clonamos exactamente la versión de producción encontrada en Zegucom
FROM php:7.3-fpm

LABEL authors="mflores"

# ── 1. DEPENDENCIAS DEL SISTEMA ───────────────────────────────────────────────
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    curl \
    gnupg \
    libzip-dev \
    libicu-dev \
    libonig-dev \
    libxml2-dev \
    --no-install-recommends \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ── 2. EXTENSIONES CRÍTICAS PARA LA API ───────────────────────────────────────
RUN docker-php-ext-install -j$(nproc) \
    pdo \
    pdo_mysql \
    mysqli \
    zip \
    intl \
    mbstring \
    bcmath \
    soap

# ── 3. COMPOSER (Versión 2 compatible con PHP 7.3) ────────────────────────────
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# ── 4. XDEBUG (Para PHP 7.3 la versión 2.9.8 mantiene compatibilidad perfecta)
RUN pecl install xdebug-2.9.8 \
    && docker-php-ext-enable xdebug

# ── 5. CONFIGURACIÓN DE PHP ───────────────────────────────────────────────────
# Busca el php.ini a su lado en la carpeta apirest y lo inyecta al contenedor
COPY ./php.ini /usr/local/etc/php/conf.d/custom.ini

# ── 6. NODE.JS (Herramientas de compilación globales) ─────────────────────────
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs --no-install-recommends \
    && npm install -g sass terser \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# ── 7. DIRECTORIO DE TRABAJO ──────────────────────────────────────────────────
WORKDIR /var/www/html

# Evitamos problemas de permisos con Linux mapeando tu usuario local
RUN usermod -u 1000 www-data